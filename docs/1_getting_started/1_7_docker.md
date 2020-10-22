---
layout: default
title: Docker
parent: Getting Started
nav_order: 3
---

# Getting Started with Docker
{: .no_toc }

1. TOC
{:toc}
---

* You must have a Yocto environment configured.
* Highly recommended to have a 32GB SD Card.
* This process my take several hours.

## Docker Installation Support on Yocto BSP

1. Retrieve the meta-virtualization layer:
```console
$ git clone https://git.yoctoproject.org/git/meta-virtualization sources/meta-virtualization -b <version>
```

2. To first step you need to do is create the build folder project:
```console
$ EULA=1 MACHINE=<board> DISTRO=fsl-imx-xwayland source ./fsl-setup-release.sh -b bld-dir
```

3. Update the bblayers.conf file adding the meta-virtualization layer:
```console
$ echo "BBLAYERS += \" \${BSPDIR}/sources/meta-virtualization \"" >> conf/bblayers.conf
```

4. Update the local.conf as follows:

4.1 Add extra space for images:
```console
IMAGE_ROOTFS_EXTRA_SPACE = " 10000000 "
```

4.2 Add docker to the image and also connman to manage the networking:
```console
DISTRO_FEATURES_append = " virtualization "
IMAGE_INSTALL_append += " docker docker-contrib connman connman-client "
CORE_IMAGE_EXTRA_INSTALL += " openssh "
```

4.3 Add basic development capabilities:
```console
EXTRA_IMAGE_FEATURES = " dev-pkgs debug-tweaks tools-debug ssh-server-openssh "
```

4.4 Add packages for networking capabilities:
```console
IMAGE_INSTALL_append = " net-tools iputils dhcpcd "
```

4.5 Add generic tools:
```console
IMAGE_INSTALL_append = "which gzip python python-pip"
IMAGE_INSTALL_append = "wget cmake gtest git zlib patchelf"
IMAGE_INSTALL_append = "nano grep vim tmux swig tar unzip"
IMAGE_INSTALL_append = "parted e2fsprogs e2fsprogs-resize2fs"
```

4.6 Add CMake for SDK’s cross-compiler:
```console
TOOLCHAIN_HOST_TASK_append = " nativesdk-cmake nativesdk-make "
```

5. Build an full i.MX image:
```console
$ bitbake imx-image-full
```

6. Uncompress the image and flash it to the SD Card:
```console
$ cd tmp/deploy/images/ && bunzip2 -f <image>.sdcard.bzip2
$ dd if=<image>.sdcard of=/dev/sd<x> bs=1M status=progress && sync
```

## Docker Examples

### Docker Base

These steps explain how to create an image from scratch with all the NXP
resources for compiling and running examples.

1. Choose `GNU/Linux Ubuntu 16.04` as your base:
```console
# docker pull ubuntu:16.04
```

2. Run the following line:
```console
# docker run -it --name first_image -h docker --privileged=true --net=host ubuntu:16.04 /bin/bash
```

3. Install the following packages:
```console
root@docker:/# apt update
root@docker:/# apt install -y vim git build-essential checkinstall cifs-utils nfs-common \
                                  software-properties-common strace gcc cmake wget
```

4. Clean the repositories:
```console
root@docker:/# apt clean && apt autoremove && rm -rf /var/lib/apt/lists/*
```

5. Download the NXP drivers:
```console
root@docker:/# wget https://www.nxp.com/lgfiles/NMG/MAD/YOCTO/imx-gpu-viv-<version>-aarch64.bin
root@docker:/# chmod +x imx-gpu-viv-<version>-aarch64.bin
root@docker:/# ./imx-gpu-viv-<version>-aarch64.bin--force --auto-accept
```

6. Copy the NXP drivers:
```console
root@docker:/# mkdir -p /etc/OpenCL/vendors/
root@docker:/# cp -r imx-gpu-viv-<version>-aarch64/gpu-core/etc/Vivante.icd /etc/OpenCL/vendors/
root@docker:/# cp -r imx-gpu-viv-<version>-aarch64/gpu-core/usr/* /usr
root@docker:/# cp -r imx-gpu-viv-<version>-aarch64/gpu-demos/opt/* /opt
root@docker:/# cp -r imx-gpu-viv-<version>-aarch64/gpu-tools/gmem-info/* /
```

7. Exit the container by pressing: **CTRL + P**, then **CTRL + Q**.
```console
# docker ps -a
# docker commit <ID_CONTAINER> <new_image_name>
```

### Docker Running Clinfo Application

1. Run the Docker container:
```console
# docker run -it --name cl-test -h docker --privileged=true --net=host \
                         <new_image_name> /bin/bash
```

2. Retrieve the clinfo repository:
```console
root@docker:/# git clone https://github.com/Oblomov/clinfo.git && cd clinfo
```

3. Compile the Clinfo application tool:
```console
root@docker:~/clinfo# make
```

4. There are two ways for running the example:

* Inside the container:
```console
root@docker:~/clinfo# ./clinfo
```

* Outside the container, this case exit the container by pressing **CTRL + P**,
then **CTRL + Q**:
```console
# docker ps -a
# docker cp <ID_CONTAINER>:/clinfo/clinfo .
# ./clinfo
```
