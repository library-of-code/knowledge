## Download and setup fonts


```sh
# download the font (let us say folder name is monokai
# and unzip it
unzip monokai.zip

# make the fonts dir
mkdir -p ~/.local/share/fonts

# copy fonts to dir
cp monokai/*.ttf ~/.local/share/fonts/

# refresh fonts cache
fc-cache -f -v

# list fonts
fc-list
```

## List of fonts used

* Mononoki Nerd Font
* SauceCodePro Nerd Font
* monospace
* JetBrainsMono Nerd Font
