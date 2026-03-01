# DEP11 for Appstream Metadata

AppStream is a cross-distribution effort for enhancing the way we interact with the software repositories provided by (Linux) distributions by standardizing software component metadata, as stated in https://www.freedesktop.org/wiki/Distributions/AppStream/.

Currently, BlankOn use `reprepro` to manage its repository. By default `reprepro` does not support Appstream metadata but fortunately there is `appstream-generator` package that could help us to generate Appstream metadata from a repository. This document explains on how to generate ones then integrate and serve it together with the repository. The goal is to make sure our repository could be loaded by GNOME Software or KDE Discover. 

## Appstream Generator

### Configuration

Configuration could be written in a JSON file like this, `asgen-config.json`:
```
{
    "ProjectName": "BlankOn",
    "Backend": "debian",
    "MetadataType": "YAML",
    "OriginName": "blankon",
    "MediaBaseUrl": "https://arsip-dev.blankonlinux.id/dev/media",
    "HtmlBaseUrl": "https://arsip-dev.blankonlinux.id/dev/report",
    "WorkspaceDir": "/var/lib/asgen/workspace",
    "ArchiveRoot": "/var/lib/irgsh/repo/verbeek/www",
    "Suites": {
        "verbeek": {
            "dataPriority": 10,
            "baseSuite": "verbeek",
            "sections": ["main", "restricted", "extras", "restricted-firmware"],
            "architectures": ["amd64"]
        }
    }
}
```

### Generate

Generate command will try to scan your repository then put the generated data under `./db/` directory. This will take hours.

```
sudo appstream-generator -c asgen-config.json run verbeek
```

### Publish

To make it ready to be integrated with the repository, you need to publish/export it, the result will be written under `./export/` directory,

```
sudo appstream-generator -c asgen-config.json publish verbeek
```

### Integration

#### Injecting and resigning into Reprepro

We can't just copy the generated files into our reposotry, we need to include them to Release file as well and re-sign the Release file with the same GPG key that used to sign the repository. Use the integrator script from this repository: `https://github.com/BlankOn/asgen2reprepro` to properly inject the generated appstream metadata to reprepro repository.

```
sudo ./integrate.sh  --basedir /var/lib/asgen/workspace --distributions /var/lib/irgsh/repo/verbeek/conf/distributions --dist /var/lib/irgsh/repo/verbeek/www/dists/verbeek --gpg-key 4ED6DAC2513877832D7B16838E50AD1822A85905
```

#### Web services

There are two directories under `export` output that need to be exposed as web service:
- `media` to be served under `MediaBaseUrl`, which is pointed at `https://arsip-dev.blankonlinux.id/media`
- `html` to be served under `HtmlBaseUrl`, which is pointed at `https://arsip-dev.blankonlinux.id/report`

At the end, we need to serve them with this structure:

```
protocol://domain/path              
                    │               
                    ├──────► /media (from appstream-generator)
                    │               
                    │               
                    ├──────► /report (from appstream-generator)
                    │               
                    │               
                    ├──────► /dists (from reprepro, including dep11 files from appstream-generator and re-signed Release files)
                    │               
                    │               
                    └──────► /pool (from reprepro)

```

### Testing

#### Using asgen2reprepro check script

```
sudo ./check.sh --url http://arsip-dev.blankonlinux.id/dev --dist verbeek --arch amd64
```

Example output:
```
...
APT Integration Test
====================

  STATUS   CHECK                                                        DETAILS
  ------   -----                                                        -------

  1. APT Source Configuration
  [PASS]   Repository found in APT sources                              /etc/apt/sources.list

  2. APT Update
  Running sudo apt update...
  [PASS]   sudo apt update succeeded                                    
  [WARN]   APT fetched DEP-11 metadata                                  No DEP-11 lines in apt update output

  3. DEP-11 Files in APT Lists (/var/lib/apt/lists)
  [PASS]   Components-amd64.yml (verbeek/main)                          arsip-dev.blankonlinux.id_dev_dists_verbeek_main_dep11_Components-amd64.yml.gz (6490681 bytes)
  [PASS]   icons-48x48.tar (verbeek/main)                               arsip-dev.blankonlinux.id_dev_dists_verbeek_main_dep11_icons-48x48.tar.gz (2190092 bytes)
  [PASS]   icons-64x64.tar (verbeek/main)                               arsip-dev.blankonlinux.id_dev_dists_verbeek_main_dep11_icons-64x64.tar.gz (3434445 bytes)
  [PASS]   Components-amd64.yml (verbeek/restricted)                    arsip-dev.blankonlinux.id_dev_dists_verbeek_restricted_dep11_Components-amd64.yml.gz (159 bytes)
  [PASS]   icons-48x48.tar (verbeek/restricted)                         arsip-dev.blankonlinux.id_dev_dists_verbeek_restricted_dep11_icons-48x48.tar.gz (29 bytes)
  [PASS]   icons-64x64.tar (verbeek/restricted)                         arsip-dev.blankonlinux.id_dev_dists_verbeek_restricted_dep11_icons-64x64.tar.gz (29 bytes)
  [PASS]   Components-amd64.yml (verbeek/extras)                        arsip-dev.blankonlinux.id_dev_dists_verbeek_extras_dep11_Components-amd64.yml.gz (157 bytes)
  [PASS]   icons-48x48.tar (verbeek/extras)                             arsip-dev.blankonlinux.id_dev_dists_verbeek_extras_dep11_icons-48x48.tar.gz (29 bytes)
  [PASS]   icons-64x64.tar (verbeek/extras)                             arsip-dev.blankonlinux.id_dev_dists_verbeek_extras_dep11_icons-64x64.tar.gz (29 bytes)
  [PASS]   Components-amd64.yml (verbeek/restricted-firmware)           arsip-dev.blankonlinux.id_dev_dists_verbeek_restricted-firmware_dep11_Components-amd64.yml.gz (166 bytes)
  [PASS]   icons-48x48.tar (verbeek/restricted-firmware)                arsip-dev.blankonlinux.id_dev_dists_verbeek_restricted-firmware_dep11_icons-48x48.tar.gz (29 bytes)
  [PASS]   icons-64x64.tar (verbeek/restricted-firmware)                arsip-dev.blankonlinux.id_dev_dists_verbeek_restricted-firmware_dep11_icons-64x64.tar.gz (29 bytes)

  4. AppStream Cache (GNOME Software readiness)
  [PASS]   appstreamcli is installed                                    AppStream versi: 1.1.2
  Running sudo appstreamcli refresh --force...
  [PASS]   appstreamcli refresh succeeded                               
  [PASS]   AppStream catalog cache exists                               4 file(s) in /var/cache/swcatalog/cache
  [PASS]   Icon cache (verbeek/main)                                    blankon-verbeek-main/ (2051 file(s))
  [PASS]   Icon cache (verbeek/restricted)                              blankon-verbeek-restricted/ (0 file(s))
  [PASS]   Icon cache (verbeek/extras)                                  blankon-verbeek-extras/ (0 file(s))
  [PASS]   Icon cache (verbeek/restricted-firmware)                     blankon-verbeek-restricted-firmware/ (0 file(s))
  [PASS]   appstreamcli can find components                             io.github.lxqt.screengrab

Summary
=======
  Total checks: 155
  Passed: 149
  Failed: 0
  Warnings: 6

  Repository is compatible with DEP-11 appstream metadata.
```

#### Using GNOME Software

```
sudo appstreamcli refresh --force
gnome-software --quit
gnome-software --verbose
```
