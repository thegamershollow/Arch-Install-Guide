# Arch Linux Install Guide
A usefull guide for installing arch linux using the command-line.

## Overview
Arch Linux is a linux distro that a lot of people say is too hard to install because you have to do everything yourself, but as arch linux has grown the user base has as well and with that, it has become more simple to install.

This guide is aimed at helping you install arch linux on your device. Let's get to the installation.

# Installing Arch Linux

## Reqirements

1. Internet (It is recommended to use ethernet, but I will go over using wifi as well)
2. USB Drive for installer.
3. Arch Linux Installer ISO.
4. Another computer for flashing the USB and reading the guide.

## Getting Started

Download Balena Etcher to your computer from [HERE](https://www.balena.io/etcher).

After downloading Balena Etcher install it and download the Arch Linux ISO from [HERE](https://archlinux.org/download/).

Open Balena etcher and select the Arch Linux ISO to flash. Select the drive you want to flash the installer to. Then click flash. (**Note if you are on MacOS or Linux, it might ask for a password**)

After flashing the ISO to you USB drive, plug the drive into your computer and select the drive from your bios menu and boot into the installer.

## Installing Arch Linux

after booting into the installer you will be greeted with the command line. 

check if you system supports UEFI
```# cat /sys/firmware/efi/fw_platform_size```

sync the clock with UTC

```# timedatectl```

list drives

```lsblk```

start fdisk

```# fdisk /dev/the_disk_to_be_partitioned```

####Example disk layout

| Mount point on the Installed System | Partition | Partition Type |Suggested Size|
| :---------------- | :--- | :--- | :--- |
| ```/efi```        |   ```/dev/efi_system_partition```   | ```Efi``` |```512 MiB``` |
| ```[SWAP]```           |   ```/dev/swap_partition```  | ```Linux Swap``` |```Use this: ```[Swap Calc](https://pickwicksoft.github.io/swapcalc/) |
| ```/``` |  ```/dev/root_partition```   | ```Linux x86_64 root (/)``` |```Remainder of the device. ```<br>```At least 23–32 GiB. ```|

###Creating and Formating the partitions

```
When I type enter hit the return key
n
ENTER
ENTER
ENTER
+512M
ENTER
n
ENTER
ENTER
ENTER
+2G
ENTER
n
ENTER
ENTER
ENTER
p
ENTER
W
ENTER
```

I will be using EXT4 for this example but you can use any format that is supported though there might be extra setup for BTRFS.

create a root partition
```# mkfs.ext4 /dev/root_partition/```

create a swap partition
```# mkswap /dev/swap_partition```

create an efi partition
```# mkfs.fat -F 32 /dev/efi_system_partition```

### Mount the file systems

mount the root partition
```# mount /dev/root_partition /mnt```

mount the efi partition
```# mount --mkdir /dev/efi_system_partition /mnt/efi```

turn on swap
```# swapon /dev/swap_partition```

### Installation

####Install essential packages

packages that you shoul install

 - base
 - base-devel
 - linux
 - linux-firmware
 - cpu microcode (amd-ucode for amd chips or intel-ucode for intel chips)
 - git
 - zsh
 - zsh-autosuggestions
 - zsh-completions
 - openssh
 - python3
 - sudo
 - man
 - reflector
 - pipewire pipewire-alsa pipewire-pulse pipewire-jack
 - wireplumber
 - nano or vim

 install packages
```# pacstrap -K /mnt base base-devel linux linux-firmware amd-ucode git zsh zsh-autosuggestions zsh-completions openssh python3 hyfetch sudo man reflector pipewire pipewire-alsa pipewire-pulse pipewire-jack wireplumber nano```

####Fstab

```# genfstab -U /mnt >> /mnt/etc/fstab```
```# cat /mnt/etc/fstab```

#### arch chroot

```# arch-chroot /mnt```

#### Localization

Edit /etc/locale.gen and uncomment en_US.UTF-8 UTF-8 and other needed UTF-8 locales. Generate the locales by running:

```# locale-gen```

Create the locale.conf file, and set the LANG variable accordingly:

```
/etc/locale.conf
----------------
LANG=en_US.UTF-8
```

#### network config

Create the hostname file:

```
/etc/hostname
----------------
*yourhostname*
```

#### Root Password

set the root password:

```
# passwd
```

#### Create a User

add your user:

```
#useradd -mG wheel yourname
```


Uncomment the wheel group to allow execution of any command( ie: remove the # from the wheel line below where it says something like: "Uncomment to let members of group wheel execute any action" ). if you want to use nano then write EDITOR=nano instead.

```
EDITOR=vim visudo
```

#### Grub Config

Install the grub bootloader:

```
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=Arch
```

Generate the grub config file:

```
grub-mkconfig -o /boot/grub/grub.cfg
```

#### Reboot

```
# reboot
```








