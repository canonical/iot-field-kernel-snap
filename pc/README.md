# PC kernel snap

## Overview

This snap is intended to provide a "generic" Ubuntu kernel for AMD64 devices,
scare-quotes because really it's intended for use on my mid-2014 Macbook Pro
running Ubuntu Core Desktop, hence the broadcom-sta and facetimehd parts and a
lack of secureboot support :)

This snap is meant to act as a reference for components and interfaces.


## Building

```
  snap install --classic --channel=latest/edge/kernel-initrd-plugin snapcraft
  snapcraft pack
```
