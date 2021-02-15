---
title: "Yocto cheat sheet"
date: 2020-03-01
categories: [Engineering,Yocto]
tags: [yocto,bitbake]
---

# Yocto

Check if certain package is present on current setup:
~~~
bitbake -s | grep <package>
~~~

Show possible image to bake:
~~~
$ bitbake-layers show-recipes "*-image-*"
~~~

Show possible layers:
~~~
$ bitbake-layers show-layers
~~~

# Image

Get complete list of packages that will be built for given image:
~~~
bitbake -g <image> && cat pn-buildlist | grep -ve "native" | sort | uniq
~~~

# Packages

Lists all packages **that have been built**:
~~~
$ oe-pkgdata-util list-pkgs
~~~

Lists the files and directories contained in the given packages:
~~~
$ oe-pkgdata-util list-pkg-files package
~~~

Lists the names of the packages that contain the given paths:
~~~
oe-pkgdata-util find-path <path>
~~~
For example, the following tells us that */usr/share/man/man1/make.1* is contained in the make-doc package:
~~~
$ oe-pkgdata-util find-path /usr/share/man/man1/make.1
make-doc: /usr/share/man/man1/make.1
~~~

Lists the name of the recipes that produce the given packages:
~~~
oe-pkgdata-util lookup-recipe <package>
~~~

Get information of particular package:
~~~
$ oe-pkgdata-util read-value <value> <pkg1>
~~~
Where _value_ can be: SUMMARY, DESCRIPTION, etc.

View dependency information:
~~~
$ bitbake -g <recipe> -u depexp
~~~