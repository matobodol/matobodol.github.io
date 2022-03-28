# Install arch linux dan menjadikannya mudah untuk digunakan

Tutorial ini ditulis sebagai dokumentasi pribadi dalam menginstal arch linux dan beberapa setup untuk membuatnya menjadi lebih mudah untuk digunakan. Meskipun demikian, tutorial ini semoga membantu pemula dalam menginstal arch linux.

Sebelum memulai,
berikut adalah penjelasan tentang setup yang saya pakai:

- proses instal dengan sistem bios/mbr
- membuat partisi /home sendiri
- dual boot windows dan arch yang sekarang sedang akan di instal
- menggunakan desktop kde plasma (minimal)

## Configure

> **Menghubungkan ke internet**

- Via iwctl

```bash
# iwctl
[iwd]# device list
[iwd]# station $wificard scan
[iwd]# station $wificard get-connect
[iwd]# station $wificard connect $SSID
```
Pada `$wificard` ganti dengan `wlan0` atau sesuakian pada keluaran `device list` dan untuk $SSID ganti dengan target wifi untuk di sambungkan.

- Via usb tethering

`- Hubungkan android ke usb`\
`- Aktifkan usb tethering di pengaturan android`

Sebelum melanjutkan pastikan jaringan sudah terkoneksi dengan benar. untuk mengeceknya lakukan ping ke dns google:
```bash
ping 8.8.8.8
```
Untuk menghentikan proses ping tekan Ctrl+c

> **Perbarui jam sistem**

```bash
timedatectl set-ntp true
```
> **Partisi**

Sebelum melakukan pemartisian alangkah baiknya kita mengetahui info tentang hardisk kita. Untuk mengeceknya bisa menggunakan perintah `lsblk` dan disana kita akan mengetahui hardisk yg kita gunakan apakah `sda` atau `sdb`.

Kita asumsikan bahwa hardisk kita adalah yg `sda`. Maka silakan lakukan pembuatan partisi dengan perintah sebagai berikut:

```bash
cfdisk /dev/sda
```

Selanjutnya lakukan pembagian partisi pada drive yg di inginkan. Misalnya pada `/dev/sda4` dengan size 100GB akan di bagi menjadi 3 partisi untuk menginstall arch linux. 

penting: jika anda membutuhkan lebih dari 4 partisi pada satu hardisk, maka partisi yg ke 4 harus dibuat dengan tipe extended

|   Drive   |   Size  | Type: |
|   -----   |   ----  | ----- |
| /dev/sda1 | 500MB   | windows boot |
| /dev/sda2 | 100GB   | Drice :C |
| /dev/sda3 | 25Gb    | Drive :D  |
| /dev/sda4 |    100GB     | extended |
| /dev/sda5 | 4GB | Linux swap / solaris |
| /dev/sda6 | 30GB | Linux |
| /dev/sda7 | 60GB | Linux |

> penjelasan:\
sda1 sampai sda3 adalah partisi primary yg isinya partisi windows\
\
sda4 adalah partisi extended yg isinya adalah sda 5 sampai sda 7 \
/dev/sda5 adalah untuk partisi swap\
/dev/sda6 adalah untuk partisi /root\
/dev/sda7 adalah untuk partisi /home

untuk menerapkan perubahan pilih `[Write]` dan ketikan `yes` lalu tekan enter, untuk keluar pilih `[Quit]`

hasil pemartisian yg tadi kita buat dapat dilihat dengan perintah `lsblk`

Jika di tahap Partisi ini anda tidak mengerti, saya sarankan untuk menghentikan mengikuti panduan menginstall arch linux ini sebelum semua menjadi buruk. Bukan salah anda hanya saja saya tidak pandai menjelaskan. 

> **Formating**

`tips:`
`untuk menghindari salah format drive, gunakan perintah lsblk untuk melihat info drive`

memformat partisi swap
```bash
mkswap /dev/sda5
```
memformat partisi sda6 menjadi ext4 untuk mountpoint /root
```bash
mkfs.ext4 /dev/sda6
```
memformat partisi sda7 menjadi ext4 untuk mountpoin /home
```bash
mkfs.ext4 /dev/sda7
```

