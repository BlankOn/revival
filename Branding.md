# Branding

This document needs to be improved to be a guide on how to do the rebranding, not only a list of what to rebrand.

## Operating System Identity and Profile

- `base-files` - Please take a look at https://github.com/blankon-packages/base-files/ ✅ by Han
  - GNOME Setttings's About Page
    - Please check Operating System Identity and Profile section above
  - GNOME Settings's logo (still using Debian)
- Repositories - Please see the configuration in https://github.com/BlankOn/blankon-live-build/ - low prio

## Live System
1. There are some rebranding attempts in live-build (https://github.com/BlankOn/blankon-live-build), including:
  - The GRUB background: `config/includes.chroot/usr/share/backgrounds/blankon/grub-splash.svg`
  - Distro branding in:
    - `config/binary`
    - `config/bootstrap

## Installer

1. Calamares icon
2. Debian icon in Calamares
3. Color/background in Calamares sidebar
4. Debian-related string in Calamares
5. ?

## Desktop
1. `desktop-base` (https://github.com/blankon-packages/desktop-base) - Distro specific configuration for desktop, like wallpaper, logo, default user profile, etc. ✅ by Han
  - `verbeek-theme` - Debian has multiple themes in this package to represent their releases including the legacy ones. We just need to maintain one theme for current release. They are including:
    - Default wallpaper: `verbeek-theme/wallpaper`
    - GRUB related theme / background image: `verbeek-theme/grub`
    - GDM: `verbeek-theme/login`
  - `defaults`
    - Default profile picture: defaults/common/etc/skel/.face (in SVG format)
