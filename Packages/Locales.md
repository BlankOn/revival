# Locales

## Live-build

1. Locale data — locales-all package (in gnome-core.list.chroot) provides pre-compiled locale data including id_ID.UTF-8
2. Translation packages — task-indonesian-desktop (in desktop.list.chroot) pulls in Indonesian translations for GNOME and common desktop apps
3. Default locale setting — the hook we just edited sets LANG=id_ID.UTF-8 in /etc/default/locale, which GNOME reads at session start to determine the UI language

## Weblate

Weblate Integration (post-merge, done by maintainer)
1. Go to https://hosted.weblate.org/ and create a component for the praya repo
2. Configure: file format = GNU gettext PO, mask = po/*.po, template = po/praya.pot, languages file = po/LINGUAS
3. Translators contribute via Weblate web UI, which creates PRs back to the repo
