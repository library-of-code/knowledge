## Installing and configuring virt-manager on KVM/QEMU


* Download packages needed for KVM/QEMU
```sh
sudo pacman -S qemu virt-manager virt-viewer dnsmasq vde2 bridge-utils openbsd-netcat libvirt

sudo pacman -S ebtables iptables

yay -S libguestfs
```


* Enable libvirtd

```
sudo systemctl enable --now libvirtd.service
sudo systemctl enable --now libvirtd
```

* Add policy kit

```
sudo pacman -S polkit
```

* Add user to kvm group (Make sure VT-x is enabled in BIOS)

```
sudo usermod -aG kvm angad
```

* Allow users of kvm group to manager libvirtd. Add the following lines in `/etc/polkit-1/rules.d/50-libvirt.rules`.

```
/* Allow users in kvm group to manage the libvirt
daemon without authentication */
polkit.addRule(function(action, subject) {
    if (action.id == "org.libvirt.unix.manage" &&
        subject.isInGroup("kvm")) {
            return polkit.Result.YES;
    }
});
```
