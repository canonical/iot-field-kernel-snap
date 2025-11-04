# i.MX kernel snap

## Overview

This snap provides an i.MX8 and i.MX9 kernel, including the OP-TEE feature to
enable full disk encryption.

This snap has been tested and works with the i.MX93 FRDM board.

## Building

```
  snap install --classic --channel=latest/edge/kernel-initrd-plugin snapcraft
  snapcraft pack
```
