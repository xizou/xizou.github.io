---
layout: post
title: "Installation of Nurbs Toolbox for MATLAB"
date: 2016-04-01 21:32:41 +0200
categories: isogeometric analysis
---

## Download source file

Download the [GeoPdes](http://rafavzqz.github.io/geopdes/) . Check if it contains the latest version of [nurbs toolbox](http://octave.sourceforge.net/nurbs/index.html). If not, download it from the official [website](http://octave.sourceforge.net/nurbs/index.html).

The nurbs toolbox can directly run with `m` scripts, but the scripts could be too slow for heavy access to some functions. For acceleration purposes, it also contains compilable C source code for GNU Octave. To create MATLAB executable files, we can use C source codes provided in GeoPdes for mex compile.

## Unzip the source file
```shell
$ tar xvf GeoPDEs_full.tar.gz
$ tar xvf nurbs-1.3.13.tar.gz
```

Go to GeoPDE folder and unzip the mex files:
```shell
$ tar xvf nurbs_mex_files.tar.gz
```

## Compile the mex files

Before compiling, make sure [mex](https://www.mathworks.com/help/matlab/ref/mex.html) works correctly in MATLAB.

Go to `nurbs_mex_files` folder, and compile the mex files with

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

## Set the paths

You need to add the path of the nurbs toolbox to `pathdef` in MATLAB, so that MATLAB can find the functions.

You may copy the compiled mex executables to the nurbs toolbox folder or add the path to `pathdef`.