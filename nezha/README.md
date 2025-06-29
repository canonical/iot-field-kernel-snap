# The kernel snap

## Overview

This snap is built out of three components:

1) The official Debian Nezha kernel,
2) The Debian linux firmware package, and
3) An initrd built from a minimal Jammy base image

It is intended for use by both the Nezha and Sipeed LicheeRV boards, one very
accessible and another very, VERY accessible pieces of RISC-V hardware. While
not the most powerful boards available, they offer a simple development platform
to facilitate experimentation and native building.
