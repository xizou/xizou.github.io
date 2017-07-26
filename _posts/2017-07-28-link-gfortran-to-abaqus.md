---
layout: post
title: "How to link gfortran to Abaqus"
date:   2017-07-26 18:21:34 +0200
categories: abaqus
---

The default compiler for Abaqus user subroutines is Intel Fortran, which is usually too heavy to install in the system. It is preferred to use GNU Fortran compiler in Linux platform.

## Settings in original `.env` file

The original `SMA/site/abaqus_v6.env` file configures many settings. In fact, the setting file for Linux `SMA/site/lnx86_64.env` provides more valuable information:

```python
fortCmd = "ifort"   # <-- Fortran compiler

# Avoid signal trapping by the Fortran RTE
os.environ["FOR_IGNORE_EXCEPTIONS"] = "1"
# Disable messages from the Fotran RTE
os.environ["FOR_DISABLE_STACK_TRACE"] = "1"

# Do not let QLogic InfiniPath driver set processor affinity.
os.environ["IPATH_NO_CPUAFFINITY"] = "1"

# Add the flag "-free" to the compile_fortran command below to use free-format FORTRAN 90 syntax.

compile_fortran = [fortCmd,
                   '-V',
                   '-c', '-fPIC', '-auto', '-mP2OPT_hpo_vec_divbyzero=F', '-extend_source',
                   '-WB', '-I%I']

link_sl = [fortCmd,
           '-V',           
           '-cxxlib', '-fPIC', '-threads', '-shared',
           '%E', '-Wl,-soname,%U', '-o', '%U', '%F', '%A', '%L', '%B', '-parallel', '-Wl,-Bdynamic',
           '-i-dynamic', '-lifport', '-lifcoremt']

# Remove the temporary names from the namespace
del fortCmd
```

## First step: compiling

Assuming `gfortran` is installed in your Linux system and works properly, an intuitive change we would make should be substituting `ifort` with `gfortran`.

If we run test command `$ abaqus veryfy -user_std`, there will be mistakes:

```
Begin Compiling Abaqus/Standard User Subroutines
gfortran: error: unrecognized command line option ‘-V’
gfortran: error: unrecognized command line option ‘-auto’
gfortran: error: unrecognized command line option ‘-mP2OPT_hpo_vec_divbyzero=F’
gfortran: error: unrecognized command line option ‘-WB’
Abaqus Error: Problem during compilation - std_user.f
```

This is because the compiling flags used by `gfortran` is not compatible with `ifort`. So we look into the flags a little bit:

- `-V` Displays the compiler version information (same as the -logo option). We can simply remove it.
- `-auto` Causes all local, non`-save`d variables to be allocated on the run-time stack (same as `-automatic` or `-nosave`). The default is `-auto-scalar`. However, if you specify `-recursive` or `-openmp`, the default is `-automatic`. We can remove it since there is no counterpart in `gfortran`.
- `-mP2OPT_hpo_vec_divbyzero=F` Seems for dealing with divided by zero. No reference found yet. Simply remove it.
- `-WB` Turns a compile-time bounds check into a warning. Normally, compile-time bounds checks are errors. This is a relaxation of the code strictness, we can remove it or replace with `-Warray-bounds`.

## Second step: linking

After correction, the compilation error would gone but linking error occurs:

```
Begin Linking Abaqus/Standard User Subroutines
gfortran: error: unrecognized command line option ‘-cxxlib’
gfortran: error: unrecognized command line option ‘-threads’
gfortran: error: unrecognized command line option ‘-parallel’
gfortran: error: unrecognized command line option ‘-i-dynamic’
Abaqus Error: Problem during linking - Abaqus/Standard User Subroutines
Abaqus/Analysis exited with errors
```

Then we do the same analysis of the incompatible flags:

- `-cxxlib` Tells the compiler to link using certain C++ run-time libraries. For Linux, it should be removed.
- `-threads` Specifies that multithreaded libraries should be linked. I guess in `gfortran` we could use `-pthread`, but it would be preferred to use `-fopenmp` if OpenMP is used in the user subroutine.
- `-parallel` Tells the auto-parallelizer to generate multi-threaded code for loops that can be safely executed in parallel. To use this option, you must also specify `-O2` or `-O3`. We can use `-fopenmp` in case of need, or just remove it.
- `-i-dynamic` Causes Intel-provided libraries to be linked in dynamically. It is the opposite of `-i-static`. It should be removed. If static libraries are preferred, one can use `-static-libgfortran`.

## Final `.env` file

Finally, the working modified `abaqus_v6.env` file in user path looks like:

```python
fortCmd = 'gfortran'

compile_fortran = [fortCmd,
                   '-c', '-fPIC', '-extend_source',
                   '-Warray-bounds', '-I%I']

link_sl = [fortCmd,
           '-fPIC', '-pthread', '-shared',
           '%E', '-Wl,-soname,%U', '-o', '%U', '%F', '%A', '%L', '%B', '-Wl,-Bdynamic',
           '-lifport', '-lifcoremt']

del fortCmd
```

Although this could pass the test command, further optimization of the file may be applied.