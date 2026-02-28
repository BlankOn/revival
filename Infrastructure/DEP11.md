# DEP11

## Appstream Generator

### Configuration

Configuration could be written in a JSON file like this, `asgen-config.json`:
```
{
    "ProjectName": "BlankOn",
    "Backend": "debian",
    "MetadataType": "YAML",
    "OriginName": "blankon",
    "MediaBaseUrl": "https://arsip-dev.blankonlinux.id/media",
    "HtmlBaseUrl": "https://arsip-dev.blankonlinux.id/report",
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

Generate command will try to scan your repository then put the generated data under `./db/` directory.

```
sudo appstream-generator -c asgen-config.json run verbeek main
```

### Publish

To make it ready to be integrated with the repository, you need to publish/export it, the result will be written under `./export/` directory,

```
sudo appstream-generator -c asgen-config.json publish verbeek main
```

### Integration

#### Injecting and resigning into Reprepro

Use the integrator script from this repository: `https://github.com/BlankOn/asgen2reprepro` to inject the generated appstream metadata to reprepro repository.

```
sudo ./integrate-dep11.sh  --basedir /var/lib/asgen/workspace --distributions /var/lib/irgsh/repo/verbeek/conf/distributions --dist /var/lib/irgsh/repo/verbeek/www/dists/verbeek --gpg-key 4ED6DAC2513877832D7B16838E50AD1822A85905
```

#### Web services

There are two directories under `export` output that need to be exposed as web service:
- `media` to be served under `MediaBaseUrl`, which is pointed at `https://arsip-dev.blankonlinux.id/media`
- `html` to be served under `HtmlBaseUrl`, which is pointed at `https://arsip-dev.blankonlinux.id/report`
