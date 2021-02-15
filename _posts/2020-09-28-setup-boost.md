---
title: "Set-Up Boost library"
date: 2020-09-06
categories: [Development,Bootstrap]
tags: [boost,cmake]
---

1. Download and unpack:
```bash
$ wget https://dl.bintray.com/boostorg/release/1.74.0/source/boost_1_74_0.tar.gz
$ tar -xf boost_1_74_0.tar.gz && rm boost_1_74_0.tar.gz
$ cd boost_1_74_0
```
2. Build and install.
With specific place:
```bash
$ ./bootstrap.sh --prefix=${HOME}/opt/boost
$ ./b2 install
```
or to the default place:
```bash
$ ./bootstrap.sh
$ sudo ./b2 install
```
3. Use by [CMake](https://cmake.org/cmake/help/latest/module/FindBoost.html)
