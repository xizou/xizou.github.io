---
layout: post
title:  "HFS+ Rescue Log"
date:   2016-11-17 18:20:00 +0100
categories: ict
---

## Install HFS+ Rescue

Download from https://www.plop.at/en/hfsprescue/download.html.
Compile and install according to instructions.

## Scan the corrupted disk

Usually the block size is 4096.

```shell
$ hfsprescue -s1 /dev/sdc1 -b 4096
```

## Cleanup file database

```shell
$ hfsprescue -s2 /dev/sdc1 -b 4096
```

## Extract the Extents Overflow File

Volume Header is defect, it is needed to extract the EOF.

```shell
$ hfsprescue --find-eof /dev/sdc1 -b 4096
```

Result:

```
1. Possible block: 4928 | File position: 0x1340000
2. Possible block: 5285 | File position: 0x14a5000    maybe ExtentsOverflowFile or CatalogFile
```

So the last block is 5285-1=5284:

```shell
$ hfsprescue --extract-eof /dev/sdc1 -b 4096 --start-block 4928 --last-block 5284
```

After that, use `--one-file` to test the restored file.

## Restore files

```shell
$ hfsprescue -s3 /dev/sdc1 -b 4096
```

## Restore the directory structure

```shell
$ hfsprescue -s4
```

## Move the restored files to the correct directories
```shell
$ hfsprescue -s5
```

## Cleanup

```shell
$ hfsprescue -s6
```

