# Fedora-setup
My step used in Fedora 32 setup

## Basic dnf
basic command to install, update
```
sudo dnf install <package_name>
sudo dnf update
sudo dnf list | grep <...>
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

## Wayland to Xorg
Checking what X server is used
```
loginctl
loginctl show-session <ID>
// ...
// Type=wayland
```
Go to `/etc/gdm/custom.conf` and uncomment `WaylandEnable=false`.
Add the following line to the `[daemon]` section:
```
DefaultSession=gnome-xorg.desktop
```
References:
https://docs.fedoraproject.org/en-US/quick-docs/configuring-xorg-as-default-gnome-session/

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


# X Server
### NVidia driver
It's hard to avoid dealing with NVidia driver. \
The opensource version is Nouveau. \
The official release from Nvidia is on their site \

Check it by:
```
lspci
// finding VGA
lspci -k
// see kernel module loaded
glxinfo -B
find /dev -group video
```
other directory of related file
```
// KERNEL
/etc/modprobe.d/
/etc/modprobe.d/*kms*
```
Main configuration
```
// CONFIG METHOD 1
/etc/xorg.conf
/etc/X11/xorg.conf

// CONFIG METHOD 2
/etc/X11/xorg.conf.d/
...
/etc/X11/xorg.conf.d/nn-xxxxxx.conf
// nn is a number the lower loaded first
/etc/X11/xorg.conf.d/50-monitors.conf
```
Log file
```
/var/log/Xorg.0.log
```
### General command
```
xinput
// show mouse, keyboard

Xorg :0 -configure
// X is stateless
// this command generate input
```

### RandR xrandr
Rotate and Resize
:0.0, the second is :0.1 
For me the screen is swap so I need to see the name of interface by
```
xrandr
```
and then set the DP-1 and HDMI-1 to be left and right screen
```
xrandr --output DP-1 --left-of HDMI-1
```

References: \
https://wiki.archlinux.org/index.php/Multihead  \
https://wiki.archlinux.org/index.php/Xorg  \
https://wiki.archlinux.org/index.php/NVIDIA


## Happy vimmer control and esc combination
Download the classic `xcape` solution make sure you have the header for compiling process.
```
$ sudo dnf install git gcc make pkgconfig libX11-devel libXtst-devel libXi-devel
$ git clone https://github.com/alols/xcape
```
Then make and compile it
```
$ make && sudo make install
```
Then remap the caplock to control \
See the full list of `keysym` and code by `xev` command \
or `xev -event keyboard`

This combination works
```
$ setxkbmap -option caps:ctrl_modifier
$ xcape -e 'Caps_Lock=Escape' -t 50
```
Xmodmap is an older alternative

**References:** \
http://nelsonware.com/blog/2019/04/30/how-to-map-caps-lock-to-escape-and-control-on-fedora-via-caps2esc.html
https://askubuntu.com/questions/445099/whats-the-opposite-of-setxkbmap-option-ctrlnocaps

