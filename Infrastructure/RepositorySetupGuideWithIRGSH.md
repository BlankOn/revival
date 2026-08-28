# Repository Setup Guide with IRGSH

This document will guide you on how to prepare and setup a brand new repository for development purpose.

### 1. Prepare the dependencies and getting the executable binaries of IRGSH

Please consult to https://github.com/blankon/irgsh-go for documentation.

Once setup, you may have `/var/lib/irgsh` directory as the main space for working with irgsh

### 2. Setup the keyring

The keyring will be derived from a GPG private key. This private key will be the primary security mechanism for this Linux distribution. Failure to secure this key will compromise the security of the entire distribution.

Currently, the private key is stored on the repository server. Some maintainers should also keep a secure backup of this key so that they can rebuild the repository in the event of a disaster.

Please refer to this documentation on how to generate one: https://blankonlinux.id/en/wiki/infrastructure/keyring

### 3. Setup the IRGSH with keyring

Let's assume that you have generated the key pair for the keyring.

```
$ gpg --list-secret-keys
/home/user/.gnupg/pubring.kbx
-----------------------------
sec   ed25519 2025-12-15 [SC]
      4ED6DAC2513877832D7B16838E50AD1822A85905
uid           [ unknown] BlankOn <dev@blankon.id>
ssb   cv25519 2025-12-15 [E]
```

To make it easier for IRGSH to work with these keys, let's symlink it to `/var/lib/irgsh`

```
$ 
```


### 4. Sync with upstream
