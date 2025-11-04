# VisionFive2 kernel snap


## Overview

This snap is built out of two components:

1) The official debian VisionFive2 kernel,
2) An initrd built from a minimal Noble base image

It is intended for use by both the StarFive VisionFive2 board, which offers a
powerful yet accessible entrance into RISC-V SBCs.


## Building

```sh
  snap install --classic --channel=latest/edge/kernel-initrd-plugin snapcraft
  snapcraft pack
```

