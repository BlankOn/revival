# IRGSH Trouble Shooting Guides

## Builder

### Some dependencies are not found in repository

<img width="1280" height="378" alt="image" src="https://github.com/user-attachments/assets/37ed692d-2ee3-4218-8c32-77dd6b8c6521" />
<img width="1280" height="378" alt="image" src="https://github.com/user-attachments/assets/cfd5762c-a403-4267-9545-834a4c5bfb3f" />

```
Get: 75 http://kartolo.sby.datautama.net.id/debian sid/main amd64 python3-zipp all 3.23.0-1 [11.0 kB]
Get: 76 http://kartolo.sby.datautama.net.id/debian sid/main amd64 python3-setuptools all 78.1.1-0.1 [738 kB]
Get: 77 http://kartolo.sby.datautama.net.id/debian sid/main amd64 python3-distutils-extra all 3.2 [17.1 kB]
Fetched 22.2 MB in 2s (9471 kB/s)
E: Failed to fetch http://kartolo.sby.datautama.net.id/debian/pool/main/p/python3-defaults/python3-minimal_3.13.9-2_amd64.deb: 404  Not Found [IP: 123.255.202.74 80]
E: Unable to fetch some packages; try '-o APT::Get::Fix-Missing=true' to continue with missing packages
Reading package lists...
Building dependency tree...
Reading state information...
Reading extended state information...
Initializing package states...
Writing extended state information...
Building tag database...
E: pbuilder-satisfydepends failed.
```
When build failed because of some missing dependencies from upstream repository, then we need to update `base.tgz` and Docker image of `pbuilder`, with this steps:
```
sudo irgsh-builder update-base
sudo irgsh-builder init-builder
```

This will update the repository index of the `base.tgz`, which will be used as chroot environment for building packages.

