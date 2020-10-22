---
layout: default
title: Toolchain
parent: Getting Started
nav_order: 3
---

# Getting Started with Toolchain
{: .no_toc }

1. TOC
{:toc}
---

## Open Source Toolchain

* The described procedures in this document targets a GNU/Linux (Ubuntu 18.04.3 LTS).
* You need root privileges (sudo) to use apt update and apt install.

### Tools

To compile and build our OS, we need to install a few tools and packages.

1. Open a terminal console and type:
```console
$ apt update
$ apt install build-essential libncurses-dev libssl-dev lzop bison flex git
```

Since we are compiling a system to run in another platform we need to compile it
for its respective architecture using a cross compiler toolchain.

### GCC Arm GNU/Linux Toolchain 32 Bits

1. Install GCC and G++ cross-compilers:
```console
$ apt install gcc-arm-linux-gnueabi g++-arm-linux-gnueabi
```

2. Export environment variables
```console
$ export ARCH=arm
$ export CROSS_COMPILE=/usr/bin/arm-linux-gnueabi-
```

### GCC Arm GNU/Linux Toolchain 64 Bits

1. Install GCC and G++ cross-compilers:
```console
$ apt install gcc-aarch64-linux-gnu g++-aarch64-linux-gnu
```

2. Export environment variables
```console
$ export ARCH=arm64
$ export CROSS_COMPILE=/usr/bin/aarc64-linux-gnu-
```


### Optional

Once you close your terminal, those environment variable are no longer set. They
are required every time a new terminal is opened. In order to make it easier,
type these commands:

#### Arm 32 Bits

```console
$ touch ~/arm
$ echo "export ARCH=arm" >> ~/arm
$ echo "export CROSS_COMPILE=/usr/bin/arm-linux-gnueabi-" >> ~/arm
```

Now every time you open a new terminal, just type:

```console
$ source ~/arm
```

#### Arm 64 Bits

```console
$ touch ~/arm64
$ echo "export ARCH=arm64" >> ~/arm64
$ echo "export CROSS_COMPILE=/usr/bin/aarch-linux-gnu-" >> ~/arm64
```

Now every time you open a new terminal, just type:

```console
$ source ~/arm64
```

## Yocto Toolchain

The toolchain created by the Yocto Project tools provides a set of tools
(compilers, libraries, and header files) to cross-compile the code for the
previously-built images.

1. Build the SDK with the Qt 5 support:
```console
$bitbake fsl-image-qt5 -c populate_sdk
```

After the build process finishes, it produces an installer script that can be
used to install the SDK on the developing system. The script is created in the:

* *tmp/deploy/sdk/fsl-imx-xwayland-glibc-x86_64-fslimage-qt5-aarch64-toolchain-<version>.sh*

2. Install the toolchain accepting all the default settings:
```console
$ chmod +x fsl-imx-xwayland-glibc-x86_64-fslimage-qt5-aarch64-toolchain-<version>.sh
$ ./fsl-imx-xwayland-glibc-x86_64-fslimage-qt5-aarch64-toolchain-<version>.sh
```

3. To export the toolchain:
```console
$ source /opt/fsl-imx-internal-xwayland/<version>/environment-setup-aarch64-poky-linux
```



