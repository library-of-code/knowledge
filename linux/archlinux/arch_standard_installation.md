# Installing Arch the Standard Way

---

## Create a bootable pendrive

Run `lsblk` to see what is your pendrive

```sh
dd if=<path to arch iso> of=/dev/sd<letter> status="progress"
```

---

## Boot from USB

Press F12 (or F8 on some machines) and choose USB hardrive. You may or may not have to disable secure boot. Choose Arch live install.

---

## Check internet connection

Check if `ping 8.8.8.8` pings the server. You can also check `ip addr` to see if an IP address shows up. 

```sh
# to get wifi
wifi-menu
```

You may need to `systemctl restart dhcpd` the dhcp daemon for the internet to work (may not also)

---

## Choose a Mirror nearest to you

Go to `/etc/pacman.d/mirrorlist` and choose the mirror closest to you and move that to the beggining of the file. Note that vim editor works.

---

## Test the Mirrors

Run:

```sh
pacman -Syyy
```

---


## Take a look at the disk structure

You can run any of the following commands to list the volumes:

```sh
fdisk -l | more

lsblk
```

The aim is to get an idea of which disk you want to partition

---

## Check if your system supports efi

If the following command gives viable output then it does:

```sh
efivar -l
```

If this is the case then your grub_install step will be different and you ideally should create an additional 2M partition of BIOS type.

---

## Create Disk Partitions

For the purpose of this tutorial let us assume that `/dev/sda` is the drive you want to install Arch on:

Note that unless you press `w` in the PS2 given by fdisk, nothing is really written on disk. You can always press `p` to see the current status. If you did something wrong then you can always press `g` again to define a GPT partition (the older data will be overwritten).

Note that we need to create 3 partitions (one 2M extra if efivar not there):

* First for EFI: 500M (can be less)
* Second for the root filesystem: 30G
* Third for the user filesystem: Remainder of the disk

```sh
fdisk /dev/sda

# Print current partition layout
> p

# Create a new gpt partition
> g

# New partition #1 (for its last sector use `+500M`, enter through all else. Dont forget to use a + sign)
> n 

# Choose new type. Press L to see the types. Press 1 to choose EFI type
> t 

# For root filesystem (enter through all, but the last sector add `+30G`)
> n 

# For the home filesystem. Enter through all and it will take the remainder of the space on disk
> n

# See your current settings
> p

# Finalize everything
> w
```

And done

---

## Format Partitions

The EFI parition should be FAT32 and the root and user should be ext4. If no efivar then the BIOS one should be FAT32 as well

```sh
# EFI as FAT32
mkfs.fat -F32 /dev/sda1

# ext4 for second and third partitions
mkfs.ext4 /dev/sda2
mkfs.ext4 /dev/sda3
```

---

## Mount the partitions

Mount the root filesystem at the mount point and the home partition inside the home folder

```sh
mount /dev/sda2 /mnt

mkdir /mnt/home

mount /dev/sda3 /mnt/home

# These command show the details
mount
df -h
```

---

## Export filesystem mount points into fstab

```sh
mkdir /mnt/etc

genfstab -U -p /mnt >> /mnt/etc/fstab

# Create a backup of the fstab file
cp /mnt/etc/fstab /mnt/etc/fstab.bak
```

---

## Pacstrap

Bootstrap the needed binaries on the mounted filesystem using pacstrap. Base is necessary. Base-devel is for the additional binaries (needed if we are building AURs locally).

```sh
pactrap -i /mnt base base-devel
```

---

## Connect to the mounted FS

Whatever we do in the chroot environment will take effect in the new Arch installation.

```sh
arch-chroot /mnt
```

---

## Choose a Kernel

For rolling kernel:

```sh
pacman -S linux linux-headers
```

For LTS Kernel

```sh
pacman -S linux-lts linux-lts-headers
```

Note that you can add both. If you do that then you will be prompted which kernel to use during startup.

---

## Install an editor

```sh
pacman -S vim
```

---

## Install SSH

This step is optional

```sh
pacman -S openssh

systemctl enable sshd
```

Systemctl basically creates symlinks on a .service file on enabling. All of the symlinked services will run as systemd services on boot.

---

## Installing Network Packages

Only the first one is required, the rest are optional

```sh
pacman -S networkmanager wpa_supplicant wireless_tools netctl

systemctl enable NetworkManager
```

---

## Install the dialog package

```sh
pacman -S dialog
```

---

## Generate initial ramdisk

```sh
# For Rolling
mkinitcpio -p linux

# For LTS
mkinitcpio -p linux-lts
```

---

## Configure Locale

```sh
# find your locale in the file. In my case -> (en_US.UTF-8 UTF-8) 
# Uncomment your locale
vim /etc/locale.gen

# Generate locale
locale-gen
```

---

## Setting a Password

