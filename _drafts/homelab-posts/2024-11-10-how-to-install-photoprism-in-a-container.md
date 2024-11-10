---
layout: post
title: HHow to install Photoprism in a container
categories: [homelabprojects]
tags: [proxmox, linux, container]
after-content: [disclaimer-notice.html]
---


apt update
apt install docker-ce
wget https://dl.photoprism.app/docker/compose.yaml
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  tee /etc/apt/sources.list.d/docker.list > /dev/null
apt-get update

apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

apt install cifs-utils
mkdir /media/appdata
nano /root/.smb

username=YOUR_USERNAME
password=YOUR_PASSWORD

nano /etc/fstab
//YOURIP/home/appdata /media/appdata cifs credentials=/root/.smb,uid=0,gid=0,dir_mode=0777,file_mode=0777,users,rw,iocharset=utf8,noperm 0 0

mount -a

- "/media/appdata/originals:photoprism/originals"
- "/media/appdata/import:photoprism/import"

docker compose up -d

docker compose exec photoprism photoprism users add -p YOURPASSWORD -n YOURNAME -m YOUREMAILADDRESS YOURUSERNAME
