## Installing kernels

TODO: In progress

* Rolling kernel

```sh
sudo pacman -S linux linux-headers linux-firmware
```

* LTS kernel

```sh
sudo pacman -S linux-lts linux-lts-headers linux-lts-firmware
```

* Xanmod kernel

```sh
yay -S linux-xanmod linux-xanmod-headers
```

Now re-initialize ramdisk and make grub config again

Edit the /etc/mkinitcpio.conf and add this before running mkinitcpio

```sh
MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)
```

```sh
# re-initialize ramdisk
sudo mkinitcpio -p <kernel name>

# update grub
sudo grub-mkconfig -o /boot/grub/grub.cfg

# OR
sudo pacman -S update-grub
sudo update-grub

# Now reboot
reboot
```

Now we need nvidia drivers for the custom kernel. Luckily we can do this: which will attach itself to a pacman hook to build the driver for the custom kernel

```sh
sudo pacman -S nvidia-dkms
```
