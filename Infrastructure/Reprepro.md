# Reprepro

## Sync Procedure (pull from upstream)

### 0. Make sure that we have enough free space

Available free space should be more than half of the current repository size. If you are adding more architecture, then you need twice the current size.

### 1. We should blacklist some packages, especially that related to rebranding. Please edit `cat conf/blacklist.pkg` file in this format:
```
base-files hold
desktop-base hold
```

- `purge` — the upstream package is never pulled into your repo at all
- `hold` — the upstream package is pulled once, then frozen at that version and won't be updated on subsequent syncs

### 2. Take a snapshot first as backup

```
cd /var/lib/irgsh/repo/verbeek/ && sudo GNUPG_HOME=/var/lib/irgsh/gnupg reprepro gensnapshot verbeek 20260219-1255
```
The snapshot is located in `www/dists/verbeek//snapshots/`

### 3. Sync

It will take hours to complete.
```
cd /var/lib/irgsh/repo/verbeek && sudo irgsh-repo -c /etc/irgsh/config.yaml sync
```

### 4. In case of disaster

Try to rollback,
```
cd /var/lib/irgsh/repo/verbeek/ && sudo GNUPG_HOME=/var/lib/irgsh/gnupg reprepro restore verbeek 20260219-1255 '*'
```

## Repair reprepro database

It usually happens when the inject/export process is terminated in the middle, which causes the database to become corrupted.

The error,
```
##### Injecting the deb files from artifact to the repository
##### RUN mkdir -p /var/lib/irgsh/repo/verbeek && cd /var/lib/irgsh/repo/verbeek/ && \
	GNUPGHOME=/var/lib/irgsh/gnupg reprepro -v -v -v --nothingiserror --component main includedeb verbeek /var/lib/irgsh/repo/artifacts/2026-02-21-115501_cc3d4514-68eb-4703-ac0b-0102b6e128f0_DCE16C7A2805D4F8FCFF2C40FDF9557305CC097B_praya-gnome-shell-extension/*.deb
Internal error of the underlying BerkeleyDB database:
Within references.db subtable references at put: BDB0067 DB_KEYEXIST: Key/data pair already exists
Internal error of the underlying BerkeleyDB database:
Within references.db subtable references at put: BDB0067 DB_KEYEXIST: Key/data pair already exists
There have been errors!
[ REPO FAILED ] Failed to inject deb files: exit status 255
```

### Solution #1: 

You can repair it with check, it will scan the whole paths and reindex the checksum (if missing).
```
cd /var/lib/irgsh/repo/verbeek
sudo GNUPG_HOME=/var/lib/irgsh/gnupg reprepro -Vb . check
```

### Solution #2: Rebuild the DB

```
mv db db_bak
sudo GNUPGHOME=/var/lib/irgsh/gnupg reprepro -Vb . check && sudo GNUPGHOME=/var/lib/irgsh/gnupg reprepro -v -v -v export
```

Sync against upstream if necessary.

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
