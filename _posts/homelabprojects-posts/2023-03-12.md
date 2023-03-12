---
layout: post
title: Install Homepage in a Container
categories: [homelabproject]
tags: [proxmox, linux, container, homepage]
after-content: [disclaimer-notice.html]
---
Use Alpine Linux to create a proxmox container
~~~
apt update
apk add nano npm git
git clone https://github.com/benphelps/homepage.git
mkdir /root/homepage/config
cd /root/homepage
npm run build
npm run start
~~~

If you want to change the port homepage uses from 3000 to 80, do the following.

```npm install dotenv```

Create a script for your prod environment in the project root e.g. prod-server.js
~~~
// prod-server.js
require('dotenv').config(); // require dotenv
const cli = require('next/dist/cli/next-start');

cli.nextStart(['-p', process.env.PORT || 80]);

~~~

Update the start command in your package.json to use the prod-server.js script like this:

~~~
  "scripts": {
    "build": "next build",
    "start": "node prod-server.js"
  }

~~~

```npm run build```

```npm run start```

Post an answer for the question on https://github.com/benphelps/homepage/discussions/579.

The documentation for making these changes is at https://stackoverflow.com/questions/60147499/how-to-set-port-in-next-js