# Installing and Using Fail2ban on Debian 12
[![Howtoforge](data:image/svg+xml,%3Csvg%20xmlns=%22http://www.w3.org/2000/svg%22%20width=%22215%22%20height=%2264%22%3E%3C/svg%3E)](https://www.howtoforge.com/)

Installing and Using Fail2ban on Debian 12
------------------------------------------

### On this page

1.  [How it works](#how-it-works)
2.  [Setting up Fail2ban](#setting-up-failban)
    1.  [Enabling a jail](#enabling-a-jail)
    2.  [Follow the logs](#follow-the-logs)
    3.  [Troubleshooting](#troubleshooting)
    4.  [Whitelisting own IP-addresses](#whitelisting-own-ipaddresses)
    5.  [Banning manually](#banning-manually)
    6.  [Unbanning manually](#unbanning-manually)
    7.  [Conclusion](#conclusion)

Fail2ban monitors log files for login failures and temporarily bans the failure-prone source IP address from accessing the host. This is a defense against password-guessing brute-force attacks. It is very useful to have fail2ban on hosts exposed to the Internet.

The version of fail2ban on Debian 12 is 1.0.2.

```
root@posti:~# fail2ban-client version
1.0.2
```


To see if fail2ban is currently running, send to the server command ping with fail2ban-client. Fail2ban server answers "pong" if it is running.

```
root@posti:/etc/fail2ban# fail2ban-client ping
Server replied: pong
root@posti:/etc/fail2ban#
```


How it works
------------

Fail2ban collects filter, action, and monitored files to a jail. Several jails come with the distribution. They need to be enabled to start working. filter specifies how to detect authentication failures. action defines how the banning and unbanning happens.

Intrusion attempts trigger ban if during findtime at least maxretry login failures are detected from same IP-number. Then the IP is banned for bantime seconds. After ban has been in effect for bantime seconds, the ban is lifted and the IP can again access the host. Fail2ban can handle both IPv4 and IPv6.

More information can be read from the files in directory /usr/share/doc/fail2ban/.

Setting up Fail2ban
-------------------

On Debian GNU/Linux systems installs fail2ban by default with sshd jail enabled with reasonable settings. This is done in file /etc/fail2ban/jail.d/defaults-debian.conf. That is the only jail working by default. Other jails must be enabled by the system administrator.

So, if you only want ssh logins monitored and misbehaving intruders banned, no additional configuration is necessary.

Unfortunately, on Debian 12, fail2ban may not work with default settings. Debian 12 marked rsyslog package as optional, meaning it may not be installed so logs are collected only to journald \[wiki.debian.org/Rsyslog\].

On ISPConfig systems fail2ban does work, since ISPConfig autoinstaller installs rsyslog and the ISPConfig Perfect server tutorial instructs to install rsyslog.

With rsyslog, log files appear in /var/log/ -directory, thus fail2ban default configuration finds the log files. If rsyslog is not installed, fail2ban configuration must be modified so it reads the logs from systemd journal. Add to jail.local default section backend = systemd, like so

```
[DEFAULT]
backend = systemd
```


### Enabling a jail

Documentation recommends putting all own modifications to .local files. This avoids problems when files provided by distribution are upgraded or modified by the maintainer \[Read file /usr/share/doc/fail2ban/README.Debian.gz\].

As an example, enabling pure-ftpd jail is done by adding to file /etc/fail2ban/jail.local (create the file if it does not exist already) the lines

```
[pure-ftpd]
enabled = true

```


Jail name in square brackets starts section for settings of that jail.

When finished with modifications, force rereading configuration files with command

```
systemctl reload fail2ban
```


The jail should now be running, which can be verified with fail2ban-client:

```
root@posti:/etc/fail2ban# fail2ban-client status pure-ftpd
Status for the jail: pure-ftpd
|- Filter
|  |- Currently failed:	0
|  |- Total failed:	106
|  `- File list:	/var/log/syslog
`- Actions
   |- Currently banned:	0
   |- Total banned:	4
   `- Banned IP list:	
root@posti:/etc/fail2ban# 
```


Since jail.local has only the "enabled" setting for that jail, all other settings are default settings from the distribution. Usually they have good values so further configuration is unnecessary. If needed, jail.local section for that jail can contain settings that override what was set in .conf files.

This example changes findtime, maxretry and bantime for pure-ftpd jail:

```
[pure-ftpd]
enabled = true
findtime = 2h
maxretry = 6
bantime = 1d
```


Fail2ban-client shows times in seconds, but times can be entered in configuration files in easier format, for example 10h instead of 36000 seconds. man jail.conf in chapter "TIME ABBREVIATION FORMAT" explains the user friendly time input formats.

fail2ban-client has option to convert from user friendly time format to seconds.

```
# fail2ban-client --str2sec 1d3h7m
97620
```


### Follow the logs

To determine what jails should be activated, follow the logs created by services running on the host. A tool that creates summaries of logs, like logwatch or pflogsumm, is helpful. Reading raw logs is time consuming and tedious.

Check if there is a jail available for a service running on the host, perhaps reading jail.conf.

Once logs show something interesting or worrisome, it is time to examine. For example, pflogsumm sent e-mail summary with lines like this:

```
136   unknown[91.224.92.40]: SASL LOGIN authentication failed: UGFzc3...
136   hostname srv-91-224-92-40.serveroffer.net does not resolve to a...
123   unknown[193.32.162.23]: SASL LOGIN authentication failed: UGFzc...
123   hostname mail.whatami.co does not resolve to address 193.32.162.23

```


That shows IP 91.224.92.40 failed 136 times logging to e-mail, and another similar case. Fail2ban should have prevented so many attempts. To see why that did not happen, examine fail2ban log.

```
root@posti:/etc/apt/apt.conf.d# grep 91.224.92.40 /var/log/fail2ban.log | head
2024-02-18 00:01:38,718 fail2ban.filter         [996]: INFO    [postfix-sasl] Found 91.224.92.40 - 2024-02-18 00:01:38
2024-02-18 00:11:50,261 fail2ban.filter         [996]: INFO    [postfix-sasl] Found 91.224.92.40 - 2024-02-18 00:11:50
2024-02-18 00:21:54,337 fail2ban.filter         [996]: INFO    [postfix-sasl] Found 91.224.92.40 - 2024-02-18 00:21:54
2024-02-18 00:32:14,232 fail2ban.filter         [996]: INFO    [postfix-sasl] Found 91.224.92.40 - 2024-02-18 00:32:14
2024-02-18 00:42:37,921 fail2ban.filter         [996]: INFO    [postfix-sasl] Found 91.224.92.40 - 2024-02-18 00:42:37
2024-02-18 00:53:06,796 fail2ban.filter         [996]: INFO    [postfix-sasl] Found 91.224.92.40 - 2024-02-18 00:53:06
2024-02-18 01:03:35,293 fail2ban.filter         [996]: INFO    [postfix-sasl] Found 91.224.92.40 - 2024-02-18 01:03:35
2024-02-18 01:14:03,765 fail2ban.filter         [996]: INFO    [postfix-sasl] Found 91.224.92.40 - 2024-02-18 01:14:03
2024-02-18 01:24:24,628 fail2ban.filter         [996]: INFO    [postfix-sasl] Found 91.224.92.40 - 2024-02-18 01:24:24
2024-02-18 01:34:43,876 fail2ban.filter         [996]: INFO    [postfix-sasl] Found 91.224.92.40 - 2024-02-18 01:34:43
root@posti:/etc/apt/apt.conf.d#

```


That IP tries at about 10 minute intervalls connecting, and fail2ban jail postfix-sasl does detect it.

It is a good idea to find out if that IP maybe belongs to a legitimate user of the host, who just has old password in smartphone or some other device trying to connect at regular intervalls. I use geoiplookup, it shows what country IP is from. My users are from my country so foreign users tend to be bad actors. geoiplookup comes from Debian packages geoip-database and geoip-bin.

```
$ geoiplookup 91.224.92.40
GeoIP Country Edition: LT, Lithuania

```


To find out why IP was not banned, examine findtime of that jail:

```
root@posti:/etc/apt/apt.conf.d# fail2ban-client get postfix-sasl findtime
600
root@posti:/etc/apt/apt.conf.d#

```


I have recently seen these slow attacks, perpetrator knows findtime is 10 minutes so tries to avoid getting banned by trying logins at longer intervals. That worked in this case. To make it so failed logins do cause bans, increase findtime for the jail, for example to 10 hours. Add to file jail.local in \[postfix-sasl\] section:

```
findtime = 10h

```


Then after systemctl reload fail2ban the jail has longer findtime:

```
root@posti:/etc/fail2ban# fail2ban-client get postfix-sasl findtime
36000
root@posti:/etc/fail2ban#

```


Another way intruders try to break in is being persistent. Even if intruder is banned, just wait until ban ends and continue guessing password. The bantime could be made longer, but that causes issues for legitimate users, who may mistype password and then can not access their account until bantime ends. There is a jail for repeat offenders called recidive. It works by looking for repeated bans for an IP in fail2ban log, and then banning for a long time, for example a week.

The following listing is from logwatch report:

```
--------------------- pam_unix Begin ------------------------ 

 sshd:

    Authentication Failures:

       unknown (212.70.149.150): 59 Time(s)
```


Searching for that IP in the log file shows:

```
2024-02-21 03:42:39,121 fail2ban.filter         [895]: INFO    [sshd] Found 212.70.149.150 - 2024-02-21 03:42:38
2024-02-21 03:42:39,508 fail2ban.actions        [895]: NOTICE  [sshd] Ban 212.70.149.150
2024-02-21 03:52:38,386 fail2ban.actions        [895]: NOTICE  [sshd] Unban 212.70.149.150
2024-02-21 03:54:33,560 fail2ban.filter         [895]: INFO    [sshd] Found 212.70.149.150 - 2024-02-21 03:54:33
2024-02-21 03:54:35,364 fail2ban.filter         [895]: INFO    [sshd] Found 212.70.149.150 - 2024-02-21 03:54:35
2024-02-21 04:00:37,017 fail2ban.filter         [895]: INFO    [sshd] Found 212.70.149.150 - 2024-02-21 04:00:36
2024-02-21 04:00:39,021 fail2ban.filter         [895]: INFO    [sshd] Found 212.70.149.150 - 2024-02-21 04:00:38
2024-02-21 04:06:43,036 fail2ban.filter         [895]: INFO    [sshd] Found 212.70.149.150 - 2024-02-21 04:06:42
2024-02-21 04:06:45,039 fail2ban.filter         [895]: INFO    [sshd] Found 212.70.149.150 - 2024-02-21 04:06:44
2024-02-21 04:06:45,426 fail2ban.actions        [895]: NOTICE  [sshd] Ban 212.70.149.150
2024-02-21 04:16:44,302 fail2ban.actions        [895]: NOTICE  [sshd] Unban 212.70.149.150
2024-02-21 04:19:04,868 fail2ban.filter         [895]: INFO    [sshd] Found 212.70.149.150 - 2024-02-21 04:19:04

```


Fail2ban works as intended, finding the wrong logins and issuing a ban for 10 minutes. During this ban the offender is prevented from accessing this host, but continues after the ban is lifted. This seems to be the case of serious attempt to break in. The remedy is to enable recidive jail with low maxretry and long bantime.

I like to use short initial ban, say 10 minutes. If a real user got banned it's just waiting for that time, hopefully using the time finding what the correct password is and then trying again. Those who get banned again the same day, get banned for a week. The default values of recidive jail can be found in file jail.conf, there is bantime of 1 week and findtime of 1 day, those seem OK to me. But I set maxretry to 2, offenders are banned quicker. I added to file jail.local:

```
[recidive]
enabled = true
maxretry = 2

```


Longer than 1 week bans are not worthwhile, in my opinion. Eventually, the old IP-address is banned or blacklisted all over the place, so the offender gets a new IP. The old IP is given to some innocent new Internet user, who should not be punished for the bad things the previous owner of that IP is guilty of.

### Troubleshooting

After booting the OS or restart of fail2ban state is restored, so bans continue until bantime elapses. Thus, testing and troubleshooting can involve restarts.

To test configuration, there is a test option:

```
fail2ban-server --test
```


To find out if an IP is banned, recent versions of fail2ban can do

```
# fail2ban-client banned 43.131.9.186
[['recidive']]
```


The command shows list of jails where given IP is currently banned.

Older versions of fail2ban do not have that command. Testing each jail one by one can be done like this:

```
# fail2ban-client status recidive | tr " " "\n" | grep 43.163.219.232
43.163.219.232
```


If output shows the IP, then it was in the list of banned IP numbers. The tr command is there to break the list of banned IP-numbers (it is one long line) to one IP per line.

To see what has been happening with a given IP, look it up in fail2ban.log:

```
root@posti:/etc/mysql# grep 43.131.9.186 /var/log/fail2ban.log | cut --characters=-80

2024-03-06 09:00:40,295 fail2ban.filter  [3574846]: INFO    [sshd] Found
2024-03-06 09:02:53,954 fail2ban.filter  [3574846]: INFO    [sshd] Found
2024-03-06 09:02:55,958 fail2ban.filter  [3574846]: INFO    [sshd] Found
2024-03-06 09:04:34,193 fail2ban.filter  [3574846]: INFO    [sshd] Found
2024-03-06 09:04:36,195 fail2ban.filter  [3574846]: INFO    [sshd] Found
2024-03-06 09:04:36,388 fail2ban.actions [3574846]: NOTICE  [sshd] Ban 43
2024-03-06 09:04:36,626 fail2ban.filter  [3574846]: INFO    [recidive] Fo
2024-03-06 09:14:35,180 fail2ban.actions [3574846]: NOTICE  [sshd] Unban
2024-03-06 09:15:10,073 fail2ban.filter  [3574846]: INFO    [sshd] Found
2024-03-06 09:16:55,919 fail2ban.filter  [3574846]: INFO    [sshd] Found
2024-03-06 09:16:58,522 fail2ban.filter  [3574846]: INFO    [sshd] Found
2024-03-06 09:18:44,972 fail2ban.filter  [3574846]: INFO    [sshd] Found
2024-03-06 09:20:30,018 fail2ban.filter  [3574846]: INFO    [sshd] Found
2024-03-06 09:20:30,499 fail2ban.actions [3574846]: NOTICE  [sshd] Ban 43
2024-03-06 09:20:30,620 fail2ban.filter  [3574846]: INFO    [recidive] Fo
2024-03-06 09:20:30,899 fail2ban.actions [3574846]: NOTICE  [recidive] Ba
2024-03-06 09:20:32,021 fail2ban.filter  [3574846]: INFO    [sshd] Found
2024-03-06 09:30:29,289 fail2ban.actions [3574846]: NOTICE  [sshd] Unban

```


Verbose dumping of the fail2ban configuration shows settings for examination:

```
# fail2ban-client -vvv -d
```


### Whitelisting own IP-addresses

Maybe your own hosts get banned or you want to make sure some foreign hosts never get banned even if they behave badly. Those ip-numbers can be whitelisted. By default nothing is whitelisted, you can verify this with:

```
root@posti:~# fail2ban-client get sshd ignoreip
No IP address/network is ignored
root@posti:~
```


The ignoreip setting could be set in each jails section, but this setting should affect all jails so it is better to set it in the DEFAULT section. It might be best to set ignore to the internal subnet, so all your own hosts avoid bans. This is from beginning of jail.local:

```
[DEFAULT]
ignoreip = 92.237.123.96/27

```


Default section can have other settings that affect all jails. For example, findtime = 10h could be added there.

### Banning manually

To test what happens when banning, set ban manually. Test first with a jail with short bantime, so you get back eventually if you mistakenly ban yourself. For example

```
# fail2ban-client set sshd banip 8.8.4.4</>
```


If some IP behaves badly but you can not make fail2ban detect it and issue bans, setting manual ban in recidive jail gets rid of that IP for a week.

```
# fail2ban-client set recidive banip 8.8.4.4
```


Banning a subnet works using CIDR notation:

```
fail2ban-client set recidive banip 5.188.87.0/24
```


### Unbanning manually

Removing a ban is possible, but do consider that if the bad behaviour continues the IP is going to get banned again. So you should find out what is happening (by reading the logs, for example) and fix the bad behaviour.

```
# fail2ban-client set recidive unbanip 8.8.4.4
```


The IP may be banned in several jails. To unban an IP in all jails, use

```
# fail2ban-client unban 8.8.4.4
```


I seldom need to do that, however. I have 10 minute bantime, except recidive jail has 1 week, so I only unbanip from recidive jail, other jails have already expired. If you do not use recidive jail, IP may be banned in several jails at the same time so this is useful.

To unban all IP-addresses from all jails do

```
fail2ban-client unban --all
```


### Conclusion

Fail2ban makes guessing passwords more difficult but does not completely prevent crackers from trying to access host. For SSH, forcing the use of SSH keys offers more protection by preventing login with a password. For other services, multi-factor authentication prevents login if only the password is known.

1 Comment(s)
------------

×

This feature is only available to subscribers. Get your subscription [here](https://www.howtoforge.com/subscription).
