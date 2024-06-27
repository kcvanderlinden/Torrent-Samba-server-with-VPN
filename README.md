# Torrent-Samba-server-with-VPN
A guide for setting up a Torrent Samba server with VPN on a Ubuntu server image. This is meant as a guide for my self for future use.

## Install Ubuntu
with username torrent

## Install Samba
```
sudo apt update \
&& sudo apt install -y samba
```
edit the config file
```
sudo nano /etc/samba/smb.conf
```
and add these lines to the bottom of the file
```
[sambashare]
    comment = Samba on Ubuntu
    path = /home/torrent
    read only = no
    browsable = yes
```
restart samba and add to firewall
```
sudo service smbd restart \
&& sudo ufw allow samba
```
setup password for username on samba
```
sudo smbpasswd -a torrent
```

## Install Docker
from the Docker [documentation](https://docs.docker.com/engine/install/ubuntu/).
Add Docker's official GPG key:
```
sudo apt-get update \
&& sudo apt-get install -y ca-certificates curl \
&& sudo install -m 0755 -d /etc/apt/keyrings \
&& sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc \
&& sudo chmod a+r /etc/apt/keyrings/docker.asc
```
Add the repository to Apt sources:
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null \
&& sudo apt-get update
```
Install Docker and required extensions
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## Install the Transmission container with VPN
from the Github repo [docker-transmission-openvpn](https://github.com/haugene/docker-transmission-openvpn)
