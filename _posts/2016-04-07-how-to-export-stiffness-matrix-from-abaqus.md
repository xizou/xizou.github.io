---
layout: post
title: "How to export stiffness matrix from Abaqus"
date:   2016-04-07 13:21:00 +0200
categories: abaqus
---

## Global stiffness matrix

An independent step is required for exporting assembled global stiffness matrix as well as mass matrix, etc.

An example: adding the following step into Abaqus input file `jobfile.inp`

```
** Output Global Stiffness Matrix
*Step, name=Global_Stiffness_Matrix
*MATRIX GENERATE, STIFFNESS
*MATRIX OUTPUT, STIFFNESS, FORMAT=MATRIX INPUT
*End Step
```

Run Abaqus through command:

```shell
$ abaqus -job jobfile
```

The outputs should include a binary file `jobfile_X1.sim` and a text file `jobfile_STIF1.mtx`. The binary file can be reread by Abaqus (see [this post](https://xizou.github.io/2016/10/21/reusing-abaqus-global-matrices/)).

The text output would be a file `jobfile_STIF1.mtx`, which is a data file like:

```
Node1, Dof1, Node2, Dof2,  Stiffness value

  101,    1,   101,    1,  9.890109890109926e+01
  101,    2,   101,    1,  3.571428571428572e+01
  102,    1,   101,    1, -6.043956043956079e+01
  102,    2,   101,    1, -2.747252747252749e+00
  103,    1,   101,    1, -4.945054945054909e+01
  103,    2,   101,    1, -3.571428571428572e+01
  104,    1,   101,    1,  1.098901098901063e+01
  104,    2,   101,    1,  2.747252747252750e+00
  101,    2,   101,    2,  9.890109890109926e+01
......
```

Using a Python script with following functions, it is possible to convert the `mtx` file into a COO sparse matrix file following Matlab ASCII format:

```python
def idx_conv(a,b):
    if b==3:
        return a*b
    else:
        return (a-1)*3 + b


def proc_mtx(fn_in,fn_out):
    fin = open(fn_in, 'r')
    fout = open(fn_out, 'w')
    for line in fin:
        line = line.split(',')
        v = float(line[4])
        i = idx_conv(int(line[0]),int(line[1]))
        j = idx_conv(int(line[2]),int(line[3]))
        fout.write('{0},{1},{2}\n'.format(i,j,v))
        if i!=j:
            fout.write('{0},{1},{2}\n'.format(j,i,v))
    fout.close()
    fin.close()
```

The output file will look like:

```
row, column,  value
  1,      1,  4.01340706825e-36
  2,      1,  5.60098743107e-37
  1,      2,  5.60098743107e-37
  3,      1, -5.22761524367e-37
  1,      3, -5.22761524367e-37
  4,      1, -2.07870246255e-37
  1,      4, -2.07870246255e-37
  5,      1, -3.62444662193e-38
  1,      5, -3.62444662193e-38
  6,      1, -3.18133224619e-37
  1,      6, -3.18133224619e-37
  7,      1, -6.63360514036e-37
  1,      7, -6.63360514036e-37
```

An alternative approach is to modify the line

`*MATRIX OUTPUT, STIFFNESS, FORMAT=MATRIX INPUT`

into

`*MATRIX OUTPUT, STIFFNESS, FORMAT=COORDINATE`

The output will be the same as the results generated from the Python script.

This works both for linear and nonlinear analysis. For the nonlinear case, the stiffness matrix is available only between steps since it requires an additional step to output.

## Element stiffness matrix

The element stiffness matrix can also be output with a step as

```
*STEP, name=Test
*STATIC
*FILE FORMAT, ASCII
*ELEMENT MATRIX OUTPUT, ELSET=FRAME, FILE NAME=Stiffness, FREQUENCY=1, OUTPUT FILE=USER DEFINED, STIFFNESS=YES
*END STEP
```

The result file will be only `Stiffness.mtx`, which looks like:

```
**
** ELEMENT NUMBER 11 STEP NUMBER 2 INCREMENT NUMBER 1
** ELEMENT TYPE CPS4
*USER ELEMENT, NODES= 4, LINEAR
** ELEMENT NODES
** 101, 102, 103, 104
1, 2
*MATRIX,TYPE=STIFFNESS
98.901098901099 ,
35.714285714286 , 98.901098901099
-60.439560439561 , 2.7472527472527 , 98.901098901099
-2.7472527472527 , 10.989010989011 , -35.714285714286 , 98.901098901099
-49.450549450549 , -35.714285714286 , 10.989010989011 , 2.7472527472528
98.901098901099 ,
-35.714285714286 , -49.450549450549 , -2.7472527472527 , -60.439560439561
35.714285714286 , 98.901098901099
10.989010989011 , -2.7472527472527 , -49.450549450549 , 35.714285714286
-60.439560439561 , 2.7472527472528 , 98.901098901099
2.7472527472527 , -60.439560439561 , 35.714285714286 , -49.450549450549
-2.7472527472527 , 10.989010989011 , -35.714285714286 , 98.901098901099
```