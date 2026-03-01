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
sudo ./integrate-dep11.sh  --basedir /var/lib/asgen/workspace --distributions /var/lib/irgsh/repo/verbeek/conf/distributions --dist /var/lib/irgsh/repo/verbeek/www/dists/verbeek --gpg-key 4ED6DAC2513877832D7B16838E50AD1822A85905
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
