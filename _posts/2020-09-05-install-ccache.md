---
title: "Install ccache"
date: 2020-09-05
categories: [Development,Tools]
tags: [cpp,ccache]
---

## Install

```bash
$ sudo apt install -y ccache
$ sudo /usr/sbin/update-ccache-symlinks
$ echo 'export PATH="/usr/lib/ccache:$PATH"' | tee -a ~/.bashrc
$ source ~/.bashrc
```

Your path should looks like:
```
/usr/lib/ccache:...
```

## Configure

To enable unlimited cache size:
```bash
$ ccache -F 0
$ ccache -M 0
```

To show cache statistics:
```bash
$ ccache -s
```

To empty cache:
```bash
$ ccache -C -z
```

## Use

Using _ccache_ in cmake project required append several modules: [EnableCcache.cmake](https://github.com/karz0n/warehouse/blob/725079707ac816d8760374c6d48e2acb183d4693/cmake/modules/EnableCcache.cmake), [launch-c.sh.in](https://github.com/karz0n/warehouse/blob/725079707ac816d8760374c6d48e2acb183d4693/cmake/modules/launch-c.sh.in) and [launch-cxx.sh.in](https://github.com/karz0n/warehouse/blob/725079707ac816d8760374c6d48e2acb183d4693/cmake/modules/launch-cxx.sh.in). Place these files into project at the modules location and include _EnableCcache.cmake_:
```bash
include(EnableCcache)
```
