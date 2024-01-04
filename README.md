# Arch-Install-Notes

# [TASTIERA]
Per impostare la tastiera in italiano...
```loadkeys it```
Per cambiare il font digitare il seguente comando sostituendo a XX i dpi [24] è una buona scelta di solito ma fare un po di fine-tuning
```setfont ter-iXXb```

# [RETE]

```iwctl```

```station wlan0 connect [nome rete]```

```exit```

```ping 8.8.8.8``` 

controlla che ti risponda correttamente

# [TIMEZONE]

timedatectl set-timezone CET

timedatectl set-tnp true

date -> controlla che la data sia corretta

# [DISK PARTITION]

fdisk -l -> controlla il disco su cui vuoi installare arch

cfdisk /dev/<nome disco]

se non è presente creare partition GPT

creare tre partizioni

512M EFI -> selezionare type EFI

doppio della ram -> [partizione swap]

root lo spazio restante

# FORMATTAZIONE
mkfs.vfat -F32 /dev/[partizione efi]

mkswap /dev/[partizione swap]

mkfs.btrfs /dev/[partizione boot]

mount /dev/[partizione boot] /mnt

# creazione sottovolume @ di root

btrfs subvolume create /mnt/@

btrfs subvolume create /mnt/@home

umount -R /mnt

mount -o subvol=@ /dev/[partizione root] /mnt

mkdir -p /mnt/home

mount -o subvol=@home /dev/[partizione root] /mnt/home

mount -> osservare se i volumi sono montati sotto mnt e mnt home

mkdir -p /mnt/boot/efi

mount /dev/[partizione efi] /mnt/boot/efi

swapon /dev/[partizione swap]

# [BASE INSTALLATION]

pacstrap -K /mnt base base-devel linux linux-firmware nano btrfs-progs grub-btrfs net-tools

WARNING!! se da problemi pacstrap con la chiave provare il comando ```# pacman -Sy archlinux-keyring```

genfstab -U /mnt >> /mnt/etc/fstab

arch-chroot /mnt

# [CHROOT]
ln -sf /usr/share/zoneinfo/Europe/Rome /etc/localtime

hwclock --systohc

date -> controllare che la data sia esatta

nano /etc/locale.gen

ctrl + W -> cercare it_IT e decommentare la riga -> ctrl + x -> y -> invio

locale-gen

nano /etc/vconsole.conf

scrivere LANG=it_IT.UTF-8 -> ctrl + x -> y -> invio

nano /etc/hostname -> scrivere il nome di rete che si vuole per il dispositivo

passwd -> inserire la password che si uole per il root


## [BOOTLOADER (GRUB)]

pacman -S intel-ucode [se si ha una cpu intel altrimenti vedere guida]

pacman -S grub efibootmgr

grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB

grub-mkconfig -o /boot/grub/grub.cfg


# [POST INSTALLATION]

useradd -m enrico

passwd enrico

nano /etc/sudoers -> cerca wheel e cancella la riga per dare i privilegi al gruppo wheel

usermod -aG wheel enrico

# [KDE]

pacman -S plasma-meta plasma-wayland-session kde-system-meta kde-utilities-meta kde-network-meta firefox

systemctl enable sddm

systemctl enable NetworkManager

# [DRIVER VIDEO]

pacman -S mesa vulkan-intel (solo per grafica intel altrimenti installare nvidia, vedere doc)
