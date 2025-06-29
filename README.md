# The kernel snap

## Notice

This branch builds the kernel and initrd by using the experimental kernel plugin
currently only available via a [PR](https://github.com/canonical/snapcraft/pull/4302) 
currently open against snapcraft.

Instructions for building the snapcraft fork:

```sh
    git clone --branch split_kernel_initrd_plugins https://github.com/kubiko/snapcraft
    cd snapcraft
    snapcraft
    snap install --dangerous --classic snapcraft_*.snap
```


## Overview

This snap is built out of three components:

1) The official Debian Nezha kernel,
2) The Debian linux firmware package, and
3) An initrd built from a minimal Jammy base image

It is intended for use by both the Nezha and Sipeed LicheeRV boards, one very
accessible and another very, VERY accessible pieces of RISC-V hardware. While
not the most powerful boards available, they offer a simple development platform
to facilitate experimentation and native building.


## Building

In order to build this kernel snap, build and install the above snapcraft fork.
Then, assuming amd64 build host:

```sh
  git clone \
    --depth 1 \
    --branch 22-riscv64-nezha \
    https://github.com/canonical/iot-field-kernel-snap \
    22-riscv64-nezha-kernel && cd 22-riscv64-nezha-kernel

  snapcraft --enable-experimental-plugins
```
