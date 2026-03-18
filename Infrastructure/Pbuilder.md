# Pbuilder

Pbuilder is the underlying isolated build system inside the irgsh-builder. It depends on the `base.tgz`

## Troubleshooting guide

### DNS Issue

The chroot works for most domains but failed to recognize `proxy.golang.org`, which needed to build go-based source code.

As stated in issue https://github.com/BlankOn/revival/issues/43, we don't have solid solution yet. Mounting resolv.conf directly will lead to unmounting failure later. So far as workaround we need to modify the `base.tgz` manually using `pbuilder` command.

1. Make sure that your `base.tgz` is located in the proper place `/var/cache/pbuilder/base.tgz`
2. Extract and enter the chroot env `sudo pbuilder login --save-after-login`
3. Once you enter the shel, modify the DNS in `/etc/resolv.conf`
```
nameserver 1.1.1.1
nameserver 8.8.8.8
```
5. Exit the shell, `pbuilder` will automaticalyl save your changes and rebuild the tarball.
