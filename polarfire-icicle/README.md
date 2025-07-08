# Microchip Polarfire Icicle kernel snap

Kernel snap for running Ubuntu Core 22 on the [Polarfire SoC Icicle kit](https://www.microchip.com/en-us/development-tool/mpfs-icicle-kit-es).

## Notices

Some notices.

### This kernel is in search of a maintainer!

If you are interested in maintaining this kernel snap, please let us know!
It would also be helpful if the same person maintained this platform's
[gadget snap](https://github.com/canonical/iot-field-gadget-snap/tree/22/polarfire-icicle) :)

## Overview

This snap is built out of three components:

1) The official Debian RISCV64 kernel,
2) The Debian linux firmware package, and
3) An initrd built from a minimal Jammy base image

## Building

This snap should be built using the experimental kernel and
initrd snapcraft plugins. These plugins are available from
[this](https://github.com/canonical/snapcraft/pull/4302) PR. In order to build
this snap, you will have to install that fork of snapcraft.

You can find a ready-built snap package of that
snapcraft in the Releases section of this repository
[here](https://github.com/canonical/iot-field-kernel-snap/releases/tag/temp).

This snap should be buildable with:

```
  snapcraft --enable-experimental-plugins
```
