# Installing arch linux and how make it to useable

Tutorial ini ditulis sebagai dokumentasi pribadi dalam menginstal arch linux dan beberapa setup untuk membuatnya menjadi lebih mudah digunakan. Meskipun demikian, tutorial ini semoga membantu dalam menginstal arch linux.

Sebelum memulai,
berikut adalah penjelasan tentang setup yang saya pakai:

- proses instal dengan sistem bios/mbr
- membuat partisi /home sendiri
- dual boot windows dan arch yang sekarang sedang akan di instal
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
Pada `namawifi` ganti dengan `wlan0`

2. **Via usb tethering**

- Hubungkan android ke usb
- Aktifkan usb tethering di pengaturan android

Sebelum melanjutkan pastikan jaringan sudah terkoneksi dengan benar. untuk mengeceknya lakukan ping ke dns google:
```bash
ping 8.8.8.8
```
Tekan Ctrl+c untuk menghentikan proses ping

> ### Perbarui jam sistem

```bash
timedatectl set-ntp true
```
> ### Partisi

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

> penjelasan:\
/dev/sda4 adalah untuk partisi swap\
/dev/sda5 adalah untuk partisi /root\
/dev/sda6 adalah untuk partisi /home

untuk menerapkan perubahan pilih `[Write]` dan ketikan `yes` lalu tekan enter, untuk keluar pilih `[Quit]`

hasil pembagian pemartisian yg tadi kita buat dapat dilihat dengan perintah `lsblk`

> ### Formating

`tips:`
`gunakan perintah lsblk untuk melihat info drive`

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

> **Hosts**

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
> **Timezone**

```bash
ln -sf /usr/share/zoneinfo/Asia/Jakarta /etc/localtime
```

```bash
hwclock --systohc
```
> **Set licale**

```bash
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```
```bash
echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
```
```bash
locale-gen
```
> **Keymap**

```bash
echo "KEYMAP=us" > /etc/vconsole.conf
```
> **Root password**

Membuat password untuk root
```bash
passwd
```
> **Membuat user**

Ganti setiap `$username` dengan nama anda. Misalnya: `budi`
```bash
useradd -m -G wheel $username
```
membuat password untuk user. misalnya `123`
```bash
passwd $username
```

> **Sudoers**

```bash
nano /etc/sudoers
```
Cari dan hapus tanda pager `#` pada baris `# %wheel ALL=(ALL) ALL`
> **Grub**

```bash
sudo pacman -S grub base-devel linux-headers networkmanager wireless_tools wpa_supplicant dialog mtools dosfstools os-prober ntfs-3g xdg-user-dirs
```
```bash
grub-install --target=i386-pc /dev/sda
```
```bash
grub-mkconfig -o /boot/grub/grub.cfg
```
> **Reboot system**

```bash
exit
umount -a
reboot
```
Setelah reboot, login sebagai user. Dalam contoh ini nama usernya adalah `budi` dan dengan passwd `123`

> **aktifkan internet**

1. network manager

```bash
systemctl start NetworkManager
```
```bash
systemctl enable NetworkManager
```
```bash
nmtui
```
Akan mucul jendela nmtui, Pilih `Activate a connection`, cari dan pilih wifi lalu masukan password wifinya. untuk keluar pilih `back` lalu `ok`

2. usb tethering

- Hubungkan android ke usb
- Aktifkan usb tethering di pengaturan android

Sebelum melanjutkan pastikan jaringan sudah terkoneksi dengan benar. untuk mengeceknya lakukan ping ke dns google:
```bash
ping 8.8.8.8
```
Tekan Ctrl+c untuk menghentikan proses ping

## Install kde (minimal)

Sampai disini bisa dikatakan kita telah selesai install arch linux. namun belum dapat digunakan semestinya distro pada umumnya, dan masih sulit untuk di operasikan. 

Nah, Untuk membuatnya menjadi lebih mudah digunakan maka kita perlu memasang desktop environmen. dan kali ini desktop yg akan kita gunakan adalah kde. Karena selain tampilannya kece, ia juga mudah di kustomisasi. 

Dari banyaknya desktop, kde adalah desktop yg canggih menurut saya dan dirasakan juga oleh user lain.

> **X server**

```bash
sudo pacman -S xorg xorg-server xorg-xinit
```
> **Display manager**

```bash
$ sudo pacman -S sddm sddm-kcm
```
```bash
systemctl enable sddm
```
> **paket kde minimal**

```bash
sudo pacman -S plasma-desktop plasma-nm plasma-pa kdeplasma-addons kde-system-meta breeze breeze-grub
```
```bash
sudo pacman -S bluedevil gst-libav dolphin dolphin-plugins discover appstream appstream-qt apckagekit packagekit-qt5 kdialog
```
```bash
sudo pacman -S konsole yakuake kfind kdeconnect ark gwenview spectacle vlc kinfocenter khelpcenter plasma-systemmonitor
```
