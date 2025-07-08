# Nezha kernel snap

Kernel snap for running Ubuntu Core 22 on the [Sipeed
LicheeRV](https://wiki.sipeed.com/hardware/en/lichee/RV/RV.html) and the [Nezha
D1](https://d1.docs.aw-ol.com/en/d1_dev/).

## Overview

This snap is built out of three components:

1) The official Debian Nezha kernel,
2) The Debian linux firmware package, and
3) An initrd built from a minimal Jammy base image

The kernel also comes with a component named wlan, which adds support for the
WiFi module on the Sipeed LicheeRV. This module is not supported on the Nezha
board.

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