```sh
# Setting root password
passwd

# Creating a user and giving sudo access
useradd -m -g users -G wheel angad

# Creating password for the user
passwd angad

# Go to this file and uncomment the wheels line
visudo
```

---

## Configure GRUB Bootloader

Install the required packages

```sh
pacman -S grub efibootmgr dosfstools os-prober mtools
```

Create a dir for EFI and mount the EFI filesystem here in the chroot environment

```sh
mkdir /boot/EFI
mount /dev/sda1 /boot/EFI
```

---

## Install GRUB

```sh
# for system which supports efi (efivar -l)
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck

# for system which does not support efivar -l
# /dev/sda4 is the 2M partition of BIOS type
grub-install --target=i386-pc /dev/sda4

```

## Copy locale to GRUB

```sh
mkdir /boot/grub/locale

# Depends on your locale
cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo
```

---

## Generate GRUB config

```sh
grub-mkconfig -o /boot/grub/grub.cfg
```

---

## Create a swapfile

When a program consumes more RAM than there is, some programs get pre-empted to the swap space. It is ideal to have it on the filesystem rather than as another partition so that the size can be flexible.

```sh
# 2 GB swapfile
fallocalte -l 2G /swapfile

# Change permisisons of the swapfile
chmod 600 /swapfile

# Make the swapfile
mkswap /swapfile

# Make it to activate at boot by adding it to the fstab
echo '/swapfile none swap sw 0 0' >> /etc/fstab
```

---

## Install additional packages

```sh
# for amd CPU microcode
pacman -S amd-ucode

# for intel CPU microcode
pacman -S intel-ucode

# for amd/intel graphics
# mesa is pre-requisite for xorg-server too
pacman -S mesa

# for nvidia graphics
pacman -S nvidia nvidia-utils

# for a desktop environment
pacman -S xorg-server xorg-xinit

# for linux firmware
pacman -S linux-firmware

# for sound
pacman -S alsa-utils

# for ifconfig
pacman -S net-tools
```

---

## If installing Arch on virtualbox

```sh
pacman -S virtualbox-guest-utils xf86-video-vmware
```

---

## Synchronise clock

We will activate the service, to activate synchronization between the computer and the servers on the internet:

```sh
systemctl enable systemd-timesyncd.service

timedatectl set-ntp true
```

---

## Fix Touch Pad

https://wiki.archlinux.org/index.php/Touchpad_Synaptics

---

## Brightness and audio keybindings

Create a script called `bright` and move it to `/usr/local/bin`:

```sh
#!/bin/bash
current=`cat /sys/class/backlight/intel_backlight/brightness`
if [[ $1 == "-d" ]]; then
 let 'current-=10000'
 if [[ $current -le 5000 ]]; then
 current=5000
 fi
else 
 let 'current+=10000'
fi
echo $current > /sys/class/backlight/intel_backlight/brightness
```

Now `chmod 777 /sys/class/backlight/intel_backlight/brightness` so that users can change brightness. You can now bind the keys:

| Key | Command |
|:---:|:-------:|
| XF86MonBrightnessUp | bright |
| XF86MonBrightnessDown | bright -d |
| XF86AudioLowerVolume | amixer set Master 5%- unmute |
| XF86AudioRaiseVolume | amixer set Master 5%+ unmute |

---

## Enable exfat and NTFS

```sh
sudo pacman -S extfat-utils

sudo pacman -S ntfs-3g
```

## Enable bluetooth

```sh
# gives you access to bluetoothctl and bluetooth services
sudo pacman -S bluez bluez-utils 

# Without these, device will pair but not connect
sudo pacman -S bluez-hid2hci pulseaudio-bluetooth

# Enable and start bluetooth.service
sudo systemctl enable --now bluetooth

# Load pulseaudio kernel module
pactl load-module module-bluetooth-discover
```

---

## Enable USB and USB input

Edit the `/etc/mkinitcpio.conf` file and add `usb` and `usbinput` in the `HOOKS` array.

---

## If locale isn't set properly

```sh
sudo localectl set-locale LANG=en_US.UTF-8
```

---

## Poweroff or reboot

```sh
# exit from chroot
exit

# check mounts
umount -a

# poweroff
poweroff
```

You are now good to go! The next steps are only necessary if you want a desktop environment.

## Setting up a display manager

You can download any display manager. This is basically your login screen to your desktop environment. You will also want a greeter to go along with it.

```sh
pacman -S lightdm lightdm-gtk-greeter

systemctl enable lightdm
```

---

## Setting up a window manager

You can download a WM for a desktop environment (DE). Let us see how we can get the awesome window manager up and running:

```sh
pacman -S awesome

echo 'exec awesome' > ~/.xinitrc

startx
```

---

## Setting up a desktop environment

Optionally, you can set up a desktop environment:

```sh
pacman -S cinnamon

echo 'exec cinnamon-session' > ~/.xinitrc

startx
```
