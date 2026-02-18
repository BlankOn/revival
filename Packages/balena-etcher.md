# balena-etcher

Official debian package from Balena Etcher require different package name than what we have in the repository:`libgdk-pixbuf2.0-0` vs `libgdk-pixbuf-2.0-0`. Which leads to this error:

```
dpkg: dependency problems prevent configuration of balena-etcher:
 balena-etcher depends on libgdk-pixbuf2.0-0; however:
  Package libgdk-pixbuf2.0-0 is not installed
```

We could resolve this by creating a dummy transitional package:
```
sudo apt install equivs
mkdir -p /tmp/equivs && cd /tmp/equivs

cat <<EOF > libgdk-pixbuf2.0-0
Package: libgdk-pixbuf2.0-0
Version: 99
Depends: libgdk-pixbuf-2.0-0
Description: Dummy transitional package for balena-etcher
EOF

equivs-build libgdk-pixbuf2.0-0
sudo dpkg -i libgdk-pixbuf2.0-0_99_all.deb
```
