---
layout: post
title: "Reusing Abaqus global matrices"
date: 2016-10-21 16:01:44 +0200
categories: abaqus
---

## Output global matrix

With an extra step in input file `job.inp`, one can output global matrix with:

```
*MATRIX GENERATE, STIFFNESS
*MATRIX OUTPUT, STIFFNESS, FORMAT=COORDINATE
```

The first line generates a binary file `job_X1.sim`, which can be reused by Abaqus.
The second line generates a text file `job_STIF1.mtx`, which can be read by MATLAB with:

```matlab
m = load('job_STIF1.mtx');
m = spconvert(m);
```

## Input global matrix

First, set all element with a null material property such as:

```
*SOLID SECTION, ELSET=ELEMENTS-ALL, MATERIAL=MAT-NULL
*MATERIAL, NAME=MAT-NULL
*ELASTIC
1.E-36, 0.3
```

Then, read the `job_X1.sim` file:

```
*MATRIX INPUT, NAME=K_ext, INPUT=job_X1.sim, MATRIX=STIFFNESS, SCALE FACTOR=1.0
*MATRIX ASSEMBLE, STIFFNESS=K_ext
```

The second line assembles the matrix $\mathbf{K}_\mathrm{ext}$ (read from `job_X1.sim`) to the model with a null stiffness matrix $\mathbf{K}_0$.

$$
\mathbf{K} = \mathbf{K}_0 + \mathbf{K}_{\mathrm{ext}}
$$
