# IoT Field Kernel Snaps

This repository is maintained by the IoT Devices Field team to store and test
kernel snaps we create.

Note that the contents of these kernel snaps are all public information and are
generally created for testing or interest in particular hardware. No promises
are made for stability or long term support.

Kernel snaps are generally verboten from the Global Store, so you will not
find these published anywhere. They are intended as implementation examples and
inspiration, for use on dangerous-grade model Ubuntu Core images.

* You can find IoT Field's gadget snaps [at this sister repository](https://github.com/canonical/iot-field-gadget-snap)
* You can find IoT Field's example snaps [at this cousin repository](https://github.com/canonical/iot-field-example-snaps)

!! NOTICE !!

This branch explains building kernel snaps in part by leveraging a kernel
plugin in snapcraft. The [PR](https://github.com/canonical/snapcraft/pull/4302)
updating that plugin to work for core20, 22, and core24 base kernel snaps has
not yet been merged.

There are two avenues forward:

1) Wait for the PR to get merged and, until then, follow the instructions to
    build the kernel snap from source.

2) Install a variant of snapcraft which includes the changes in that PR


The main upshot of the updated kernel plugin is that it easily enables
cross-building kernel snaps! However, there are outstanding bugs which require
some additional work until the relevant fixes are picked up.

To build a kernel snap using the updated plugin, follow the below instructions.

1) Download and install an alternative snapcraft

Snap package builds can be found [here](https://launchpad.net/~ondrak/+snap/snapcraft-kernel-initrd-split) 
and can be installed using `snap install --classic --dangerous path/to/downloaded/snapcraft/snapcraft*.snap`

* If you are building core20 kernel snaps, you will have to handle doing the
build manually. The relevant points in the snapcraft.yaml for doing so have been
noted, so just read carefully :)

* If you are building core22 base kernel snaps, that's it! Install the variant
snapcraft and simply run `snapcraft` to build your kernel snap :)

* If you are building core24 kernel snaps **natively**, that's it! Install the
variant snapcraft and simply run `snapcraft` to build your kernel snap :)

* If you are *cross*-building core24 kernel snaps, some additional, work is
required. For instance for a riscv64 kernel snap:

```
  lxc launch ubuntu:24.04 riscv64-kernel-snap
  lxc file push path/to/downloaded/snapcraft/snapcraft*.snap riscv64-kernel-snap/root/
  lxc shell riscv64-kernel-snap

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

  git clone https://github.com/kubiko/dracut-ng && cd dracut-ng
  apt install pkg-config libkmod-dev
  apt build-dep dracut
  ./configure
  make
  mount --bind dracut-install /usr/lib/dracut/dracut-install

  snap install --dangerous --classic /root/snapcraft*.snap

  cd /path/to/kernel/snap
  snapcraft --destructive-mode
```

The specific bugs to watch are 

* [this one](https://bugs.launchpad.net/ubuntu/+source/fakechroot/+bug/2073407) for a fakechroot bug,

* [this one](https://github.com/dracut-ng/dracut-ng/pull/548) for a dracut-install bug, and

* [this one](https://github.com/dracut-ng/dracut-ng/pull/550) for another dracut-install bug

These bugs will all need to be SRUd back into Ubuntu releases. This !! NOTICE !! section will be removed once resolved.


## Repository structure

Each kernel snap is confined to its own branch. The naming convention is:

\<Core release\>-\<architecture\>-\<platform\>

* The `main` branch is for hosting workflows, a project description (this README),
and an example skeleton of a kernel snap.
* If you wish to add a new kernel to this repository, choose either a board with
similar architecture or create a branch using `--orphan`.
* If you wish to update a kernel snap from one base to another, please create a
new branch with the same name changing the `<Core release>` prefix.


## Branches

Please ensure that your branch includes the following:

1) a README describing 
   - the board, 
   - how the kernel snap functions (in broad, general terms; no need to be too technical), 
   - including references to documentation if you prefer,
2) license information in the snapcraft.yaml
   - Any general text file should be CC-BY-SA
   - Any code should be GPLv3


## Workflows

The expectation is that any new branch should have an accompanying new workflow
added. That workflow should follow the general style of the other workflows, and
ideally there are two:

1) One workflow testing builds using some version-specific snapcraft (i.e. `{7,8}.x/edge`)
2) One workflow testing builds using snapcraft from `latest/edge`

This ensures continuous testing of our work to spot any potential regressions or breaking changes.

The workflows should only exist on the `main` branch.


## Contributing

This repository is open to contributions so long as:

1) You have signed the Canonical [Contributor License Agreement](https://ubuntu.com/legal/contributors)
2) You have signed the Ubuntu [Code of Conduct](https://launchpad.net/codeofconduct)


Commits should follow the below style:

* The entire message should read as:

```
    <path/to/file>: <imperative present tense short description of change>

    <longer explanation of change if required>

    Signed-off-by: <author name> <author email>
```

* Keep first line summaries to under 60 characters, body summaries under 80

* Include a signoff for each commit using `git commit -s`
    * To amend prior commits with a signoff, do `git rebase --signoff HEAD~<number of commits>`

* Signing commits with `git commit -S` (GPG or SSH) is preferred but not required

* If you are modifying the `README.md`, `LICENSE`, `.gitignore`, `.github`,
    etc., please use `README`, `LICENSE`, `github`, `gitignore`, etc as the filename in the message

* Commits should be atomic

* PRs should contain logically related commits

* Try to follow prior art and style wherever possible


Even if you have write permissions to this repository, unless it is trivial
please create a PR for your change and attempt to get a +1 from someone on the
FE IoT team.
