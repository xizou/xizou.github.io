---
layout: post
title:  "A Note on TeXLive Manager"
date:   2017-03-16 11:09:33 +0200
categories: latex
---

### Using TeXLive 2015 on LinuxMint in usermode

```shell
$ tlmgr init-usertree
$ tlmgr option repository ftp://tug.org/historic/systems/texlive/2015/tlnet-final
```

will generate `$HOME/texmf/`, including subfolders `tlpkg` and `web2c`.

```shell
$ sudo apt install perl-tk
$ sudo apt install xzdec
$ tlmgr --gui
```

To use `newtx` package, the following should be installed:
- `newtx`
- `kastrup`
- `boondox`

### Use of `newtx` package:

##### Method 1
```latex
\usepackage{newtxtext}
\usepackage[cmintegrals]{newtxmath}
```
##### Method 2

```latex
\usepackage{esint,newtxtext,newtxmath}
```