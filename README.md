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
[iwd]# station namawifi scan
[iwd]# station namawifi get-connect
[iwd]# station namawifi connect SSID
```
Pada bagian `namawifi` ganti dengan `wlan0`

2. **Via usb tethering**

```bash
- Hubungkan android ke usb
- Aktifkan usb tethering di pengaturan android
```
Sebelum melanjutkan pastikan jaringan sudah terkoneksi dengan benar. untuk mengeceknya lakukan ping ke dns google:
```bash
ping 8.8.8.8
```

> ### Perbarui jam sistem

```bash
timedatectl set-ntp true
```
> ### Pemartisian

```bash
cfdisk /dev/sd?
```
Tanda "?" pada `/dev/sd?` bisa di sesuaikan pada komputer anda. Untuk mengeceknya bisa gunakan perintah `lsblk` dan disana kita akan mengetahui hardisk yg akan kita install adalah `sda` atau `sdb`.

selanjutnya silahkan lakukan pembagian pemartisian pada drive yg anda inginkan.
Misalnya pada `/dev/sda3` dengan size 100 GB akan di bagi menjadi 3 partisi untuk menginstall arch linux.

|   Drive   |   Size  |
|   -----   |   ----  |
| /dev/sda4 |   4GB   |
| /dev/sda5 |   30GB  |
| /dev/sda6 | sisanya |


