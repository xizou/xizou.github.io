---
layout: post
title: "Preprocessing Isomap MATLAB Package"
date: 2016-01-04 04:59:52 +0200
categories: machine learning
---

## Download source

Download the source from [here](http://isomap.stanford.edu/).

## Modify the files
Comment the following lines in `dijkstra.cpp`:
```c++
extern void _main();

#include <stdlib.h>
#include <iostream.h>
#include <stdio.h>
#include <conio.h>
#include <curses.h>
#include <ctype.h>
#include <memory.h>
#include <time.h>

#include "fibheap.h"

#define _FIBHEAP_CPP
```
and comment a later repeated line:
```c++
#include <conio.h>
```
Modify
```c++
#include <stdlib.h>
#include <iostream.h>
#include <stdio.h>
```
into
```c++
#include <stdlib.h>
#include <iostream>
#include <stdio.h>
```
Then adding the following line
```c++
using namespace std;
```
after line
```c++
#include "fibheap.h"
```
Finally, comment the following block:
```c++
if (kbhit() && getch() == 27)
  {
    cout << "Hit a key to resume or ESC to break\n";
      if (getch() == 27)
        break;
  }
```

Add space before the second `num2str` in file `IsomapII.m` at line 179,196,218,248,273:

```octave
disp([' Iteration: ' num2str(i) '     Estimated time to completion: ' num2str((N-i)*toc/60/50) ' minutes']); tic;
```
Then change `LR` in line 319 to `LA`:
```octave
[alpha,beta] = eigs(subB'*subB, max(dims), 'LA', opt);
```

## Complie the file
Compile the file in matlab
```octave
mex -largeArrayDims dijkstra.cpp
```

On 64-bit Linux systems, there will be a problem like:

>
>cannot convert ‘mwIndex* {aka long unsigned int*}’ to ‘int*’ in assignment
```c++
  irs     = mxGetIr(prhs[0]);
```

The problem comes from the places where original code converts int's to pointers. In 32-bit machine this is okay, but with 64-bit, you need to use a 64-bit type instead of int. In this case, if you read the error in detail, it tells you that mwIndex (a 64-bit pointer of type unsigned long \*) is being assigned to an int. If you go to that line you see irs = mxGetIr(prhs[0]); Find where ir is defined (line 800, 903: `int`) and change its type to a unsigned long (`long unsigned int`). And repeat a similar procedure for this kind of errors.

On 64-bit Windows systems, there will be a problem like:

>
>cannot convert ‘mwIndex* {aka long long unsigned int*}’ to ‘int*’ in assignment
```c++
  irs     = mxGetIr(prhs[0]);
```

Solution is similar by changing `int` to `long long unsigned int`.
