# VisionFive2 kernel snap

## Notice

This branch builds the kernel and initrd by using the experimental kernel plugin
currently only available via a [PR](https://github.com/canonical/snapcraft/pull/4302) 
currently open against snapcraft.

In order to build the kernel available on this branch, you will have to build
and install this fork of snapcraft until that PR is merged.

Alternatively, you can download a prebuilt version [here](https://launchpad.net/~ondrak/+snap/snapcraft-kernel-initrd-split).

Instructions for building the snapcraft fork:

```sh
    git clone --branch split_kernel_initrd_plugins https://github.com/kubiko/snapcraft
    cd snapcraft
    snapcraft
    snap install --dangerous --classic snapcraft_*.snap
```

If you instead opt to download a prebuilt snap, just do the last line from above.


## Overview

This snap is built out of two components:

1) The official debian VisionFive2 kernel,
2) An initrd built from a minimal Noble base image

It is intended for use by both the StarFive VisionFive2 board, which offers a
powerful yet accessible entrance into RISC-V SBCs.


## Building

Be sure to have done the manual intervention in the above notice to ensure a
compatible version of snapcraft is installed.

```sh
  git clone -b 24-riscv64-visionfive2 -d 1 https://github.com/canonical/iot-field-kernel-snap 24-riscv64-visionfive2-kernel && cd 24-riscv64-visionfive2-kernel
  snapcraft
```

