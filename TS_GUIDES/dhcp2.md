# How to troubleshoot DHCP communication problems on your network

*   How to troubleshoot DHCP communication problems on your network
*   Start with the basics
*   Scan for the DHCP server
*   Sniff for DHCP traffic
*   Sniffing network traffic with tcpdump
*   Sniffing network traffic with Wireshark
*   Use an Nmap script
*   Start with the simple stuff
*   Damon Garn
*   Try Red Hat Enterprise Linux
*   Download it at no charge from the Red Hat Developer program.
*   Related Content

##### Links

*   [Static and dynamic IP address configurations: DHCP deployment](https://www.redhat.com/sysadmin/static-dynamic-ip-2)
*   [how to use Nmap](https://www.redhat.com/sysadmin/quick-nmap-inventory)
*   [Bash shell scripting cheat sheet](https://developers.redhat.com/cheat-sheets/bash-shell-cheat-sheet?intcmp=701f20000012ngPAAQ)
*   [full access to Red Hat's curriculum](https://www.redhat.com/en/services/training/learning-subscription?intcmp=701f20000012ngPAAQ)
*   [Nmap Scripting Engine](https://nmap.org/nsedoc/)
*   [broadcast-dhcp-discover script](https://nmap.org/nsedoc/scripts/broadcast-dhcp-discover.html)
*   [unicast version of the script](https://nmap.org/nsedoc/scripts/dhcp-discover.html)
*   [My go-to Linux network troubleshooting commands](https://www.redhat.com/sysadmin/linux-network-troubleshooting-commands)
*   [Tyler Carrigan (Editorial Team, Red Hat)](https://www.redhat.com/sysadmin/users/tcarriga)
*   [Networking for sysadmins: Recognizing router problems](https://www.redhat.com/sysadmin/recognizing-router-problems)
*   [Joachim Haller (Accelerator, Sudoer)](https://www.redhat.com/sysadmin/users/joachim-haller)
*   [Troubleshooting hardware problems in Linux](https://www.redhat.com/sysadmin/troubleshooting-hardware-problems-linux)
*   [Daniel Oh (Red Hat)](https://www.redhat.com/sysadmin/users/daniel-oh)
*   [Troubleshooting](https://www.redhat.com/sysadmin/topics/troubleshooting)
*   [More about me](https://www.redhat.com/sysadmin/users/dgarn)
*   [Test TCP ports with Python and Scapy](https://www.redhat.com/sysadmin/test-tcp-python-scapy)
*   [Jose Vicente Nunez (Sudoer)](https://www.redhat.com/sysadmin/users/jose-vicente-nunez)
*   [Stop using Telnet to test ports](https://www.redhat.com/sysadmin/stop-using-telnet-test-port)
*   [Monitor and troubleshoot applications with Glances and InfluxDB](https://www.redhat.com/sysadmin/monitoring-glances-influxdb)

##### Marks

Use Nmap, tcpdump, and Wireshark to discover why your enterprise switch and clients aren't communicating IP configurations correctly.

Posted: January 13, 2022 | %t min read | **by** [Damon Garn](https://www.redhat.com/sysadmin/users/dgarn)

Image

Imagine you have a repurposed enterprise switch with a Dynamic Host Configuration Protocol (DHCP) service that you need to troubleshoot. There is little information available about the switch's configuration or previous deployments. The device is reported to be functional and should lease Internet Protocol (IP) address configurations to clients. However, the attached clients are not receiving IP configurations from the switch.

There are many ways to troubleshoot this, including the ones I'll explore in this article: network scanning and packet sniffing tools. An advantage of scanning and sniffing tools is that they display exactly what is happening on the network. Not what the network should do, but what it _is_ doing.

DHCP uses a four-step process to enable clients to lease an IP address configuration:

*   **DHCP DISCOVER:** Client broadcasts that it needs to lease an IP configuration from a DHCP server
*   **DHCP OFFER:** Server broadcasts to offer an IP configuration
*   **DHCP REQUEST:** Client broadcasts to formally ask for the offered IP configuration
*   **DHCP ACKNOWLEDGE (ACK):** Server broadcasts confirming the leased IP configuration

These broadcasts use ports 67/udp and 68/udp. If you're not familiar with how DHCP works, see [Static and dynamic IP address configurations: DHCP deployment](https://www.redhat.com/sysadmin/static-dynamic-ip-2).

Start with the basics
---------------------

First, check all the basics:

*   Does physical connectivity exist with functional network media?
*   Have you restarted the DHCP service?
*   Is a DHCP scope configured?
*   Do the server and client logs display any clues as to why the leases fail? (If so, try to fix those issues before moving on.)

Once you've confirmed the above (including that there aren't any clues in the logs), follow the steps below to use network scanners and packet sniffers to display valuable troubleshooting information.

Scan for the DHCP server
------------------------

One logical step is to confirm that the DHCP service device has a network presence. An Nmap scan verifies its identity on the network. Many articles describe [how to use Nmap](https://www.redhat.com/sysadmin/quick-nmap-inventory). Begin with a basic ping sweep that identifies all hosts on the segment. Run the scan from a connected device with a static IP address configuration.

For a basic ping sweep to identify available hosts on the 192.168.1.0/24 network, type:

```$ nmap -sn 192.168.1.1\-255```

Good news: The network device hosting the DHCP service was detected. If it appears to have a legitimate IP address configuration, then it should be able to lease addresses. Refer to the organization's network diagram to ensure Nmap detects the nodes you expect to see.

If the results indicate it did not find the DHCP server on the network, check its static IP address configuration, ensure network interface controllers (NICs) are enabled, and so on.

Sniff for DHCP traffic
----------------------

[![IT Automation ebook](https://www.redhat.com/sysadmin/sites/default/files/styles/image_cta/public/2021-07/SysAdminGuidetoITAutomation-e-book-medium.jpg?itok=8_5VlQhF)](https://www.redhat.com/en/engage/system-administrator-guide-s-202107300146?intcmp=701f20000012ngPAAQ)

[Download now](https://www.redhat.com/en/engage/system-administrator-guide-s-202107300146?intcmp=701f20000012ngPAAQ)

You might be asking: What DHCP traffic is being exchanged? The clients send DHCP DISCOVER queries, and the server provides DHCP OFFER responses. Use a protocol analyzer (or packet sniffer) to intercept network traffic and ensure the communication occurs as expected. The two primary examples of sniffers are [tcpdump](https://www.redhat.com/sysadmin/troubleshoot-tcpdump) and [Wireshark](https://www.redhat.com/sysadmin/introduction-wireshark). Which you select is a matter of preference, familiarity, and what is installed on the system.

### Sniffing network traffic with tcpdump

The tcpdump utility is fairly common on many Linux admin computers. If not, use `dnf` to install it:

```$ sudo dnf install tcpdump```

The network interface you want to monitor must be in promiscuous mode. You set this using the `ip` command. For example, to configure `eth0`:

```$ sudo ip link set eth0 promisc on```

You can configure tcpdump to grab specific network packet types, and on a busy network, it's a good idea to focus on just the protocol needed. This example gathers information on `eth0` for UDP ports 67 and 68 (DHCP) in verbose mode. tcpdump writes the output to a file named `dhcp.pcap`:

```$ tcpdump -i eth0 udp port 67 and port 68 -vv -w dhcp.pcap```

View the file's contents using tcpdump (rather than a standard text editor!). The read option is `-r`, followed by the filename:

```$ tcpdump -r dhcp.pcap```

tcpdump can read the file, but it may be more visually appealing and easier to filter the output by opening the file in Wireshark. Launch Wireshark, go to the **File** menu, select **Open**, and select the output .pcap file (the exact process may vary by version).

First, establish whether the clients sent DHCP DISCOVER queries (remember, the client initiates the lease-generation process). If so, then the clients are likely functioning properly. If DHCP DISCOVER queries are getting sent, check for DHCP OFFER responses from the server. Do these responses exist and are they offering the correct information?

_**\[ Download the [Bash shell scripting cheat sheet](https://developers.redhat.com/cheat-sheets/bash-shell-cheat-sheet?intcmp=701f20000012ngPAAQ). \]**_

### Sniffing network traffic with Wireshark

Wireshark is another excellent traffic-sniffing tool, and the process is basically the same as with tcpdump. It's best to run Wireshark from the DHCP server in this case because the client computers aren't configured. Another option is to configure a central troubleshooting workstation with a static IP address to capture all traffic. Wireshark has excellent flexibility, and you can also run it from non-Linux systems.

Set the capture filter for the appropriate network interface (there isn't a capture filter for DHCP), and begin the capture process. Again, confirming the DHCP DISCOVER and DHCP OFFER communications is key. Next, start a DHCP client workstation to initiate the lease-generation process. Stop the capture after about one minute, at most. The DHCP query occurs very early in the operating system's startup procedure.

Save the capture file, if desired. In the **Display filter** box, type **dhcp** and select **Enter** to filter the packets. Wireshark now displays the DHCP packets picked up from the network. The client packets are DHCP DISCOVER communications, and the server should reply with a DHCP OFFER. If both sets of packets are displayed, the devices are communicating correctly. If either set is missing, then the related device has the issue. DHCP REQUEST and ACK exchanges are also displayed if the lease-generation process is successful.

**_\[ Get access to a free trial of [full access to Red Hat's curriculum](https://www.redhat.com/en/services/training/learning-subscription?intcmp=701f20000012ngPAAQ). \]_**

Use an Nmap script
------------------

While Nmap can conduct general scans and protocol analyzers can display information based on packet captures, what about a more complete solution? Browse the Nmap site for the [Nmap Scripting Engine](https://nmap.org/nsedoc/) (NSE). It contains more than 600 scripts with preconfigured settings for various Nmap scans. Authors create and share these scripts. In this scenario, the [`broadcast-dhcp-discover` script](https://nmap.org/nsedoc/scripts/broadcast-dhcp-discover.html) helps with DHCP troubleshooting.

The script generates a DHCP DISCOVER message, the same as a standard DHCP client, and logs the DHCP OFFER responses from any DHCP servers. Not only can this information prove that the DHCP server is answering requests from clients, but it also detects rogue DHCP servers (rogue DHCP servers may be planted in the network by malicious actors, or they might be misconfigured servers or unknown servers deployed by administrators). The script should detect any DHCP servers because the DISCOVER message is broadcast to the 255.255.255.255 address.

The basic syntax for Nmap scripts, with the DHCP broadcast script as an example, is `nmap --script broadcast-dhcp-discover`. A more specific DHCP syntax is:

```$ nmap -sU -p67 \--script broadcast-dhcp-discover```

The [unicast version of the script](https://nmap.org/nsedoc/scripts/dhcp-discover.html), `dhcp-discover`, sends a direct query to the DHCP server. Notice the query is addressed to the DHCP server:

```nmap -sU -p67 \--script dhcp-discover 10.10.10.1$```

This query generates a response from the server that provides basic configuration information and suggests that the service is communicating. The response to this message may vary by DHCP service type, but any response should indicate functionality. The DHCP server is likely misconfigured, not running, blocked, or otherwise unavailable if no response is detected. Regardless, it identifies the server as the problem in this scenario.

**Note:** There are corresponding scripts for IPv6 network troubleshooting, as well.

Start with the simple stuff
---------------------------

Narrowing the scope of the problem to specific network communications by using packet sniffers gives the most granular view of what's happening on the network. Confirming the presence of the DHCP server on the segment with Nmap is a good way of knowing what you think is on the network is actually on the network.

I want to point out a general note on my troubleshooting methodology in this article. Notice that I began with the simple stuff: physical connectivity, service status, service configuration, logs, and such. Begin with the simple things and move toward the more complicated. Just because a network is complex does not mean the problem is complex.

Image

[My go-to Linux network troubleshooting commands](https://www.redhat.com/sysadmin/linux-network-troubleshooting-commands)

Every sysadmin needs a good troubleshooting strategy, and you can't fix a problem if you cannot identify it. These are my favorite commands to quickly filter through the possibilities of a given problem.

Posted: November 29, 2019

Author: [Tyler Carrigan (Editorial Team, Red Hat)](https://www.redhat.com/sysadmin/users/tcarriga)

Image

[Networking for sysadmins: Recognizing router problems](https://www.redhat.com/sysadmin/recognizing-router-problems)

Multiple network components, including those of the ISP, can be the cause of a network incident. Here's what to consider to determine if your router is to blame.

Posted: January 20, 2020

Author: [Joachim Haller (Accelerator, Sudoer)](https://www.redhat.com/sysadmin/users/joachim-haller)

Image

[Troubleshooting hardware problems in Linux](https://www.redhat.com/sysadmin/troubleshooting-hardware-problems-linux)

Learn what's causing your Linux hardware to malfunction so you can get it back up and running quickly.

Posted: May 13, 2019

Author: [Daniel Oh (Red Hat)](https://www.redhat.com/sysadmin/users/daniel-oh)

**Topics:**   [Troubleshooting](https://www.redhat.com/sysadmin/topics/troubleshooting)   [Networking](https://www.redhat.com/sysadmin/topics/networking)  

Damon Garn
----------

Damon Garn owns Cogspinner Coaction, LLC, a technical writing, editing, and IT project company based in Colorado Springs, CO. Damon authored many CompTIA Official Instructor and Student Guides (Linux+, Cloud+, Cloud Essentials+, Server+) and developed a broad library of interactive, scored labs. He regularly contributes to Enable Sysadmin, SearchNetworking, and CompTIA article repositories. Damon has 20 years of experience as a technical trainer covering Linux, Windows Server, and security content. He is a former sysadmin for US Figure Skating. He lives in Colorado Springs with his family and is a writer, musician, and amateur genealogist. [More about me](https://www.redhat.com/sysadmin/users/dgarn)

Try Red Hat Enterprise Linux
----------------------------

#### Download it at no charge from the Red Hat Developer program.

Related Content
---------------

Image

![A blue cable plugged into a green Raspberry Pi](https://www.redhat.com/sysadmin/sites/default/files/2022-10/blue-network-cable-raspberry-pi.jpg)

[Test TCP ports with Python and Scapy](https://www.redhat.com/sysadmin/test-tcp-python-scapy)

Get greater control over TCP port checking with a DIY, customizable approach using Python and Scapy.

Posted: April 19, 2023

Author: [Jose Vicente Nunez (Sudoer)](https://www.redhat.com/sysadmin/users/jose-vicente-nunez)

Image

[Stop using Telnet to test ports](https://www.redhat.com/sysadmin/stop-using-telnet-test-port)

Make life simpler by automating network checks with tools like Expect, Bash, Netcat, and Nmap instead.

Posted: April 18, 2023

Author: [Jose Vicente Nunez (Sudoer)](https://www.redhat.com/sysadmin/users/jose-vicente-nunez)
