---
layout: post
title: "Environment settings for Abaqus"
date: 2015-03-11 09:39:21 +0100
categories: abaqus, python
---

## Abaqus setting file

In current directory or user home directory, create a text file `abaqus_v6.env` with contents:

```python
ask_delete = OFF
run_mode = INTERACTIVE
odb_output_by_default = OFF
```

Explanation:
- `ask_delete` controls if directly overwrite existing analysis files.
- `run_mode` controls running mode, the default is `BACKGROUND`.
- `odb_output_by_default` controls if output odb file even no result is requested.