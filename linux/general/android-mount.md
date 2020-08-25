## Mount Android Device

Devices use the Media Transfer Protocol

```
# install mtp
sudo pacman -S libmtp

# detect devices
mtp-detect

# connect to device
# make sure your device isnt opened on a file manager as it
# may make the device busy
mtp-connect

# view folders
mtp-folders

# download mtp filesystem
sudo pacman -S mtpfs

# mount the filesystem
sudo mtpfs -o allow_other ~/mnt
```
