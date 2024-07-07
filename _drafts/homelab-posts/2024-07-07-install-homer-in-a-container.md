---
layout: post
title: Install Homer in a Container
categories: [homelabprojects]
tags: [proxmox, linux, container, homepage]
after-content: [disclaimer-notice.html]
---

debian-12-standard_12.2-1_amd64.tar.zst


~~~
apt update
apt install unzip nginx
wget https://github.com/bastienwirtz/homer/releases/latest/download/homer.zip
unzip homer.zip -d /var/www/homer
cd /var/www/homer
cp assets/config.yml.dist assets/config.yml
systemctl start nginx
systemctl enable nginx
nano /etc/nginx/sites-available/homer
~~~


~~~
server {
    listen 80;
    server_name homer;
    root /var/www/homer;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
~~~

~~~
nginx -t
~~~

~~~
ln -s /etc/nginx/sites-available/homer /etc/nginx/sites-enabled/
rm /etc/nginx/sites-enabled/default
systemctl reload nginx
~~~

~~~
hostname -I
~~~