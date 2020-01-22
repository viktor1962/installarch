# installarch
crib
подсказки для установки arch linux
loadkeys ru
setfont cyr-sun16
timedatectl set-ntp true
fdisk -l
cfdisk /dev/sda

#boot 1G
mkfs.fat -F32 /dev/sda1
mkdir -p /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi

#root 32G
mkfs.ext4  /dev/sda2 -L "Arch"
mount /dev/sda2 /mnt

#home
mkdir /mnt/home
mkfs.ext4  /dev/sda3 -L "Home"
mount /dev/sda3 /mnt/home

#swap 16G
mkswap /dev/sda4 -L "swap"
swapon /dev/sda4

..#Просматриваем и проверяем разделы и подключение: cfdisk

..nano /etc/pacman.d/mirrorlist
.............
pacstrap /mnt base base-devel linux linux-firmware nano dhcpcd netctl
.............
..genfstab -pU /mnt >> /mnt/etc/fstab
..#Просмотрим созданный fstab: nano /mnt/etc/fstab
.............
..arch-chroot /mnt
..pacman -S nano
..nano /etc/hostname
.............
..ln -svf /usr/share/zoneinfo/Asia/Yekaterinburg /etc/localtime
..hwclock --systohc
.............
..echo "en_US.UTF-8 UTF-8" > /etc/locale.gen
..echo "ru_RU.UTF-8 UTF-8" >> /etc/locale.gen 
..locale-gen
.............
echo 'LANG="ru_RU.UTF-8"' > /etc/locale.conf
....
echo 'KEYMAP=ru' >> /etc/vconsole.conf
...
echo 'FONT=cyr-sun16' >> /etc/vconsole.conf
...........
useradd -G wheel -s /bin/bash -m vitaly
...........
nano /etc/sudoers #Раскомментируем строку %wheel ALL=(ALL) ALL
....
mkinitcpio -p linux
..pacman -Syy
..pacman -S grub efibootmgr --noconfirm 
...grub-install /dev/sda
...grub-mkconfig -o /boot/grub/grub.cfg
.......
#Создаем root пароль
passwd
........
#Устанавливаем пароль пользователя'
passwd vitaly
....
echo '[multilib]' >> /etc/pacman.conf
echo 'Include = /etc/pacman.d/mirrorlist' >> /etc/pacman.conf
....
..pacman -Syy
..pacman -S xorg-server xorg-drivers xorg-xinit 
..pacman -S xfce4 xfce4-goodies --noconfirm
..pacman -S lxdm --noconfirm
..systemctl enable lxdm
..pacman -S ttf-liberation ttf-dejavu --noconfirm 
..pacman -S networkmanager network-manager-applet ppp --noconfirm
..systemctl enable NetworkManager
...........reboot
