# Installing arch linux and how make it to useable

Tutorial ini ditulis sebagai dokumentasi pribadi dalam menginstal arch linux dan beberapa setup untuk membuatnya menjadi lebih mudah digunakan. Meskipun demikian, tutorial ini semoga membantu dalam menginstal arch linux.

Sebelum memulai,
berikut adalah penjelasan tentang setup yang saya pakai:

- proses instal dengan sistem bios/mbr
- membuat partisi /home sendiri
- dual boot windows dan arch yang sekarang sedang akan di instal
- pembagian partisi menggunakan gparted live xubuntu
- menggunakan desktop kde plasma (minimal)

## Pemasangan base linux
> ### Menghubungkan ke internet

1. **Via iwctl**

```bash
# iwctl
[iwd]# device list
[iwd]# station device scan
[iwd]# station device get-connect
[iwd]# station device connect SSID
```
2. **Via usb tethering**

```bash
* Hubungkan android ke usb
* Aktifkan usb tethering di pengaturan android
```
