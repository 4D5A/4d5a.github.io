---
layout: post
title: Unblocking yourself from WHM
gh-repo: 4D5A
gh-badge: [follow]
tags: [WHM, Linux, VPS]
after-content: [disclaimer-notice.html]
---
WHM provides some decent out of the box security tools. Those include iptables, cPHulk Brute Force Protection, and fail2ban. If you are using both cPHulk and fail2ban, you may end up getting locked out of WHM and not knowing how to log back in. This is more likely to happen if you only Allowlisted your public IP address in cPHulk or fail2ban and not both. cPHulk offers a web-UI and is easier for people who are new to linux to configure than fail2ban so it is understandable for someone to Allowlist their public IP address in cPHulk but not fail2ban. This may be a problem if fail2ban ever bans your public IP address as the result of a false positive (this may happen if you are logging into multiple cPanel accounts on your WHM server and there is a problem with the authentication token).

If this occurs, your IP might only be blocked from accessing your server using the URI https://fqdn:2087 (where fqdn is your WHM server’s Fully Qualified Domain Name) but not https://whm.domainname.com (where domainname.com is your domain name). If you can login to https://whm.domainname.com you might be able to correct the problem without contacting your hosting provider.

After you login to WHM, enter “Terminal” in the search box and under “Server Configuration” click “Terminal”. Since cPHulk and fail2ban both rely on iptables, you need to remove your Blocklisted IP from both iptables chains. To see if your IP address is in the cPHulk Blocklist, you can use the following command:

~~~
cat /usr/local/cpanel/logs/cphulkd.log | grep "IP"
~~~

You will want to substitute your IP address for “IP”.<sup>1</sup> You can use the following command to Allowlist your IP from cPHulk where “IP” is the IP address you wish to Allowlist<sup>2</sup>:

~~~
/usr/local/cpanel/scripts/cphulkdwhitelist "IP"
~~~

To see if your IP address is in the fail2ban Blocklist, you can use the following command:

~~~
cat /var/log/fail2ban.log | grep "IP"
~~~

You will want to substitute your IP address for “IP”. You can Allowlist your IP in fail2ban by editing /etc/fail2ban/jail.conf where “IP” is the IP address you wish to Allowlist (the example assumes you want to Allowlist one IP address which is why it includes “/32”.

~~~
sudo nano /etc/fail2ban/jail.conf
ignoreip = "IP"/32
~~~

-Press CTRL + X
-Type Y
-Press Enter

Restart the fail2ban service by running the following command:

~~~
sudo service fail2ban restart
~~~

You can use the following commands to unban your IP from fail2ban where “IP” is the IP address you wish to unban.

~~~
fail2ban-client set cpanel-iptables unbanip "IP"
fail2ban-client set recidive unbanip "IP"
~~~

[1] [https://docs.cpanel.net/knowledge-base/security/cphulk-management-on-the-command-line/](https://docs.cpanel.net/knowledge-base/security/cphulk-management-on-the-command-line/)

[2] [https://docs.cpanel.net/knowledge-base/security/cphulk-management-on-the-command-line/](https://docs.cpanel.net/knowledge-base/security/cphulk-management-on-the-command-line/)