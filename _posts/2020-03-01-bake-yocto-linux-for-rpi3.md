---
title: "Bake yocto linux for RaspberryPi 3"
date: 2020-03-01
categories: [Engineering,Yocto]
tags: [yocto,linux,raspberrypi3]
---

# Useful Links

[Quick build](https://www.yoctoproject.org/docs/3.0/brief-yoctoprojectqs/brief-yoctoprojectqs.html) 

# Install dependencies

~~~ bash
$ sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib \
    build-essential chrpath socat cpio python python3 python3-pip python3-pexpect \
    xz-utils debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev \
    pylint3 xterm
~~~

For details see: [Required packages for the build host](https://www.yoctoproject.org/docs/3.0/ref-manual/ref-manual.html#required-packages-for-the-build-host).
 
# Download
 
To build raspberry-pi3 image we need to get _poky_ and two additional layers: _meta-raspberrypi_ and _meta-openembedded_:
 
~~~ bash
$ git clone git://git.yoctoproject.org/poky yocto
$ cd yocto
$ git checkout origin/zeus -b zeus

$ git clone git://git.openembedded.org/meta-openembedded
$ cd meta-openembedded
$ git checkout origin/zeus -b zeus
$ cd -

$ git clone git://git.yoctoproject.org/meta-raspberrypi
$ cd meta-raspberrypi
$ git checkout origin/zeus -b zeus
$ cd -
~~~

We checkout to last stable release branch - _zeus_. To get all release branches follow next link - [Releases](https://wiki.yoctoproject.org/wiki/Releases).

# Configure

~~~ bash
$ source oe-init-build-env build-rpi3
~~~

Configure layers:
~~~ bash
$ vim conf/bblayers.conf

BBLAYERS ?= " \
  /home/denis/workspace/source/yocto/meta \
  /home/denis/workspace/source/yocto/meta-poky \
  /home/denis/workspace/source/yocto/meta-yocto-bsp \
  /home/denis/workspace/source/yocto/meta-openembedded/meta-oe \
  /home/denis/workspace/source/yocto/meta-openembedded/meta-multimedia \
  /home/denis/workspace/source/yocto/meta-openembedded/meta-networking \
  /home/denis/workspace/source/yocto/meta-openembedded/meta-python \
  /home/denis/workspace/source/yocto/meta-raspberrypi \
  "
~~~

Configure local configuration:
~~~ bash
$ vim conf/local.conf
~~~

~~~
# Specify target machine type (aarch64 architecture)
MACHINE ??= "raspberrypi3-64"

# Specify GPU memory size
GPU_MEM_256 = "128"
GPU_MEM_512 = "196"
GPU_MEM_1024 = "396"

# Specify image filesystem types
IMAGE_FSTYPES = "tar.bz2 ext4 rpi-sdimg"

# Specify filesystem type of SD image
SDIMG_ROOTFS_TYPE = "ext4"

# Replace systemv by systemd
DISTRO_FEATURES_append = " systemd"
VIRTUAL-RUNTIME_init_manager = "systemd"
DISTRO_FEATURES_BACKFILL_CONSIDERED = "sysvinit"
VIRTUAL-RUNTIME_initscripts = ""

# --------------------------------
# Enable high-level image features  
# --------------------------------

# Enable: openssh
EXTRA_IMAGE_FEATURES_append = " ssh-server-openssh"

# Enable: development tools (optional)
EXTRA_IMAGE_FEATURES_append = " tools-debug"

# --------------------------------
# Enable specific image features
# --------------------------------

# Include: full command line environment (optional)
CORE_IMAGE_EXTRA_INSTALL_append = " packagegroup-core-full-cmdline"

# Include: connman as network manager
CORE_IMAGE_EXTRA_INSTALL_append = " connman connman-client"

# Include: e2fs tools
CORE_IMAGE_EXTRA_INSTALL_append = " e2fsprogs e2fsprogs-e2fsck e2fsprogs-mke2fs e2fsprogs-tune2fs e2fsprogs-badblocks e2fsprogs-resize2fs"

# Include: C++ runtime library
CORE_IMAGE_EXTRA_INSTALL_append = " libstdc++"
~~~

# Build and flash image

~~~ bash
$ bitbake core-image-base
~~~

Inject SD card to PC and determine which name device has:
~~~ bash
$ lsblk
...
sdd      8:48   1    58G  0 disk 
├─sdd1   8:49   1    40M  0 part /media/denis/raspberrypi
└─sdd2   8:50   1  57,9G  0 part /media/denis/116dc20e-1fbb-4d1c-9951-f2a8ef902f11
...
~~~

For my case it has _sdd_ name and already consists of two parts (sdd1 and sdd2). Before we continue we need to unmount:
~~~ bash
$ sudo umount /dev/sdd1
$ sudo umount /dev/sdd2
~~~

Now we can flash image to SD card. To do it in efficient way we need to obtain block size:
~~~ bash
$ sudo fdisk -l /dev/sdd
...
Units: sectors of 1 * 512 = 512 bytes
...
~~~
As we cn see the block size is _512 bytes_. Now flash image to SD card:
~~~ bash
$ sudo dd if=tmp/deploy/images/raspberrypi3-64/core-image-base-raspberrypi3-64.rpi-sdimg of=/dev/sdd bs=512
$ sync
~~~

After that you can inject SD card to device and power on it.

# Optional: Expand filesystem on SD card to take whole space

Inject SD card and 
Login to console on device and enter next commands:
~~~ bash
# mount
/dev/mmcblk0p2 on / type ext4 ...
...
# fdisk -u /dev/mmcblk0
> p # print all partitions (remember the start of the second partition)
> d # delete partition
> 2 # select the second partition to delete
> n # create new partition
> p # specify new partition as primary
> 2 # specify parition number
> ~ # specify the start of partition 
> ~ # specify the end of parition
> ~ # reject to remove the signature
> p # print all partitions
> w # write changes
> q

# resize2fs /dev/mmcblk0p2
~~~

# Optional: Build SDK image

To build SDK image we need to specifying _SDKMACHINE_ to _x86_64_ which means that produced gcc-crosssdk toolchain will run on _x86_64_ machine type build host and produce code for _MACHINE_ type target.
~~~ bash
$ vim conf/local.conf
...
SDKMACHINE ?= "x86_64"
...
~~~
Now we can build SDK toolchain:
~~~ bash
$ bitbake core-image-base -c populate_sdk_ext
$ cd tmp/deploy/sdk
$ chmod +x poky-glibc-x86_64-core-image-base-aarch64-raspberrypi3-64-toolchain-ext-2.7.1.sh
$ ./poky-glibc-x86_64-core-image-base-aarch64-raspberrypi3-64-toolchain-ext-2.7.1.sh
~~~
