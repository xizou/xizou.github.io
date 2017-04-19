---
layout: post
title: "Installation of Nurbs Toolbox for GeoPdes"
date: 2016-04-01 21:32:41 +0200
categories: isogeometric analysis
---

## Download source file

Download the [GeoPdes](http://rafavzqz.github.io/geopdes/) .

Download the latest version of [nurbs toolbox](http://octave.sourceforge.net/nurbs/index.html).

## Unzip the source file
```shell
$ tar xvf GeoPDEs_full.tar.gz
$ tar xvf nurbs-1.3.10.tar.gz
```

Go to GeoPDE folder and unzip the mex files:
```shell
$ tar xvf nurbs_mex_files.tar.gz
```

## Compile the mex files

Go to nurbs_mex_files folder to compile with
```octave
>> compile
```
On Windows, `mexmat.h` may need to modify by adding a line
```c
#include <uchar.h>
```
before
```c
#include "mex.h"
```
to avoid typedef errors.