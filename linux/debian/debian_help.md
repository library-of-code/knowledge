# OS Bootstrap Help

## Making a bootable USB drive
* Figuring out which is the USB (will be in the /dev or /media)

```bash
lsblk | grep <usb name>
```
For this example lets assume /dev/sdc1

* dd command for conversion

```bash
# sudo dd bs=4M if=<path-to-iso> of=/dev/sd<letter> conv=fdatasync
sudo dd if=<path-to-iso> of=<path-to-dev, eg: /dev/sdc> status="progress"
```

* Voila

3rd December 2018
after apocalypse

1. Nvidia And Power
   * Settings and drivers
     * cuda and cudnn
   * Power - tlp and fast get

2. vim
   * vimrc
   * vim plug
   * Building vim from source (for ycm)
```bash
https://github.com/ycm-core/YouCompleteMe/wiki/Building-Vim-from-source
```

3. audio
   * pulseeffects

4. Misc
   * system
     * chrome
     * unity tweak tool


### Touchpad

<br />

```bash
DO NOT RUN THIS  sudo apt-get install xserver-xorg-input-synaptics sudo reboot
```

<br />

### SSH keygen

``` 
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub
```
