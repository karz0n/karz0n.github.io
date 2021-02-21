---
title: "Install C++ development environment on Ubuntu"
date: 2019-10-05
categories: [Development,Configure]
tags: [gcc, ubuntu]
---

# Installing development environment

``` bash
$ sudo apt update
$ sudo apt install -y build-essential
```

Check version:
``` bash
$ gcc --version
```

Output:
```
gcc (Ubuntu 9.3.0-10ubuntu2) 9.3.0
Copyright (C) 2019 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

**Note:** From Ubuntu 20 the embedded GCC has version 9.3.0.

# Installing multiple GCC versions

Add additional PPA to system:
``` bash
$ sudo apt install software-properties-common
$ sudo add-apt-repository ppa:ubuntu-toolchain-r/test
```
Install the desired GCC/G++ (e.g. the latest one):
``` bash
$ sudo apt install gcc-10 g++-10
```
Update alternatives for convenient switching:
``` bash
$ sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 90 --slave /usr/bin/g++ g++ /usr/bin/g++-9 --slave /usr/bin/gcov gcov /usr/bin/gcov-9
$ sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-10 10 --slave /usr/bin/g++ g++ /usr/bin/g++-10 --slave /usr/bin/gcov gcov /usr/bin/gcov-10
```
Configure alternatives (by default the highest priority alternative used):
``` bash
$ sudo update-alternatives --config gcc
```
Output:
```
There are 2 choices for the alternative gcc (providing /usr/bin/gcc).

  Selection    Path             Priority   Status
------------------------------------------------------------
* 0            /usr/bin/gcc-9    90        auto mode
  1            /usr/bin/gcc-10   10        manual mode
  2            /usr/bin/gcc-9    90        manual mode
```

# Installing CLang

```bash
$ sudo wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add -
$ sudo apt-get update
$ sudo apt-get install -y clang-11 lldb-11 lld-11 clangd-11
```

Using clang by cmake:
```
$ cmake -S . -B build \
-DCMAKE_C_COMPILER=/usr/bin/clang-11 \
-DCMAKE_CXX_COMPILER=/usr/bin/clang++-11
```