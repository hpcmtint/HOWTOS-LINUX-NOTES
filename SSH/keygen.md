Today’s article offers a straightforward tutorial on boosting the security of your SSH remote sessions. We’ll focus on setting up SSH Key-based Authentication for your Linux server and show you how to transition away from less secure Password-based authentication. To wrap up, I’ll share bonus tips on additional ways to secure your SSH server.

## Overview

**SSH (Secure Shell) key-based authentication** is a security method that uses a pair of cryptographic keys to authenticate a user’s identity on a remote server. This approach involves generating and storing a public-private key pair on your computer and configuring the SSH server to recognize and accept these keys. It significantly improves security by reducing the risks associated with traditional Password-based Authentication.

> Using SSH keys for authentication is like having a secret handshake to get into an exclusive club, except this one keeps the hackers out and you don’t need to remember any awkward dance moves!

SSH keys are created in pairs and saved as plain-text files. A key pair consists of two parts:

-   🔒 **Public Key**: This part is stored on the SSH server. It can be safely shared, allowing the server to recognize your identity when you connect.
-   🔑 **Private Key:** This part is stored on your computer and should be kept secure at all times. It’s used to authenticate with the server and should never be shared. The private key should be set with permissions so that no other users on your computer can read the file.

> ⚠️**Warning**: Never share your private SSH key with anyone; it’s the key to your identity and server access. When a site or a service requests your SSH key, they are likely referring to your public key.

## How it works

1.  You generate a public-private key pair on your computer.
2.  The server is configured to recognize the public key and grant access if the corresponding private key is used during authentication.
3.  Optionally, protect the private key with a passphrase for additional security. This passphrase provides an extra layer of protection in case the key falls into the wrong hands.

## Setting up Public Key Authentication For SSH

Now that we’ve covered the basics, let’s dive into the steps to get this task done.

There are two options for creating the SSH key pair: on a Windows machine or a Linux machine. While the steps are generally similar, the software tools used in each environment differ.

## Method 1: Generating SSH key pair on Linux

**Step 1: Generate a new SSH key pair using the Ed25519 algorithm.**

This step assumes you are creating the key pair on your local computer and later uploading them to the server. This is safer than creating them on the server and downloading your private key back to your local host.

> You’d always want to keep sight of your private key during generation and storage.

Generate a key pair using the Ed25519 algorithm:

```
<span id="ba38" data-selectable-paragraph="">ssh-keygen -t ed25519 -C "cybersecmav@warfare.systems"</span>
```

-   `**-t**`: Type of key (algorithm), in our case Ed25519.
-   `**-C**`: Comment (optional) to help you distinguish between SSH keys.

> **Ed25519** is a public-key signature algorithm that belongs to the family of elliptic curve cryptography (ECC). It’s designed for strong security with smaller key sizes and is widely used in SSH key-based authentication. It has recently replaced RSA as the widely recommended algorithm.

If prompted for a file name, press Enter to accept the default name and path. SSH keys are typically stored in the `~/.ssh/` directory.

