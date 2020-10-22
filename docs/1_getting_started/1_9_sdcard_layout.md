---
layout: default
title: SD Card Layout
parent: Getting Started
nav_order: 3
---

# Getting Started with SD Card Layout
{: .no_toc }

1. TOC
{:toc}
---

## SD Card Layout

### Using Parted Tool

1. Use _parted(8)_ tool to create the SD Card layout:
```console
# parted /dev/sd<x>
```
2. Enter the following options to define the layout:
```console
 mklabel msdos
 mkpart primary fat16 8MiB 100MiB
 mkpart primary ext4 100MiB -1
```
3. Leave the _parted(8)_ tool:
```console
  quit
```

4. Create file systems on the newly created partitions:
```console
# mkfs -t vfat -n BOOT /dev/sd<x>1
# mkfs -t ext4 -L ROOT /dev/sd<x>2
```
