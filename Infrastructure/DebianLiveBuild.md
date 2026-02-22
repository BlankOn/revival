# Debian Live Build

Git repository: https://salsa.debian.org/live-team/live-build.git

```
git clone https://salsa.debian.org/live-team/live-build.git debian-live-build
cd debian-live-build
sudo make install
sudo lb --version
sudo lb clean --purge
sudo lb config
sudo lb build
```

We will have `live-image-amd64.hybrid.iso` ready to boot.

# BlankOn Live Build

Currently the BlankOn Live Build config only tested with live-build version `20230502` or commit sha on `dd916ac5be9428ff79a28fb6343f5d244acca438`.

Git repository: https://github.com/BlankOn/blankon-live-build/

This repository contains configuration and script to build the ISO using Debian Live Build tool. Because of the nature of `lb` commands that could make a mess of the original configuration, the build script (`build.sh`) need to be run on separate working directory.

```
git clone https://github.com/BlankOn/blankon-live-build
mkdir live-build-workdir
cp blankon-live-build/build.sh live-build-workdir/build.sh
cd live-build-workdir
sudo ./build.sh https://github.com/BlankOn/blankon-live-build verbeek
```

## General Workflow

<img src="https://github.com/BlankOn/revival/blob/main/assets/images/blankon-live-build-workflow.png?raw=true"/>

## Configuration Overview

- `config/binary` - Configuration that represent the compiled binary of the ISO
- `config/boostrap` - Anything that related to bootstrap
- `config/chroot` - Anything that related to chroot.
- `config/common` - Distribution and package related
- `config/source` - Package source configuration
- `config/archives/blankon.key.binary` and `config/archives/blankon.key.chroot` - Initially the `lb` will try to use the keyring package from `--keyring-packages blankon-keyring` argument, but in some cases, especially in chroot reconfiguration, these package is still missing and these `blankon.key.*` will serve as backup.
- `config/bootloaders` - Boot loader configuration, currently using GRUB with custom background (`config/bootloaders/grub-legacy/splash.xpm.gz`)
- `config/hooks` - Higher number will override the smaller ones.
  - `config/hooks/live` - The hooks that will be executed when the OS booted in live session.
  - `config/hooks/normal` - The hooks that will be executed during the ISO build process
- `config/package-lists/live.list.chroot` - The list of the package that will be prepared for live session. There are live session specific packages listed here like `live-boot`, `live-config`, `live-config-systemd`

## Hooks vs Packaging

Small modifications that related to system wide will be maintained in `config/hooks`. If a modification is heavily tied to certain packages, then these packages should be patched and repackaged into development repository instead.

Example:
- We should alter the distribution name and remove irrelevant files in `/etc` that may cause problems --> Alter them with hook.
- We have broken LibreOffice package that could be solved with small file modification. --> Patch LibreOffice and repackage through IRGSH.

## What are important to setup / package / modify

- Branding
  - `/etc/os-release`
  - About interface in GNOME's Settings
  - Default GRUB background
  - Default desktop background / wallpaper
  - Installer customization
- Default apps
- System behaviours
  - Default services
- System convention
  - Default font, etc
 
## What to test

- ?

## Trouble Shooting Guides

### Failed to chroot
The error that gene
```
I: Extracting tzdata...
I: Extracting usr-is-merged...
I: Extracting util-linux...
I: Extracting zlib1g...
W: Failure trying to run: chroot "/home/user/iso/workdir/chroot" /bin/true
W: See /home/user/iso/workdir/chroot/debootstrap/debootstrap.log for details
E: An unexpected failure occurred, exiting...
Done in 00:00:43.
```
debootstrap.log does not give much clue either,
```
mknod: /home/user/iso/build/chroot/dev/null: No such file or directory
mknod: /home/user/iso/build/chroot/dev/zero: No such file or directory
mknod: /home/user/iso/build/chroot/dev/full: No such file or directory
mknod: /home/user/iso/build/chroot/dev/random: No such file or directory
mknod: /home/user/iso/build/chroot/dev/urandom: No such file or directory
mknod: /home/user/iso/build/chroot/dev/tty: No such file or directory
mknod: /home/user/iso/build/chroot/dev/console: No such file or directory
chroot: failed to run command ‘/bin/true’: No such file or directory
```

The actual cause lies on missing `base-files` package. This package is blacklisted from repo. So, when we rebuild our repository (usually after corrupted incident), there will be no `base-files` package.

Solution: package your `base-files` first to our repository. Please see blacklisted packages here: [https://github.com/BlankOn/revival/edit/main/Packages/MaintainerPackageList.md](https://github.com/BlankOn/revival/blob/main/Packages/MaintainerPackageList.md)

### Missing blankon-keyring package

The error generated from live-build:
```
Reading package lists...
Reading package lists...
Building dependency tree...
E: Unable to locate package blankon-keyring
E: An unexpected failure occurred, exiting...
P: Begin unmounting filesystems...
P: Saving caches...
Reading package lists...
Building dependency tree...
Done in 00:01:56.
```

Solution:

You need to build the `blankon-keyring` package first:
- https://github.com/BlankOn/revival/blob/main/Infrastructure/Keyring.md
- https://github.com/blankon-packages/blankon-keyring/

Then inject it to repository.
```
sudo GNUPGHOME=/var/lib/irgsh/gnupg reprepro -v -v -v --nothingiserror --component main includedeb verbeek ./blankon-keyring/blankon-keyring_2020.11.25-4.5*.deb
```

# References

- https://www.debian.org/devel/debian-live/
- https://live-team.pages.debian.net/live-manual/html/live-manual/index.en.html
- From BlankOn wiki: https://github.com/BlankOn/wiki/blob/master/TimPengembang/Pemaket/LiveBuild.md
