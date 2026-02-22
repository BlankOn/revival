# Maintainer Package List

To blacklist the package, see https://github.com/BlankOn/revival/blob/main/Infrastructure/Reprepro.md

## base-files (reprepro blacklist)

```
irgsh-cli package --package https://github.com/blankon-packages/base-files.git --package-branch master --ignore-checks
```

## desktop-base (reprepro blacklist)

```
irgsh-cli package --package https://github.com/blankon-packages/desktop-base.git --package-branch master --ignore-checks
```

## blankon-keyring

Because IRGSH depends on the keyring as well, this package need to be build and injected to repository manually.

```
sudo GNUPGHOME=/var/lib/irgsh/gnupg reprepro -v -v -v --nothingiserror --component main includedeb verbeek ./blankon-keyring/blankon-keyring_2020.11.25-4.5*.deb
```

Please see:
- https://github.com/BlankOn/revival/blob/main/Infrastructure/Keyring.md
- https://github.com/blankon-packages/blankon-keyring/

## plymouth-theme-blankon

```
irgsh-cli package --package https://github.com/blankon-packages/plymouth-theme-blankon.git --package-branch master --ignore-checks
```

## blankon-wallpapers

## calamares-settings-blankon

## praya-gnome-shell-extension

```
irgsh-cli package --package https://github.com/blankon/praya-gnome-shell-extension.git --package-branch main --ignore-checks
```

## praya-service

## tilingshell-gnome-shell-extension
