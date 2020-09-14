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

## Setup ZSH
```
sudo dnf install zsh
// go to https://ohmyz.sh/#install
// copy that curl install script and paste
chsh -s $(which zsh)
```
## Docker problem as always
One user fault is not starting docker deamon
```
sudo systemctl start docker
sudo systemctl status docker
sudo systemctl restart docker
```
More about permission
This solves the problem with `unix://` socket permission
```
sudo chmod 666 /var/run/docker.sock
```
Another basic is permission in the directory
```
usermod -aG docker ${USER}
sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
sudo chmod g+rwx "$HOME/.docker" -R
```
