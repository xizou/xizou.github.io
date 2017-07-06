---
layout: post
title:  "MATLAB OpenGL Settings"
date:   2017-07-03 11:27:33 +0200
categories: matlab
---

In MATLAB, setting OpenGL with hardware acceleration could cause graphic problems especially in Linux systems.

```matlab
>> opengl('save','hardware')
```

In ArchLinux, the libraries usually are newer than MATLAB libraries. Using MATLAB libraries together with system libraries could cause some problems, especially graphic faults.

Solution: force MATLAB using system libraries. For example, for `libstdc++.so.6`:

```shell
$ cd $MATLAB_HOME/sys/os/glnxa64
$ mv libstdc++.so.6 libstdc++.so.6.old
```
This could solve graphic problems for R2014b or higher version.
