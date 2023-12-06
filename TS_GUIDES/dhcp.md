# Check if a DHCP server existing in my network using bash

DHCP uses UDP port 67 on the server side and UDP port 68 on the client side. DHCP also has two versions: DHCPv4 and DHCPv6 to support IPv4 and IPv6, respectively. These two versions, much like the two versions of IP, are very different and are therefore considered separate protocols and use separate ports.

## Links

*   [one of many MAC address lookup tools on the web](http://www.wireshark.org/tools/oui-lookup.html)

## nmap does this easily:

```sudo nmap --script broadcast-dhcp-discover -e eth0```

<pre>
Starting Nmap 6.40 ( http://nmap.org ) at 2016-08-16 09:25 UTC
Pre-scan script results:
| broadcast-dhcp-discover: 
|   IP Offered: 192.168.0.67
|   DHCP Message Type: DHCPOFFER
|   Server Identifier: 192.168.0.1
|   IP Address Lease Time: 0 days, 0:05:00
|   Subnet Mask: 255.255.255.0
|   Router: 192.168.0.1
|   Domain Name Server: 8.8.8.8
|   Domain Name: maas
|   Broadcast Address: 192.168.0.255
|_  NTP Servers: 91.189.91.157, 91.189.89.199, 91.189.94.4, 91.189.89.198
WARNING: No targets were specified, so 0 hosts scanned.
Nmap done: 0 IP addresses (0 hosts up) scanned in 0.27 seconds
</pre>

Note: there is a similar script for dhcpv6

```bash
sudo nmap --script broadcast-dhcp6-discover -e eth0
```

If you have `tcpdump` available to you, invoking the program as root with the following parameters might assist you in finding the server:

<pre>
SYNOPSIS
       dhcpdump [-h regular-expression] -i interface

DESCRIPTION
       This command parses the output of tcpdump to display the dhcp-packets for easier checking and debugging.

USAGE
       dhcpdump -i /dev/fxp0

       If you want to filter a specific Client Hardware Address (CHADDR), then you can specifiy it as a regular expressions:

       dhcpdump -i /dev/fxp0 -h ^00:c0:4f

       This will display only the packets with Client Hardware Addresses which start with 00:c0:4f.
</pre>

```tcpdump -i \[interface id\] -nev udp port 68```

Unfortunately, due to my network's layout, I can't get a full DHCP handshake captured right away. However, I do see a DHCP Request from my iPad:

```bash
22:16:44.767371 30:10:e4:8f:02:14 > ff:ff:ff:ff:ff:ff, ethertype IPv4 (0x0800), length 342: (tos 0x0, ttl 255, id 15652, offset 0, flags \[none\], proto UDP (17), length 328)
    0.0.0.0.68 > 255.255.255.255.67: BOOTP/DHCP, Request from 30:10:e4:8f:02:14, length 300, xid 0x42448eb6, Flags \[none\]
      Client-Ethernet-Address 30:10:e4:8f:02:14
      Vendor-rfc1048 Extensions
        Magic Cookie 0x63825363
        DHCP-Message Option 53, length 1: Request
        Parameter-Request Option 55, length 6: 
          Subnet-Mask, Default\-Gateway, Domain-Name-Server, Domain-Name
          Option 119, Option 252
        MSZ Option 57, length 2: 1500
        Client-ID Option 61, length 7: ether 30:10:e4:8f:02:14
        Requested-IP Option 50, length 4: 192.168.2.222
        Lease-Time Option 51, length 4: 7776000
        Hostname Option 12, length 15: "NevinWiamssiPad"

```
After letting \`tcpdump' run overnight, I did eventually see this ACK:

```bash

07:46:40.049423 a8:39:44:96:fa:b8 > 68:a8:6d:58:5b:f3, ethertype IPv4 (0x0800), length 320: (tos 0x0, ttl 64, id 0, offset 0, flags \[none\], proto UDP (17), length 306)
    192.168.2.1.67 > 192.168.2.22.68: BOOTP/DHCP, Reply, length 278, xid 0x5e7944f, Flags \[none\]
      Client-IP 192.168.2.22
      Your-IP 192.168.2.22
      Client-Ethernet-Address 68:a8:6d:58:5b:f3
      Vendor-rfc1048 Extensions
        Magic Cookie 0x63825363
        DHCP-Message Option 53, length 1: ACK
        Server-ID Option 54, length 4: 192.168.2.1
        Lease-Time Option 51, length 4: 86400
        Subnet-Mask Option 1, length 4: 255.255.255.0
        Default\-Gateway Option 3, length 4: 192.168.2.1
        Domain-Name-Server Option 6, length 8: 192.168.2.1,142.166.166.166
```
If when running that `tcpdump` command, and you see a BOOTP/DHCP Offer or Ack(Nack), that will be from a DHCP server, and the server's MAC address will be right after the timestamp on the first line.

So the (valid) DHCP server here has MAC address a8:39:44:96:fa:b8\`.

Using [one of many MAC address lookup tools on the web](http://www.wireshark.org/tools/oui-lookup.html) I see this MAC belongs to `A8:39:44 Actiontec Electronics, Inc` which is my router.

In order to catch rogue DHCP server packets as they happen, I would have to leave this `tcpdump` process running in terminal window:

```tcpdump -i en0 -nev udp src port 67 and not ether host a8:39:44:96:fa:b8```

This will only show me DHCP server responses from hosts other than my valid DHCP server, as long as the process is running in its own window.

The following command will run in the background until 100 packets are captured, appending any rogue DHCP server messages to the file `/tmp/rogue`. Again, the MAC address of your valid DHCP server has to be used in the appropriate place, as well as the interface descriptor on your system.

```tcpdump -U -i en0 -c 100 -nev udp src port 67 and not ether host a8:39:44:96:fa:b8 >> /tmp/rogue 2>&1 &```
