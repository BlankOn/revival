# Reprepro

## Sync Procedure (pull from upstream)

### 1. We should blacklist some packages, especially that related to rebranding. Please edit `cat conf/blacklist.pkg` file in this format:
```
base-files hold
desktop-base hold
```

- `purge` — the upstream package is never pulled into your repo at all
- `hold` — the upstream package is pulled once, then frozen at that version and won't be updated on subsequent syncs

### 2. Take a snapshot first as backup

```
cd /var/lib/irgsh/repo/verbeek/ && sudo GNUPG_HOME=/var/lib/irgsh/gnupg reprepro gensnapshot verbeek snapshot_name_with_date
```

### 3. Sync

It will take hours to complete.
```
cd /var/lib/irgsh/repo/verbeek && sudo irgsh-repo -c /etc/irgsh/config.yaml sync
```

### 4. In case of disaster

Try to rollback,
```
cd /var/lib/irgsh/repo/verbeek/ && sudo GNUPG_HOME=/var/lib/irgsh/gnupg reprepro copysnapshot verbeek verbeek snapshot_name_with_date
```

## Force remove a particular package

```
cd /var/lib/irgsh/repo/verbeek
sudo GNUPG_HOME=/var/lib/irgsh/gnupg reprepro remove verbeek package_name version_but_optional
```

Then reindex.

## Reindex

```
cd /var/lib/irgsh/repo/verbeek/ && sudo GNUPG_HOME=/var/lib/irgsh/gnupg reprepro -v -v -v export
```

## Check specific package via chroot

```
sudo chroot chroot apt update
sudo chroot chroot apt-cache policy linux-image-amd64

linux-image-amd64:
  Installed: (none)
  Candidate: 6.17.13-1
  Version table:
     6.17.13-1 500
        500 http://100.71.100.14/dev verbeek/main amd64 Packages
```
