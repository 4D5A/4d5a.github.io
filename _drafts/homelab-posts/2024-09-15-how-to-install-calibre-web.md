---
layout: post
title: How to install calibre web
categories: [homelabprojects]
tags: [proxmox, linux, container, pi-hole]
after-content: [disclaimer-notice.html]
---

apt update
sudo apt install python3-pip python3-venv
mkdir /opt/calibre-web
mkdir /library
cd /opt/calibre-web
python3 -m venv venv
./venv/bin/python3 -m pip install calibreweb
nohup ./venv/bin/cps
http://192.168.8.79:8083/
admin
admin123
nano /etc/ssh/sshd_config
Change PermitRootLogin without-password to PermitRootLogin yes
CTRL + X
Y
ENTER
service ssh restart

SSH into 192.168.8.79

From the computer that Calibre Library is installed on, copy the contents of the Calibre Library directory to /library

CTRL + C
nohup ./venv/bin/cps