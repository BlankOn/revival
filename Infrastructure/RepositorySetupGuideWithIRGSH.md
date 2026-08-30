# Repository Setup Guide with IRGSH

This document will guide you on how to prepare and setup a brand new repository for development purpose.

## Prerequisites

1. Redis

## The Setup

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
$ sudo ln -s /home/user/.gnupg gnupg
```

Then adjust the ownership of the entire workign directory
```
$ sudo chown -R irgsh:irgsh /var/lib/irgsh
```

Confirm if it is accessible,
```
$ GNUPGHOME=/var/lib/irgsh/gnupg gpg --list-secret-keys
/var/lib/irgsh/gnupg/pubring.kbx
--------------------------------
sec   ed25519 2025-12-15 [SC]
      4ED6DAC2513877832D7B16838E50AD1822A85905
uid           [ unknown] BlankOn
```
### 4. Configuration

```
sudo cat /etc/irgsh/config.yaml
---
redis: 'redis://localhost:6379'

monitoring:
  enabled: true
  heartbeat_interval: 30       # Heartbeat every 30 seconds
  instance_timeout: 90          # Mark offline after 90 seconds
  cleanup_interval: 3600        # Cleanup every hour (instances removed after 24h)

notification:
  webhook_url: ''              # Webhook URL for job notifications (leave empty to disable)

storage:
  database_path: '/var/lib/irgsh/chief/irgsh.db'  # SQLite database for job persistence
  max_jobs: 1000               # Maximum number of build jobs to retain
  max_iso_jobs: 200            # Maximum number of ISO jobs to retain

chief:
  address: 'http://localhost:8080'
  workdir: '/var/lib/irgsh/chief'
  gnupg_dir: '/var/lib/irgsh/gnupg'

builder:
  workdir: '/var/lib/irgsh/builder'
  upstream_dist_codename: 'trixie'
  upstream_dist_url: 'http://kartolo.sby.datautama.net.id/debian'

repo:
  workdir: '/var/lib/irgsh/repo'
  dist_name: 'BlankOn'
  dist_label: 'BlankOn'
  dist_codename: 'verbeek'
  dist_components: 'main restricted extras restricted-firmware'
  dist_supported_architectures: 'amd64 source'
  dist_version: '12.0'
  dist_version_desc: 'BlankOn Linux 12.0 Verbeek'
  dist_signing_key: 'B98DEB8991B7855069F7B8A638DB8B8C04CAB9C9'
  upstream_name: 'merge.trixie'
  upstream_dist_codename: 'trixie'
  upstream_dist_url: 'http://kartolo.sby.datautama.net.id/debian'
  upstream_dist_components: 'main non-free>restricted contrib>extras non-free-firmware>restricted-firmware'
  gnupg_dir: '/var/lib/irgsh/gnupg'

iso:
  workdir: '/var/lib/irgsh/iso'
  outputdir: '/tmp/jahitan'
  public_base_url: 'http://jahitan.blankonlinux.id'
```

### 5. Initialize the repository

```
$ sudo irgsh-repo -c /etc/irgsh/config.yaml init
2026/08/30 12:13:22 config.go:88: load config from :  /etc/irgsh/config.yaml
? Are you sure you want to initialize new repository? Any existing distribution will be flushed.? [y/N] y
```

### 6. Sync with upstream

```
$ sudo irgsh-repo -c /etc/irgsh/config.yaml sync
```
