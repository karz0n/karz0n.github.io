---
title: "How to check for memory leak"
date: 2020-03-01
categories: [Development,General]
tags: [memory,valgrind]
---

To check for memory leak we will use [valgrind](https://www.valgrind.org/) tools.
First we need to compile and install it:

~~~ bash
$ wget https://sourceware.org/pub/valgrind/valgrind-3.16.1.tar.bz2
$ tar -xf valgrind-3.16.1.tar.bz2 && rm valgrind-3.16.1.tar.bz2
$ cd valgrind-3.16.1
$ ./configure
$ make -j$(nproc)
$ sudo make install
~~~

Now we can check for memory leak some application:
~~~ bash
$ valgrind --tool=memcheck --leak-check=yes --show-reachable=yes <app>
~~~

The difference in the number of allocs and the number of frees showed a memory leaks.