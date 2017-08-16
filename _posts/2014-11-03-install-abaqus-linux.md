---
layout: post
title:  "Installing Abaqus 6.13-4 on Ubuntu Linux"
date:   2014-11-03 09:37:13 +0200
categories: abaqus
---

## Pre-installation

- First, install `csh`. That's because the default shell that Abaqus installer use is [csh](https://en.wikipedia.org/wiki/C_shell).

  ```shell
  $ sudo apt install csh tcsh
  ```

- Create link for necessary C libraries

  ```shell
  $ sudo ln -s /lib/i386-linux-gnu/libc.so.6 /lib/libc.so.6
  ```
  or

  ```shell
  $ sudo ln -s /lib/x86_64-linux-gnu/libc.so.6 /lib/libc.so.6
  ```
- Install `libjpeg62`, `libstdc++5` and `xfonts-75dpi` if not installed
  ```shell
  $ sudo apt install libjpeg62 libstdc++5 xfonts-75dpi
  ```

## Installation

- Install License Server following the manual
- Install Abaqus following the manual

## Post-installation

- Create link for temporary files

  ```shell
  $ sudo ln -s /tmp /usr/tmp
  ```

- Create alias for running license manager

  ```shell
  alias lm="/opt/abaqus/License/lmgrd -c /opt/abaqus/License/abq6134.dat -l /tmp/abq6134.log"
  ```

- Set the environment variable `export XLIB_SKIP_ARGB_VISUALS=1` in `.profile` or make alias

  ```shell
  alias abaqus="XLIB_SKIP_ARGB_VISUALS=1 /opt/abaqus/Commands/abaqus"
  ```

- If hardware acceleration is unnecessary, one can run Abaqus/CAE by

  ```shell
  $ abaqus cae -mesa
  ```

- If hardware acceleration is needed, one needs to modify `/usr/bin/Xorg` at one's own risk:

  ```shell
  #!/bin/sh
  #
  # Execute Xorg.wrap if it exists otherwise execute Xorg directly.
  # This allows distros to put the suid wrapper in a separate package.

  basedir=/usr/lib/xorg-server
  if [ -x "$basedir"/Xorg.wrap ]; then
      exec "$basedir"/Xorg.wrap "$@"
  else
      exec "$basedir"/Xorg "$@" +iglx
  fi
  ```
  This is because newer version of [Xorg](https://en.wikipedia.org/wiki/X.Org_Server) has disabled indirect OpenGL rendering.

  An alternative way is install `libosmesa6`, which is actually pointing to a newer version.

  ```shell
  $ sudo apt install libosmesa6
  ```
  Or one may use an older version of the libraries provided by Abaqus:

  ```shell
  ABQLIB=/opt/abaqus/6.13-4/code/bin/SMAExternal/mesa/full
  export LD_LIBRARY_PATH=$ABQLIB:$LD_LIBRARY_PATH
  ```

## References

1. [http://blog.yeguang-xue.com/2017/01/18/abaqus-installation](http://blog.yeguang-xue.com/2017/01/18/abaqus-installation)
2. [https://polymerfem.com/forum/finite-element-modeling/computer-software-aa/3288-glx-error-when-launching-abaqus-on-debian](https://polymerfem.com/forum/finite-element-modeling/computer-software-aa/3288-glx-error-when-launching-abaqus-on-debian)