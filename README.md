# Installing arch linux and how make it to useable

Tutorial ini ditulis sebagai dokumentasi pribadi dalam menginstal arch linux dan beberapa setup untuk membuatnya menjadi lebih mudah digunakan. Meskipun demikian, tutorial ini semoga membantu dalam menginstal arch linux.

Sebelum memulai,
berikut adalah penjelasan tentang setup yang saya pakai:

- proses instal dengan sistem bios/mbr
- membuat partisi /home sendiri
- dual boot windows dan arch yang sekarang sedang akan di instal
- pembagian partisi menggunakan gparted live xubuntu
- menggunakan desktop kde plasma (minimal)

## Configure
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

Sebelem melakukan permartisian alangkah baiknya kita mengetahui info tentang hardisk kita. Untuk mengeceknya bisa menggunakan perintah `lsblk` dan disana kita akan mengetahui hardisk yg akan kita install adalah `sda` atau `sdb`.

Kita asumsikan bahwa hardisk kita adalah yg `sda`. Maka silakan lakukan pembuatan partisi dengan perintah sebagai berikut:

```bash
cfdisk /dev/sda
```

Selanjutnya silakan lakukan pembagian pemartisian pada drive yg di inginkan.
Misalnya pada `/dev/sda3` dengan size 100 GB akan di bagi menjadi 3 partisi untuk menginstall arch linux.

|   Drive   |   Size  | Type: |
|   -----   |   ----  | ----- |
| /dev/sda4 |   4GB   | Linux swap / solaris |
| /dev/sda5 |  30GB   | Linux |
| /dev/sda6 | sisanya | Linux |

```
penjelasan: 
/dev/sda4 adalah untuk partisi swap
/dev/sda5 adalah untuk partisi /root
/dev/sda6 adalah untuk partisi /home
```

untuk menerapkan perubahan pilih `[Write]` dan ketikan `yes` lalu tekan enter, untuk keluar pilih `[Quit]`

hasil pembagian pemartisian yg tadi kita buat dapat dilihat dengan perintah `lsblk`

> ### Formating

```
tips:
gunakan perintah lsblk untuk melihat info drive
```

```bash
mkswap /dev/sda4
```
```bash
mkfs.ext4 /dev/sda5
```
```bash
mkfs.ext4 /dev/sda6
```

> ### Mount

```bash
swapon /dev/sda?
```
```bash
mount /dev/sda? /mnt
```
```bash
mkdir /mnt/home
```
```bash
mount /dev/sda? /mnt/home
```

> ### Set fstab

Jika proses pemartisian yakin telah diatur dengan benar, berikutnya lakukan `fstab`

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

## Install base
> **Pacstrap**

```bash
pacstrap /mnt base linux linux-firmware nano
```
> **Chroot**

```bash
arch-chroot /mnt
```
> **Hostnane**

```bash
echo '$hostname' > /etc/hostname
```
`$hostname` ganti dengan nama yg anda inginkan untuk komputer/host. misalnya kita ganti dengan `myarch`

> **hosts**

```bash
nano /etc/hosts
```
dan isi seperti berikut:
```bash
127.0.0.1   localhost
::1         localhost
127.0.1.1   $hostname.localdomain   $hostname
```
ganti semua `$hostname` dengan nama yg telah diberikan sebelumnya untuk komputer/host yaitu `myarch` sehingga menjadi seperti berikut:
```bash
127.0.1.1   myarch.localdomain    myarch
```

```bash
```

```bash
```