> **Mount**

mengaktifkan partisi swap yg sudah kita format tadi
```bash
swapon /dev/sda?
```
mount partisi /root untuk tempat kita install base sistem archlinux
```bash
mount /dev/sda? /mnt
```
membuat direktori home
```bash
mkdir /mnt/home
```
mount partisi /home 
```bash
mount /dev/sda? /mnt/home
```

Ganti tanda `?` dengan identitas drive. gunakan kembali perintah `lsblk` jika anda masih bingung.

## Install base
> **Pacstrap**

Jika proses pemartisian yakin telah diatur dengan benar, berikutnya lanjutkan dengan install base sistem

```bash
pacstrap /mnt base linux linux-firmware nano
```
> **Set fstab**

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```
> **Chroot**

```bash
arch-chroot /mnt
```
> **Hostname**

```bash
echo '$hostname' > /etc/hostname
```
Ganti `$hostname` dengan nama yg anda inginkan untuk komputer/host. misalnya kita ganti dengan `laptopku`

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
ganti semua `$hostname` dengan nama yg telah diberikan sebelumnya untuk komputer/host yaitu `laptopku` sehingga menjadi seperti berikut:
```bash
127.0.1.1   laptopku.localdomain    laptopku
```
> **Timezone**

```bash
ln -sf /usr/share/zoneinfo/Asia/Jakarta /etc/localtime
```

```bash
hwclock --systohc
```
> **Set locale**

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

Ganti `$username` dengan nama anda. Misalnya: `budi`
```bash
useradd -m -G wheel $username
```
membuat password untuk user. misalnya `123`
```bash
passwd $username
```

> **Sudoers**

```bash
EDITOR=nano visudo
```
Cari dan hapus tanda pager `#` pada baris `# %wheel ALL=(ALL) ALL`

> **Grub**

```bash
sudo pacman -S grub base-devel linux-headers networkmanager wireless_tools wpa_supplicant dialog mtools dosfstools os-prober ntfs-3g xdg-user-dirs
```
perlu di perhatikan, command di bawah hanya berlaku untuk menginstall grub jenis bios/mbr
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
Setelah reboot, login sebagai user. Dalam contoh ini nama usernya adalah `budi` dan dengan password `123`

> **Aktifkan internet**

- network manager

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

- usb tethering

`- Hubungkan android melalui kabel usb`\
`- Aktifkan usb tethering di pengaturan android`

Sebelum melanjutkan pastikan jaringan sudah terkoneksi dengan benar. untuk mengeceknya lakukan ping ke dns google:
```bash
ping 8.8.8.8
```
Untuk menghentikan proses ping tekan Ctrl+c

## Install kde (minimal)

Sampai disini bisa dikatakan kita telah selesai install arch linux. namun belum dapat digunakan semestinya distro pada umumnya, dan masih sulit untuk di operasikan karena masih berbasis CLI. 

Nah, Untuk membuatnya menjadi lebih mudah digunakan maka kita perlu memasang desktop environmen. dan kali ini desktop yg akan kita gunakan adalah kde. Karena selain tampilannya kece, kde juga mudah di kustomisasi. 

Dari banyaknya desktop, kde adalah desktop yg canggih menurut saya.

> **X server**

```bash
$ sudo pacman -S xorg xorg-server xorg-xinit
```

> **Display manager**

```bash
$ sudo pacman -S sddm sddm-kcm
```
```bash
$ sudo systemctl enable sddm
```

> **Paket kde minimal**

```bash
$ sudo pacman -S plasma-desktop plasma-nm plasma-pa kdeplasma-addons kde-system-meta breeze breeze-grub powerdevil
```
```bash
$ sudo pacman -S bluedevil gst-libav dolphin dolphin-plugins discover appstream appstream-qt packagekit packagekit-qt5 kdialog
```
```bash
$ sudo pacman -S konsole kfind kdeconnect ark gwenview okular spectacle vlc kinfocenter khelpcenter plasma-systemmonitor
```
reboot system

> **sddm theme**

masuk ke setting pilih startup & shutdown pilih sddm dan ganti theme ke breeze.

