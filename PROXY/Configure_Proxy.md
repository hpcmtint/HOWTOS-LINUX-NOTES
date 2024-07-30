# How to Configure Proxy On Ubuntu 22.04 | by Mohamed Mansoor Zaheer Hussain | Medium

![Mohamed Mansoor Zaheer Hussain](https://miro.medium.com/v2/resize:fill:88:88/1*dJwRSms7Ebvp-dgXjOM2Rw.jpeg)
[Mohamed Mansoor Zaheer Hussain](https://medium.com/@zaheernew)

HI HI, Greetings!

This is my first post for Medium.

Today, I would like to explain How to Configure a Proxy On Ubuntu 22.04. This is extremely helpful for all users. In this article, I only covered command line configuration in Ubuntu 22.04 system. Let’s move to the article.

**Introduction**

In order to implement Internet access controls, such as connection authentication, connection sharing, bandwidth management, and content filtering and blocking, a proxy server can function as an intermediary between the client computer and the internet.

If your office network is behind a proxy server, then you will need to configure the proxy in order to browse the Internet. However, the improved network security, privacy, and speed that proxies offer can also be advantageous to individual users.

Setting Up Proxy with Ubuntu Terminal or CLI. In order to Ubuntu system there are several methods to configure the Proxy and those are as follows,

1.  _Configure for Temporary for Single User_
2.  _Configure for Permanent for Single User_
3.  _Configure for Permanent for All Users_
4.  _Configure the APT package manager._
5.  _Configure the WGET package manager._

For every single configuration, you must have to configure the following environment variables and it will support easy internet access.

*   _HTTP_
*   _HTTPS_
*   _FTP_

**1\. Configure for Temporary for Single User**

After a system restart, a temporary proxy connection is reset. Use the following command to create that connection for the current user.

**Syntax:**

#With Authentication,  
_export HTTP\_PROXY=\[username\]:\[password\]@\[proxy-web-or-IP-address\]:\[port-number\]  
export HTTPS\_PROXY=\[username\]:\[password\]@\[proxy-web-or-IP-address\]:\[port-number\]  
export FTP\_PROXY=\[username\]:\[password\]_[_@_](https://forum.huawei.com/enterprise/en/profile/2942045) _\[proxy-web-or-IP-address\]:\[port-number\]  
export NO\_PROXY=localhost,127.0.0.1,::1_

#Without Authentication,  
_export HTTP\_PROXY=\[proxy-web-or-IP-address\]:\[port-number\]  
export HTTPS\_PROXY=\[proxy-web-or-IP-address\]:\[port-number\]  
export FTP\_PROXY=\[proxy-web-or-IP-address\]:\[port-number\]  
export NO\_PROXY=localhost,127.0.0.1,::1_

Example:

**2\. Configure for Permanent for Single User**

To make a permanent proxy setting for a single user, the configuration steps are given below, and it is not affected after rebooting.

_1- Edit the .bashrc file._  
**Syntax:**  
sudo nano ~/.bashrc

Example:

_2- Add the following commands:_  
**Syntax:**

#With Authentication,

_export HTTP\_PROXY=”\[username\]:\[password\]@\[proxy-web-or-IP-address\]:\[port-number\]”  
export HTTPS\_PROXY=”\[username\]:\[password\]@\[proxy-web-or-IP-address\]:\[port-number\]”  
export FTP\_PROXY=”\[username\]:\[password\]_[_@_](https://forum.huawei.com/enterprise/en/profile/2942403) _\[proxy-web-or-IP-address\]:\[port-number\]”  
export NO\_PROXY=”localhost,127.0.0.1,::1"_

#Without Authentication,  
_export HTTP\_PROXY=\[proxy-web-or-IP-address\]:\[port-number\]  
export HTTPS\_PROXY=\[proxy-web-or-IP-address\]:\[port-number\]  
export FTP\_PROXY=\[proxy-web-or-IP-address\]:\[port-number\]  
export NO\_PROXY=localhost,127.0.0.1,::1_

_3- Save and exit the file._  
**Syntax:**  
Ctrl+O and Yes

Example:

_4- To apply new settings, use the following commands._  
**Syntax:**  
source ~/.bashrc

Example:

**3\. Configure for Permanent for All Users**

To make a permanent proxy setting for all users, the configuration steps are given below, and it is not affected after rebooting.

_1- Edit the /etc/environment file._  
**Syntax:**

sudo nano /etc/environment

Example:

_2- Add the following commands:_  
**Syntax:**

#With Authentication,

_export HTTP\_PROXY=”\[username\]:\[password\]@\[proxy-web-or-IP-address\]:\[port-number\]”  
export HTTPS\_PROXY=”\[username\]:\[password\]@\[proxy-web-or-IP-address\]:\[port-number\]”  
export FTP\_PROXY=”\[username\]:\[password\]_[_@_](https://forum.huawei.com/enterprise/en/profile/2942523) _\[proxy-web-or-IP-address\]:\[port-number\]”  
export NO\_PROXY=”localhost,127.0.0.1,::1"_

#Without Authentication,  
_export HTTP\_PROXY=\[proxy-web-or-IP-address\]:\[port-number\]  
export HTTPS\_PROXY=\[proxy-web-or-IP-address\]:\[port-number\]  
export FTP\_PROXY=\[proxy-web-or-IP-address\]:\[port-number\]  
export NO\_PROXY=localhost,127.0.0.1,::1_

_3- Save and exit the file._  
**Syntax:**  
Ctrl+O and Yes

Example:

_4- To apply the new setting, you will need to log out and log in again to the system._

**4\. Configure the APT package manager.**

On some Linux-based systems, the Ubuntu repository (apt command-line utility) needs a separate proxy configuration, because it does not use system environment variables and the configuration steps are given below.

_1- To specify the proxy settings for_ **_apt_**_, create or edit (if it already exists) a file named_ **_apt.conf_** _in_ **_/etc/apt_** _directory._

**Syntax:**  
sudo nano /etc/apt/apt.conf

Example:

_2- Add the following commands:_  
**Syntax:**

#With Authentication,

_Acquire::http::Proxy “http://\[username\]:\[password\]_[_@_](https://forum.huawei.com/enterprise/en/profile/2942573) _\[proxy-web-or-IP-address\]:\[port-number\]”;  
Acquire::https::Proxy “http://\[username\]:\[password\]_[_@_](https://forum.huawei.com/enterprise/en/profile/2942577) _\[proxy-web-or-IP-address\]:\[port-number\]”;  
Acquire::ftp::Proxy “http://\[username\]:\[password\]_[_@_](https://forum.huawei.com/enterprise/en/profile/2942579) _\[proxy-web-or-IP-address\]:\[port-number\]”;_

#Without Authentication,  
_Acquire::http::Proxy “http://\[proxy-web-or-IP-address\]:\[port-number\]";  
Acquire::https::Proxy “http://\[proxy-web-or-IP-address\]:\[port-number\]";  
Acquire::ftp::Proxy “http://\[proxy-web-or-IP-address\]:\[port-number\]";_

_3- Save and exit the file._  
**Syntax:**  
Ctrl+O and Yes

Example:

_4- To apply the new setting, you will need to reboot the system._

**5\. Configure the WGET package manager.**

On some Linux-based systems, the Ubuntu repository (wget command-line utility) needs a separate proxy configuration, because it does not use system environment variables, and the configuration steps are given below.

_1- To specify the proxy settings for_ **_wget_**_, create or edit (if it already exists) a file named_ **_wget_** _in_ **_~/.wgetrc_** _directory._

**Syntax:**  
Sudo nano ~/.wgetrc

Example:

_2- Add the following commands:_  
**Syntax:**

#With Authentication,

_Use\_proxy = on  
http\_Proxy = http://\[username\]:\[password\]_[_@_](https://forum.huawei.com/enterprise/en/profile/2942585) _\[proxy-web-or-IP-address\]:\[port-number\]”;  
https\_Proxy = http://\[username\]:\[password\]_[_@_](https://forum.huawei.com/enterprise/en/profile/2942587) _\[proxy-web-or-IP-address\]:\[port-number\]”;  
ftp\_Proxy = http://\[username\]:\[password\]_[_@_](https://forum.huawei.com/enterprise/en/profile/2942591) _\[proxy-web-or-IP-address\]:\[port-number\]”;_

#Without Authentication,

_Use\_proxy = on  
http\_Proxy = http://\[proxy-web-or-IP-address\]:\[port-number\]";  
https\_Proxy = http://\[proxy-web-or-IP-address\]:\[port-number\]";  
ftp\_Proxy = http://\[proxy-web-or-IP-address\]:\[port-number\]";_

_3- Save and exit the file._  
**Syntax:**  
Ctrl+O and Yes

Example:

_4- To apply the new setting, you will need to reboot the system._

**Conclusion:**

You should now be educated on how to make temporary and permanent configurations to your system’s proxy configuration for the entire system. However, my personal recommendation on this scenario, you should configure the following two places in your proxy environment, and it will support your daily routine work.

*   Configure for Permanent for All Users.
*   Configure the APT package manager.

_Source:_

*   [_https://help.ubuntu.com/stable/ubuntu-help/net-proxy.html.en_](https://help.ubuntu.com/stable/ubuntu-help/net-proxy.html.en)
*   [_https://forum.huawei.com/enterprise/en/how-to-configure-proxy-on-ubuntu-22-04/thread/1052320-895_](https://forum.huawei.com/enterprise/en/how-to-configure-proxy-on-ubuntu-22-04/thread/1052320-895)

_M M Zaheer Hussain_

_Stay Safe!_
