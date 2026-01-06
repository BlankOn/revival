# Release Cycle

## Repository Initialization and Sync

Please refer to IRGSH documentation.

## Live Build configuration

Please refer to https://github.com/BlankOn/revival/blob/main/DebianLiveBuild.md

## Alpha

Requirement / metric:
1. The distro can be installed.
2. Branding is not a priority.

## Beta

Requirement / metric:
1. The distro can be installed.
2. Branding should be done.
3. There may be some bugs.
4. Old release could be upgraded to new release without problem.

## Release Candidate

At this point, we need to freeze the development repo (arsip-dev) by disabling synchronization against Sid. This will ensure no more bugs are introduced by new packages from Sid.

Requirement / metric:
1. The distro can be installed.
2. Branding should be done.
3. No or minimum bug on the core apps.
4. Old release could be upgraded to new release without problem.
5. Tested thoughtfully by core contributors.

## Sync arsip-dev.blankonlinux.id to arsip.blankonlinux.id

The target repo should be fully synced with reprepro metadata being carried. It should be able to be remanaged with reprepro to inject security update later.

## Release

Requirement / metric:
1. The distro can be installed.
2. Branding should be done.
3. No or minimum bug on the core apps.
4. Change repository address from arsip-dev.blankonlinux.id to arsip.blankonlinux.id.
5. Tested thoughtfully on many devices and by public users.

## Fixes Release

If there is an important bug or security issues within the package in the ISO images, then we will release new ISO image with minor version being increased, e.g. from v12.0 to v12.1.
## Start over a new arsip-dev for the next release

Back to `Repository Initialization and Sync` section. Repeat.

---

## Support

Default support is 1 year (12 months). This can be extended until the next release is ready. The support will remain 1 year even if the next release is released before 1 year has elapsed.
