## Create default applications

* Set default browser

Create a desktop entry for your desktop and add the entries

```sh
sudo vim /usr/share/applications/qutebrowser.desktop
```

```
[Desktop Entry]
Encoding=UTF-8
Version=1.0
Type=Application
Terminal=false
Exec=/usr/bin/qutebrowser
Name=qutebrowser
#Icon=/path/to/icon
```

Change the defaults using xdg-mime

```sh
xdg-mime default qutebrowser.desktop x-scheme-handler/http
xdg-mime default qutebrowser.desktop x-scheme-handler/https
```
