<!-- TITLE: Home Network -->
<!-- SUBTITLE: wozitech-ltd.local -->

# Main Hosts
* fw - 192.168.1.201/192.168.100.2 (DGW: 192.168.1.1)
* web proxy - 192.168.1.205 (to be removed)/192.168.100.17 (DGW: 192.168.1.1)
* mongoserver - 192.168.100.18 (DGW: 192.168.1.1)

Example network script: `/etc/sysconfig/network-scripts/ifcfg-eth0`:
```
TYPE=Ethernet
BOOTPROTO=none
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=eth0
UUID=14419f76-a2a6-486f-9e01-c405eaceb542
DEVICE=eth0
ONBOOT=yes
IPADDR=192.168.100.18
NETWORK=192.168.100.0
GATEWAY=192.168.100.2
```
The important changes are:
* `BOOTPROTO`
* `IPADDR`
* `NETWORK`
* `GATEWAY`

For DNS resolution, to `/etc/resolv.conf`:
* `nameserver 192.168.1.1`