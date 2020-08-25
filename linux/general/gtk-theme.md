## Setting the gtk theme

```sh
sudo pacman -S arc-gtk theme
gsettings set org.gnome.desktop.interface gtk-theme "arc-gtk-theme"
```

## Preferring dark theme

```sh
# Add the following line in this file:
# gtk-application-prefer-dark-theme = true
# FOR gtk 2
sudo vim /usr/share/gtk-2.0/settings.ini

# FOR gtk 3
vim ~/.config/gtk-3.0/settings.ini

# Changing font:
gtk-font-name = JetBrainsMono Nerd Font 11

# Adding icons
gtk-icon-theme-name = Adwaita
gtk-theme-name = Adwaita
```
