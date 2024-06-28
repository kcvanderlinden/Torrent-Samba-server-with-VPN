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

in the home/torrent folder, run 
``` 
mkdir vpnconfig
```

Export username and password of NordVPN as environment variables [retrieve from here](https://my.nordaccount.com/nl/dashboard/nordvpn/manual-configuration/)
```
export NORDVPNUSER=<username>
export NORDVPNPASS=<password>
```

Using NordVPN
```
$ sudo docker run --cap-add=NET_ADMIN -d \
-v /home/torrent/:/data \
-v /home/torrent/vpnconfig/:/config \
-e OPENVPN_PROVIDER=NORDVPN \
-e NORDVPN_COUNTRY=NL \
-e OPENVPN_USERNAME=$NORDVPNUSER \
-e OPENVPN_PASSWORD=$NORDVPNPASS \
-e LOCAL_NETWORK=192.168.1.0/24 \
--log-driver json-file \
--log-opt max-size=10m \
-p 9091:9091 \
-d --restart unless-stopped \
-d --name torrentserver \
haugene/transmission-openvpn
```

## Test your Torrent server behind a VPN connection
Go to [ipleak.net](https://ipleak.net/) and under Torrent Address detection copy the magnet link, paste it in your torrentserver and see on the ipleak site if the ipaddress returned from the detection script is the same as the ipaddress that is displayed on the top of the page.
