# Fedora-setup
My step used in Fedora 32 setup

## Alt Shift for input change without gnome-tweak
```
gsettings set org.gnome.desktop.wm.keybindings switch-input-source "['<Shift>Alt_L']"
gsettings set org.gnome.desktop.wm.keybindings switch-input-source-backward "['<Alt>Shift_L']"
```
Checking the result
```
gsettings get org.gnome.desktop.wm.keybindings switch-input-source
gsettings get org.gnome.desktop.wm.keybindings switch-input-source-backward
```
Well, how do I know this apart from Stackoverflow
```
gsettings
gsettings list-schemas
gsettings list-keys org.gnome.desktop.wm.keybindings
```
This tell you the structure is schema --> keys --> value
