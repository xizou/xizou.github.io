---
layout: post
title:  "Compiling OdePack static library"
date:   2016-02-25 20:44:04 +0100
categories: fortran
---


1. Download [OdePack](http://www.netlib.org/odepack/). Only three files are needed, for double precision, the files we need are:
`opkdmain.f`, `opkda1.f` and `opkda2.f`.
2. Compile the source codes with
```shell
$ gfortran -c opkdmain.f opkda1.f opkda2.f
```
three object files will be created, namely: `opkdmain.o`, `opkda1.o` and `opkda2.o`.
3. Build the static library `libodepack.a` with
```shell
$ ar rcs libodepack.a opkdmain.o opkda1.o opkda2.o
```