# Nezha kernel snap

## Notice

This branch builds the kernel and initrd by using the experimental kernel plugin
currently only available via a [PR](https://github.com/canonical/snapcraft/pull/4302) 
currently open against snapcraft.

In order to build the kernel available on this branch, you will have to build
and install this fork of snapcraft until that PR is merged.

Alternatively, you can download a prebuilt version [here](https://launchpad.net/~ondrak/+snap/snapcraft-kernel-initrd-split).

Instructions for building the snapcraft fork:

```
    git clone --branch split_kernel_initrd_plugins https://github.com/kubiko/snapcraft
    cd snapcraft
    snapcraft
    snap install --dangerous --classic snapcraft_*.snap
```

If you instead opt to download a prebuilt snap, just do the last line from above.


## Overview

This snap is built out of three components:

1) The official debian Nezha kernel,
2) An unofficial driver for the RTL8723DS, and
3) An initrd built from a minimal Noble base image

It is intended for use by both the Nezha and Sipeed LicheeRV boards, one very
accessible and another very, VERY accessible pieces of RISC-V hardware. While
not the most powerful boards available, they offer a simple development platform
to facilitate experimentation and native building.


## Building

In order to build this kernel snap some small changes must be made which make
building this snap different from others. Once [this](https:// github.com/canonical/craft-archives/issues/104) 
issue has been resolved, the below interventions should not be required. Until
then, here are instructions for building this snap, assuming amd64 build host:

```
  lxc launch ubuntu:24.04 kernel-snaps
  lxc file push path/to/modified/snapcraft/snapcraft*.snap kernels-snap/root/
  lxc shell kernels-snap

  sed -i '/Signed-By/a Architectures: amd64' /etc/apt/sources.list.d/ubuntu.sources
  sed -i 's/Types: deb/Types: deb deb-src/'  /etc/apt/sources.list.d/ubuntu.sources 

  { echo 'Types: deb deb-src'; \
    echo 'URIs: http://ports.ubuntu.com/ubuntu-ports'; \
    echo 'Suites: noble noble-updates noble-backports'; \
    echo 'Components: main universe restricted multiverse'; \
    echo 'Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg'; \
    echo 'Architectures: riscv64'; } >> /etc/apt/sources.list.d/ubuntu.sources

  ln -s /usr/riscv64-linux-gnu/lib/ld-linux-riscv64-lp64d.so.1 /lib

  dpkg --add-architecture riscv64
  apt update

  git clone -b ubuntu/noble https://git.launchpad.net/~ondrak/ubuntu/+source/fakechroot && cd fakechroot

  apt install build-essential crossbuild-essential-riscv64 devscripts libc6:riscv64
  tar cf - . | xz -z >> ../fakechroot_2.20.1+ds.orig.tar.xz
  apt build-dep -y -a riscv64 fakechroot
  DEB_BUILD_OPTIONS=nocheck dpkg-buildpackage -us -uc -b -d --host-arch riscv64
  dpkg -i ../libfakechroot_*.deb

  apt build-dep -y fakechroot
  DEB_BUILD_OPTIONS=nocheck dpkg-buildpackage -us -uc -b -d
  dpkg -i ../libfakechroot_*.deb
  dpkg -i ../fakechroot_*.deb

  snap install --dangerous --classic /root/snapcraft*.snap

  git clone -b 24-riscv64-nezha -d 1 https://github.com/canonical/iot-field-kernel-snap 24-riscv64-nezha-kernel && cd 24-riscv64-nezha-kernel

  snapcraft --destructive-mode
```

