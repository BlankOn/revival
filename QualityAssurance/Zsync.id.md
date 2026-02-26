# Zsync

Zsync adalah alat untuk memungkinkan kita mengunduh hanya bagian perubahan (delta) dari file ISO terbaru dibandingkan dengan unduhan sebelumnya. Hal ini dapat menghemat banyak bandwidth dan waktu dalam siklus pengujian kita.

1. Unduh ISO lengkap terlebih dahulu dan pastikan nama file hasil unduhan tetap blankon-live-image-amd64.hybrid.iso.
2. Setiap kali ada pembaruan di Jahitan, Anda cukup menjalankan perintah ini dari direktori yang sama:
```
zsync http://jahitan.blankonlinux.id/harian/current/blankon-live-image-amd64.hybrid.iso.zsync
```

3. Zsync akan mencoba mengunduh hanya bagian yang berbeda saja. Seperti terlihat pada tangkapan layar, zsync hanya mengunduh sekitar 10–11% dari file ISO, bukan seluruh file ISO.

<img width="738" height="210" alt="image" src="https://github.com/user-attachments/assets/e501e396-73fb-40a9-974c-0ecd74d2a733" />
