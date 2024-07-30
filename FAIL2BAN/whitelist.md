# How to whitelist an IP in Fail2ban on Debian Linux
[![Howtoforge](https://www.howtoforge.com/images/howtoforge_logo_trans.gif)](https://www.howtoforge.com/)

How to whitelist an IP in Fail2ban on Debian Linux
--------------------------------------------------

Fail2Ban is used to protect servers against brute-force attacks. Fail2ban uses iptables to block attackers, so if we want to add permanent IP address and never be blocked, we must add it in the config file.

First, edit the config file :

```
nano /etc/fail2ban/jail.local
```


Suppose you do not have a jail.local file, edit the file jail.conf instead:

```
nano /etc/fail2ban/jail.conf
```


Then, check the line :

```
ignoreip =
```


Add all IP addresses you want now. Each IP or range IP must be separated here with a whitespace. Ex: 192.168.0.1 192.168.5.0/32

Example:

```
ignoreip = 192.168.0.1 192.168.5.0/32
```


The line should be added in the **\[DEFAULT\]** section of the file.

Save the file and restart Fail2Ban:

```
service fail2ban restart
```


That's all.

7 Comment(s)
------------

×

This feature is only available to subscribers. Get your subscription [here](https://www.howtoforge.com/subscription).
