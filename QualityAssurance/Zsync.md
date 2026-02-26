# Zsync

Zsync is a tool to let us donwload just the delta of the latest ISO file, compared to our previous download. This can save a lot of bandwith and time in our test cycle.

1. Download the full ISO first and make sure the downloaded file name is remain `blankon-live-image-amd64.hybrid.iso`
2. Anytime there is an update in Jahitan, you can just run this command from the same directory,

```
zsync http://jahitan.blankonlinux.id/current/blankon-live-image-amd64.hybrid.iso.zsync
```

3. It will try to download just the difference. As seen in this screenshot, zsync will just download 10-11% of the ISO instead of the whole ISO.

<img width="738" height="210" alt="image" src="https://github.com/user-attachments/assets/e501e396-73fb-40a9-974c-0ecd74d2a733" />
