# Penerbitan Kunci Keyring

Kunci Keyring adalah kunci GPG yang digunakan untuk 2 hal:
1. Menandatangani paket sebelum dimasukkan ke dalam repo.
2. Menjadi kunci publik yang dikenali oleh berbagai pihak yang berurusan dengan repository / lumbung paket.

## Penerbitan kunci GPG

Terbitkan kunci GPG seperti biasa dengan perkakas `gpg`. Setel waktu kadaluarsa yang panjang (misal 10 tahun) atau tanpa masa kadaluarsa sama sekali.

## Ekspor

```
user@irgsh-repo:~$ gpg --list-key
/home/user/.gnupg/pubring.kbx
-----------------------------
pub   ed25519 2025-12-15 [SC]
      4ED6DAC2513877832D7B16838E50AD1822A85905
uid           [ultimate] BlankOn <dev@blankon.id>
sub   cv25519 2025-12-15 [E]

user@irgsh-repo:~$ gpg --output blankon-archive-keyring.gpg --export 4ED6DAC2513877832D7B16838E50AD1822A85905
```

## Pemaketan

Bawa kunci publiknya (dalam bentuk binary0 ke paket `blankon-keyring` lalu dipaketkan ke https://github.com/blankon-packages/blankon-keyring/tree/master

## Penggunaan

Setelah berhasil dibangun, paket ini diperlukan untuk dipasang saat berurusan dengan `debootstrap` dan pembangunan ISO dengan `live-build`.
