# ArchLinux-Installation-Guide

Guida Installazione ArchLinux

Release date: 2019.01.26

Rel. 2.2

# Youtube Video Tutorial:

[![IMAGE ALT TEXT](http://img.youtube.com/vi/d6xPdCzxxcg/0.jpg)](http://www.youtube.com/watch?v=d6xPdCzxxcg "Arch Linux Installation Guide")



#

### Appunti per installare il sistema base: ##





```
loadkeys it
cfdisk
```

# Struttura delle partizioni:

```
/dev/sda1         # boot (flag bootable)
/dev/sda2         # root (/)
/dev/sda3         # Home
/dev/sda4         # Swap
```

# Creazione FileSystem:
```
mkfs.ext4 /dev/sda1
mkfs.ext4 /dev/sda2
mkfs.ext4 /dev/sda3
mkswap /dev/sda4
swapon /dev/sda4
```
# Montaggio partizioni e creazione cartelle:
```
mount /dev/sda2 /mnt
mkdir /mnt/boot /mnt/home
mount /dev/sda1 /mnt/boot
mount /dev/sda3 /mnt/home


# Monto la root (/dev/sda2 sotto /mnt) 
# Creo le cartelle /boot e /home nella partizione /dev/sda2 appena montata in /mnt
# Monto /dev/sda1 (partizione di boot) in /mnt/boot
# Monto /dev/sda3 (partizione della home) in /mnt/home
```
# Installazione sistema base:
```
pacstrap /mnt base base-devel
```
# Generazione del file fstab e login chroot nel sistema appena installato:
```
genfstab -U /mnt >> /mnt/etc/fstab          # Genera il file in /etc/fstab (-U per scrivere gli UUID)
arch-chroot /mnt                            # Entro in chroot per modificare il sistema appena installato
```
# Lingua, Data/ora, hostname
```
vi /etc/locale.gen  # Decommenta sia it_IT.UTF-8 UTF-8 che en_US.UTF-8
locale-gen          # Genera il locale
echo "LANG=it_IT.UTF-8" > /etc/locale.conf
echo "LANG=it_IT.UTF-8\nKEYMAP=it" > /etc/vconsole.conf
export LANG=it_IT.UTF-8

rm /etc/localtime
ln -s /usr/share/zoneinfo/Europe/Rome /etc/localtime
hwclock --systohc --utc

echo "nome_pc" > /etc/hostname
vi /etc/hosts
                    # Inserire:
                                127.0.0.1   localhost.localdomain   localhost
                                ::1         localhost.localdomain   localhost
                                127.0.1.1   nome_pc.localdomain     nome_pc
```
Nel file `locale.conf` conviene inserire quanto segue per evitare errori di localizzazione:
```
LANG="it_IT.UTF-8"
LC_CTYPE="it_IT.UTF-8"
LC_NUMERIC=it_IT.UTF-8
LC_TIME=it_IT.UTF-8
LC_COLLATE=it_IT.UTF-8
LC_MONETARY=it_IT.UTF-8
LC_MESSAGES="it_IT.UTF-8"
LC_PAPER="it_IT.UTF-8"
LC_NAME="it_IT.UTF-8"
LC_ADDRESS="it_IT.UTF-8"
LC_TELEPHONE="it_IT.UTF-8"
LC_MEASUREMENT=it_IT.UTF-8
LC_IDENTIFICATION="it_IT.UTF-8"
LC_ALL="it_IT.UTF-8"
```

# Creazione utente e reset password:
```
passwd                                             # Imposta password utente root
useradd -m -G wheel -s /bin/bash nomeutente
passwd nomeutente                                  # Imposta password utente appena creato
pacman -Syyu sudo vim                              # Installo sudo e vim (Sync db arch+full upgrade -yyu)
visudo                                             # Togliere commento alla riga:  %wheel ALL=(ALL) ALL
```
# Installare Bootloader (grub2):
```
pacman -S grub linux-headers
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
mkinitcpio -p linux
```
# Abilitare Dhcp all'avvio, uscita Chroot e riavvio:
```
systemctl enable dhcpcd.service
exit
umount -R /mnt
reboot
```