![SSH key pair generation on a local Linux machine](https://miro.medium.com/v2/resize:fit:638/1*qS4VolXnVMO5KlZGmvIg-Q.png)

SSH key pair generation on a local Linux machine

> By default, **Ed25519** private keys are saved as `id_ed25519`, while RSA private keys are saved as `id_rsa`. Public keys have the same name as their corresponding private keys but with a `.pub` extension (e.g., `id_ed25519.pub`).

**Step 2: Prepare the remote server**

Create the **~/.ssh** directory and **authorized\_keys** file if they don’t exist:

```
<span id="edde" data-selectable-paragraph=""><span>mkdir</span> ~/.ssh<br><span>touch</span> ~/.ssh/authorized_keys</span>
```

The **authorized\_keys** file, located in the `~/.ssh/` directory contains a list of public keys authorized to access your SSH server.

Set the appropriate file permissions for the **~/.ssh** directory and **authorized\_keys** files appropriate file permissions:

```
<span id="5296" data-selectable-paragraph=""><span>chmod</span> 700 ~/.ssh<br><span>chmod</span> 600 ~/.ssh/authorized_keys</span>
```

-   **chmod 700 ~/.ssh**: allows only the owner to read, write, or execute files in that directory.
-   **chmod 600 ~/.ssh/authorized\_keys**: restricts access to the file to just the owner, preventing others from viewing or modifying it, to safeguard authorized SSH keys.

> If you have created the key pair on a local machine, you will need to upload your public SSH key **id\_ed2551.pub** to the remote server’s ~/.ssh directory. Secure Copy (SCP) is highly recommended.

Upload the public key from your local machine to the remote server:

```
<span id="7c68" data-selectable-paragraph="">scp id_ed2551.pub cybersecmav@warfare.systems:/home/cybersecmav/id_ed2551.pub</span>
```

Move the public key you uploaded to the **.ssh/** directory:

```
<span id="43f9" data-selectable-paragraph=""><span>mv</span> id_ed2551.pub ~/.ssh/<br><span>chmod</span> 600 ~/.ssh/id_ed2551.pub</span>
```

Copy the output of the public key to the authorized\_keys file:

```
<span id="3f1f" data-selectable-paragraph="">cat ~<span>/.ssh/i</span>d_ed2551.<span>pub</span> &gt;&gt; ~<span>/.ssh/</span>authorized_keys</span>
```

View the contents of authorized\_keys to verify the public key was added:

```
<span id="363c" data-selectable-paragraph=""><span>cat</span> .ssh/authorized_keys<br>ssh-ed25519 AAA&lt;&lt;...REDATCTED......&gt;&gt;1uEqXysh cybersecmav@warfare.systems</span>
```

Let’s verify we have all the right folder and file permissions:

```
<span id="f71d" data-selectable-paragraph="">cybersecmav@ares:~$ cd .ssh<br>cybersecmav@ares:~/.ssh$ ls -la<br>total 16<br>drwx------ 2 cybersecmav cybersecmav 4096 Apr 26 14:32 .<br>drwxr-xr-x 5 cybersecmav cybersecmav 4096 Apr 29 13:32 ..<br>-rw------- 1 cybersecmav cybersecmav  109 Apr 26 14:33 authorized_keys<br>-rw-r--r-- 1 cybersecmav cybersecmav  109 Apr 23 19:49 id_ed2551.pub</span>
```

**Step 3: Configure the SSH server to support Public Key Authentication**

Open the SSH Server Configuration file for editing:

```
<span id="db94" data-selectable-paragraph="">sudo nano /etc/ssh/sshd_config</span>
```

Scroll down to locate the **PubkeyAuthentication** setting. Change it from "no" to "**yes**.". Also, ensure that the line beginning with **AuthorizedKeysFile** is uncommented or added if it’s missing.

```
<span id="cf16" data-selectable-paragraph="">PubkeyAuthentication <span>yes</span><br>AuthorizedKeysFile .ssh/authorized_keys .ssh/authorized_keys2</span>
```

This is how the **/etc/sshd\_config** file should look like:

![Modify the SSH server config file to enable Publick Key Authentication](https://miro.medium.com/v2/resize:fit:616/1*wTwAD0aQqtUAWlnYZdS7pQ.png)

Modify the SSH server config file to enable Publick Key Authentication

> Remember, we haven’t disabled **password authentication** yet. We’ll only do that after thoroughly testing and confirming that our SSH key authentication works perfectly.

**Step 4: Reload the SSH server to apply the configuration change**

Whenever you make changes to the sshd\_config file, you need to either _reload_ or _restart_ the SSH server for the changes to take effect.

**Debian/Ubuntu:**

```
<span id="46c9" data-selectable-paragraph="">sudo systemctl reload ssh<br>sudo systemctl restart ssh</span>
```

**Redhat/CentOS:**

```
<span id="9bf5" data-selectable-paragraph="">sudo systemctl reload sshd<br>sudo systemctl restart sshd</span>
```

## Bonus: SSH Server Security Tips

Now that we’ve covered the essentials of SSH key-based authentication, let’s explore some additional security tips to further protect your SSH server. Implementing these best practices will boost your SSH server security.

## Tip 1: Disable Password Authentication

Once you’re sure SSH key-based authentication works, disable password-based login in the SSH configuration file `**/etc/ssh/sshd_config**` by setting **_PasswordAuthentication_** to “**no**”.

```
<span id="cbf2" data-selectable-paragraph="">sudo nano /etc/ssh/sshd_config</span>
```

![Disable Password Authentication](https://miro.medium.com/v2/resize:fit:640/1*sOAg8g2bQvQmcX9Lf4C-wA.png)

Disable Password Authentication

## Tip 2: Restrict Root Login

Prevent direct root access via SSH by setting **_PermitRootLogin_** to “**no**”. This requires users to log in with a non-root account and then use `**sudo**` for administrative tasks.

![Restrict root Logins](https://miro.medium.com/v2/resize:fit:571/1*9Ay91ewR2EDBGtwfyyQU4A.png)

Restrict root Logins

## Tip 3: Use a Non-standard SSH Port

Changing the default SSH port from 22 to a higher, unusual number can make it harder for blind automated scans and brute-force attacks, which make up the bulk of threats. However, keep in mind that this doesn’t completely shield your SSH server from all attacks; determined hackers can still find and target your SSH port.

> Avoid common ports like 2222, often targeted by automated attacks. Choose a higher port, such as 62022, to reduce the chances of detection by typical port scans. Though no port is completely safe, high numbers make it harder for attackers who don’t scan all 65,535 ports.

![Use Non-standard SSH port to dodge automated scans and brute force attempts](https://miro.medium.com/v2/resize:fit:700/1*pVGOCT8mDF8C051NU-jduA.png)

Use Non-standard SSH port to dodge automated scans and brute force attempts

## Tip 4: Enable SSH Logging and Monitoring

Ensure SSH logging is enabled to track connection attempts and detect unusual activity. Regularly monitor logs for signs of unauthorized access or brute-force attacks.

In most systems, SSH logging is enabled by default. However, check your SSH configuration file `**/etc/ssh/sshd_config**` to ensure it's not disabled.

**Step 1: Enable SSH logging in the SSH service configuration file**

Set **_SyslogFacility_** to “**AUTH**” and **_LogLevel_** to “**INFO**”.

![SSH Server Logging](https://miro.medium.com/v2/resize:fit:439/1*FvSkchieV4qeagR_qu7jmQ.png)

SSH Server Logging

If you want to explore other levels of logging, see the section below. I would advise against **_Verbose_** and **_Debug_** as they generate extensive logs and are only useful for troubleshooting or debugging.

**SSH Log levels**

-   **QUIET**: Logs almost nothing, providing minimal output.
-   **FATAL**: Logs only critical errors that stop the SSH server from functioning.
-   **ERROR**: Logs significant issues and serious errors.
-   **INFO**: The default level, logging general information about SSH operations.
-   **VERBOSE**: Logs detailed information about SSH processes; useful for troubleshooting.
-   **DEBUG**: Produces extensive logs for debugging; typically not for everyday use.

**Step 2: Configure log storage**

SSH logs are typically stored in **/var/log/auth.log** (Debian/Ubuntu) or **/var/log/secure** (Red Hat/CentOS).

```
<span id="cd08" data-selectable-paragraph="">$ <span>ls</span>  -la /var/log/auth.log<br>-rw-r----- 1 root adm 292525 May  4 10:18 /var/log/auth.log</span>
```

Verify these files are present and accessible. Check the Owner is set to root and the file permissions are set appropriately to secure the log file.

```
<span id="428b" data-selectable-paragraph=""><span>chmod</span> 640 /var/log/auth.log</span>
```

## Tip 5: Implement Intrusion Detection (Fail2ban)

[**Fail2Ban**](https://github.com/fail2ban/fail2ban) can protect your SSH server by monitoring login attempts and blocking IP addresses after too many failed attempts, which is a common sign of brute-force attacks. After installing it, set it to watch your SSH logs and configure the rules to ban suspicious IPs. It’s an effective way to add an extra layer of security to your SSH server.

> Don’t forget to add your non-standard SSH port to the Fail2Ban configuration. I also suggest keeping the default port 22 in the list, so you can catch and block those failed attempts there too!

Setting up and configuring _Fail2Ban_ is outside the scope of this article, but I highly recommend having it on your server for added security.

## Conclusion

In this article, we’ve covered the benefits of SSH key-based authentication, highlighting its enhanced security and reduced risk of brute-force attacks. We walked through the setup process, showing how to generate SSH keys, configure them on your server, and ensure proper security. We also discussed additional tips for securing your SSH server, including changing default ports, implementing Fail2Ban, and enabling thorough SSH logging and monitoring.

Overall, SSH key-based authentication offers a more secure and convenient way to manage remote access. It’s an effective replacement for password-based login, reducing the chance of unauthorized access and allowing for automation without compromising security.

As for key recommendations, always secure your private key with a passphrase, monitor your SSH logs regularly for suspicious activity, and consider disabling password authentication once you’re confident in your key-based setup. Implementing Fail2Ban adds an automated layer of protection, blocking IP addresses that show signs of brute-force attacks.

By following these steps, you’re well on your way to a secure and reliable SSH setup.

Thank you for joining me through this guide, and here’s to keeping your SSH connections safe and your servers secure! 🔐

_Stay safe & keep it secure!_

[**_CyberSecMaverick_**](https://medium.com/@cybersecmaverick)

[

![](https://miro.medium.com/v2/resize:fit:218/1*Vw7BOCNqvYwmSR0-KNCgTQ.png)

](https://www.buymeacoffee.com/cybersecmaverick)
