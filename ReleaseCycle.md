# Release Cycle

## 0. Repository Initialization and Sync

Please refer to IRGSH documentation.

## 1. Live Build configuration

Please refer to https://github.com/BlankOn/revival/blob/main/DebianLiveBuild.md

## 2. Alpha Release

We may have multiple alpha release, like Alpha 1, Alpha 2.

Requirement / metric:
1. The distro can be installed.
2. Branding is not a priority.

## 3. Beta Release

We may have multiple beta release, like Beta 1, Beta 2.

Requirement / metric:
1. The distro can be installed.
2. Branding should be done.
3. There may be some bugs.
4. Old release could be upgraded to new release without problem.

## 4. Release Candidate (RC)

At this point, we need to freeze the development repo (arsip-dev) by disabling synchronization against Sid. This will ensure no more bugs are introduced by new packages from Sid. We may have multiple RC release, like RC 1, RC 2.

Requirement / metric:
1. The distro can be installed.
2. Branding should be done.
3. No or minimum bug on the core apps.
4. Old release could be upgraded to new release without problem.
5. Tested thoughtfully by core contributors.

## 6. Sync arsip-dev.blankonlinux.id to arsip.blankonlinux.id

### Internal

The target repo should be fully synced with reprepro metadata being carried. It should be able to be remanaged with reprepro to inject security update later.

### External

Coordinate with mirror maintainers to sync our new repository.

## 7. Final Release

Requirement / metric:
1. The distro can be installed.
2. Branding should be done.
3. No or minimum bug on the core apps.
4. Change repository address from arsip-dev.blankonlinux.id to arsip.blankonlinux.id.
5. Tested thoughtfully on many devices and by public users.

## 8. Fixes Release

If there is an important bug or security issues within the package in the ISO images, then we will release new ISO image with minor version being increased, e.g. from v12.0 to v12.1.

## 9. Start over a new arsip-dev for the next release

Back to `Repository Initialization and Sync` section. Repeat.

---

## Support

Default support is 1 year (12 months). This can be extended until the next release is ready. The support will remain 1 year even if the next release is released before 1 year has elapsed.
