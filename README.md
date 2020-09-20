# Fedora-setup
My step used in Fedora 32 setup

## Basic dnf
basic command to install, update
```
sudo dnf install <package_name>
sudo dnf update
sudo dnf remove docker-*
sudo dnf config-manager --disable docker-*
sudo dnf search nodejs
```


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

### Node.js and yarn
I'm impressed that node and yarn is up-to-date than apt
```
sudo dnf install nodejs
sudo dnf install yarnpkg
```

## Setup ZSH
```
sudo dnf install zsh
// go to https://ohmyz.sh/#install
// copy that curl install script and paste
chsh -s $(which zsh)
```
## Docker problem as always
This is from Fedora Magazine \
credit: https://fedoramagazine.org/docker-and-fedora-32/ \

This root problem is that Fedora move to cgroup v2 but most of the container stick with cgroup v1.
```
sudo grubby --update-kernel=ALL --args="systemd.unified_cgroup_hierarchy=0"
```
Enable firewall
```
sudo firewall-cmd --permanent --zone=trusted --add-interface=docker0
sudo firewall-cmd --permanent --zone=FedoraWorkstation --add-masquerade
```
Install Mobi engine \
Don't worry Mobi is written by Docker
```
sudo dnf install moby-engine docker-compose
sudo systemctl enable docker
sudo reboot
```
Volume problem \
adding `:z` or `:Z` solve the problem \
Also don't know why
```
volumes:
- ./home/app:./postgres-data:z
```

basic mistake
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

Checking what X server is used
```
loginctl
loginctl show-session <ID>
// ...
// Type=wayland
```

## Restarting X Server
When the screen freeze switch to terminal mode by \
`Ctrl+Alt+F2` or `F3`,`F4` for `tty3` and `tty4`

The default Fedora 32 use `gdm` \
you can change to some alternative like SDDM/LXDM/LightDM/KDM/XDM \
For `gdm` restart it by
```
sudo systemctl restart gdm
```

## Change watch limit
For some poor repos with limited tools config, you need to relax the watch rule to watch a giant amount of files.
```
sudo sysctl -a // seeing the name
sudo sysctl -w fs.inotify.max_user_watches=32768
```

