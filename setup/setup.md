# Enable running 32-bit apps
```
sudo dpkg --add-architecture i386
sudo apt-get update
sudo apt-get install libc6:i386 libncurses5:i386 libstdc++6:i386 -y
```

# Apps
```
sudo apt-get install -y \
    filezilla \
    wine-stable \
    speedcrunch \
    htop \
    virtualbox \
    imagemagick \
    lcov \
    ccache \
    ninja-build \
    cmake \
    git-lfs \
    libx11-dev \
    libusb-1.0.0-dev \
    xorg-dev \
    libglu1-mesa-dev \
    freeglut3-dev \
    libudev-dev \
    awesome \
    xclip \
    numlockx \
    python3 \
    python3-pip \
    wireshark \
    docker.io \
    vlc \
    rclone

pip3 install crccheck pyusb tqdm pandas

cd ~; wget https://download.jetbrains.com/cpp/CLion-2021.2.3.tar.gz; tar -xzf CLion-*.tar.gz
```

# VS Code
```
sudo apt install software-properties-common apt-transport-https wget -y
wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"
sudo apt update
sudo apt install code -y
sudo update-alternatives --set editor /usr/bin/code
```

## Set VSCode as default for text files
* `sudo nano /usr/share/applications/defaults.list`
* Replace the `text/plain` line with `text/plain=code.desktop`

# Gitkraken
```
sudo apt-get install -y gconf-service gconf2
sudo apt --fix-broken install -y
wget https://release.gitkraken.com/linux/gitkraken-amd64.deb -O /tmp/gitkraken.deb
sudo dpkg -i /tmp/gitkraken.deb
sudo apt-get install -f -y
echo fs.inotify.max_user_watches=9999999 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```

# Allow docker without sudo
```
sudo systemctl enable docker
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```

# Allow wireshark to capture without sudo
```
sudo usermod -a -G wireshark $USER
newgrp wireshark
sudo dpkg-reconfigure wireshark-common
```

# Stop Linux from breaking Windows Clock
```
timedatectl set-local-rtc 1
```

# Allow access to USB devices
```
echo 'SUBSYSTEM=="usb", MODE="0660", GROUP="plugdev"' | sudo tee /etc/udev/rules.d/00-usb-permissions.rules
sudo udevadm control --reload-rules
sudo usermod -a -G plugdev $USER
```

# Eenable sudo without password
```
sudo visudo
```

Add this to the bottom, replace $USER with your username
```
$USER ALL=NOPASSWD: ALL
```

# Enable ctrl-backspace in console
Add this in ~/.inputrc
```
"\C-H": backward-kill-word
"\e[1;5C": forward-word
"\e[1;5D": backward-word
"\e[5C": forward-word
"\e[5D": backward-word
"\e\e[C": forward-word
"\e\e[D": backward-word
"\e[3;5~": kill-word
```

# Disable DPI scaling
```
echo Xft.dpi:96 > ~/.Xresources
```

# Fix tearing
* `sudo nvidia-settings`
* in X Server Display Configuration
* Select primary screen
* Click Advanced...
* Check Force Composition Pipeline
* Click Save to X Configuration File
* Copy content and save to /etc/X11/xorg.conf

# Set ccache size
`ccache -M 50G`

# Mount Rose's Jester
## NFS
Add the following in /etc/fstab: `192.168.0.10:/mnt/pool1 /mnt/share nfs defaults 0 0`

## Samba
Install cfs utils with `sudo apt install cifs-utils -y`
Create creds file with `nano ~/.smb`
```
user=[redacted]    
password=[redacted]
domain=WORKGROUP
```

Mount in fstab with `sudo nano /etc/fstab`, add 
```
//192.168.0.10/mntsmb /mnt/share cifs uid=0,credentials=/home/rose/.smb,iocharset=utf8,noperm 0 0
```

Apply without reboot via `sudo mount -a`

# Spek
```
git clone git://github.com/alexkay/spek.git
cd spek
./autogen.sh
make
sudo make install
```

# RClone 
* Create the `b2` remote via `rclone config create backblaze-encrypted b2 account "" key ""`
* Create the `crypt` remote via `rclone config create backblaze-decrypted crypt remote backblaze-encrypted:remi-backup-encrypted filename_encryption standard directory_name_encryption true password "" password2 ""`
* List directories via `rclone lsd backblaze-decrypted:/`
* List files via `rclone ls --max-depth=1 backblaze-decrypted:/Images/Ponies` 
* Copy files via `rclone copy backblaze-decrypted:/Images/Ponies/1VZ0u.png ./`
* Delete the remotes via `rclone config delete backblaze-encrypted` and `rclone config delete backblaze-decrypted`

# Maintenance
```
sudo apt update
sudo apt upgrade -y
sudo apt autoremove --purge -y
```

# Tasmota
In Tasmota Console:
```
MqttHost 192.168.0.199
MqttUser 0
MqttPassword 0
EnergyToday 0
EnergyTotal 0
PowerOnState 1
TelePeriod 30
```

# Static IP
`sudo nano /etc/netplan/01-netcfg.yaml`:

```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp5s0f0:
      dhcp4: no 
      addresses:
        - 10.1.1.11/24
    enp6s0:
      dhcp4: yes
```

then `sudo netplan apply`
