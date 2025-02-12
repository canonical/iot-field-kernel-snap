# The kernel snap

## Notice

This branch builds the kernel and initrd by using the experimental kernel plugin
currently only available via a [PR](https://github.com/canonical/snapcraft/pull/4302) 
currently open against snapcraft.

In order to build the kernel available on this branch, you will have to build
and install this fork of snapcraft until that PR is merged.

If [#5233](https://github.com/canonical/snapcraft/pull/5233) has not yet been
merged, apply the below patch as well!

```
diff --git a/requirements-devel.txt b/requirements-devel.txt
index 7880a30c2..da34c32dc 100644
--- a/requirements-devel.txt
+++ b/requirements-devel.txt
@@ -47,7 +47,7 @@ coverage[toml]==7.6.4
     #   snapcraft (setup.py)
 craft-application==4.4.0
     # via snapcraft (setup.py)
-craft-archives==2.0.1
+git+https://github.com/canonical/craft-archives@work/CRAFT-2425-foreign-archs
     # via
     #   craft-application
     #   snapcraft (setup.py)
diff --git a/requirements-docs.txt b/requirements-docs.txt
index 9f311008c..fffd4090d 100644
--- a/requirements-docs.txt
+++ b/requirements-docs.txt
@@ -61,7 +61,7 @@ colorama==0.4.6
     # via sphinx-autobuild
 craft-application==4.4.0
     # via snapcraft (setup.py)
-craft-archives==2.0.1
+git+https://github.com/canonical/craft-archives@work/CRAFT-2425-foreign-archs
     # via
     #   craft-application
     #   snapcraft (setup.py)
diff --git a/requirements.txt b/requirements.txt
index 932ff5a8e..a3795e1dc 100644
--- a/requirements.txt
+++ b/requirements.txt
@@ -29,7 +29,7 @@ click==8.1.7
     # via snapcraft (setup.py)
 craft-application==4.4.0
     # via snapcraft (setup.py)
-craft-archives==2.0.1
+git+https://github.com/canonical/craft-archives@work/CRAFT-2425-foreign-archs
     # via
     #   craft-application
     #   snapcraft (setup.py)
diff --git a/setup.py b/setup.py
index 87cec33c0..e5cf65698 100755
--- a/setup.py
+++ b/setup.py
@@ -99,7 +99,7 @@ install_requires = [
     "catkin-pkg; sys_platform == 'linux'",
     "click",
     "craft-application~=4.4",
-    "craft-archives~=2.0",
+    "craft-archives @ git+https://github.com/canonical/craft-archives@work/CRAFT-2425-foreign-archs",
     "craft-cli>=2.15.0",
     "craft-grammar>=2.0.1,<3.0.0",
     "craft-parts==2.4.1",
diff --git a/snap/snapcraft.yaml b/snap/snapcraft.yaml
index 347655002..d6c13827a 100644
--- a/snap/snapcraft.yaml
+++ b/snap/snapcraft.yaml
@@ -187,7 +187,7 @@ parts:
     build-environment:
         # Build PyNaCl from source since the wheel files interact
         # strangely with classic snaps. Well, build it all from source.
-        - "PIP_NO_BINARY": ":all:"
+        # - "PIP_NO_BINARY": ":all:"
         # Use base image's libsodium for PyNaCl.
         - "SODIUM_INSTALL": "system"
         - "CFLAGS": "$(pkg-config python-3.12 yaml-0.1 --cflags)"

```

Instructions for building the snapcraft fork:

```sh
    git clone --branch split_kernel_initrd_plugins https://github.com/kubiko/snapcraft
    cd snapcraft
    # Apply the aforementioned patch if necessary
    snapcraft
    snap install --dangerous --classic snapcraft_*.snap
```

If you instead opt to download a prebuilt snap, just do the last line from above.


## Overview

This snap is built out of three components:

1) The official Debian Nezha kernel,
2) The Debian linux firmware package, and
3) An initrd built from a minimal Noble base image

It is intended for use by both the Nezha and Sipeed LicheeRV boards, one very
accessible and another very, VERY accessible pieces of RISC-V hardware. While
not the most powerful boards available, they offer a simple development platform
to facilitate experimentation and native building.


## Building

If you built the above fork with the given patch, these instructions are unnecessary.

In order to build this kernel snap some small changes must be made which make
building this snap different from others. Once [this](https://github.com/canonical/snapcraft/pull/5233) 
PR has been merged, the below interventions should not be required. Until
then, here are instructions for building this snap, assuming amd64 build host:

```sh
  lxc launch ubuntu:24.04 kernel-snaps
  lxc file push path/to/modified/snapcraft/snapcraft*.snap kernel-snaps/root/
  lxc shell kernel-snaps

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

  snap install --dangerous --classic /root/snapcraft*.snap

  git clone \
    --branch 24-riscv64-nezha \
    --depth 1 \
    https://github.com/canonical/iot-field-kernel-snap \
    24-riscv64-nezha-kernel && cd 24-riscv64-nezha-kernel

  snapcraft --destructive-mode
```

