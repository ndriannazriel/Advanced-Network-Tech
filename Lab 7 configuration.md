![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1735486196000f40c98.png)

Whats new here?
1. GRE
2. IPSEC VPN

## Modify Topology

##### R1
```
int 
ip add
ipv6 add
no sh

!---Configure OSPF Ipv4
int 
ip ospf 1 area 60
ipv6 ospf 1 area 60
```

##### R-GW
```
!---Left side
int 
ip add
ipv6 add
no sh

!---Right side
int 
ip add 100.100.99.1 255.255.255.252
ipv6 add 2001:100:100:99::/127
no sh

!---Configure OSPF Ipv4
router ospf 1 
router-id 

int 
ip ospf 1 area 60
ipv6 ospf 1 area 60

!---Configure eBGP
router bgp 29

```

##### ISP
```

```
## Shift R1 ACL to R-GW

##### R-GW
```
ip access-list extended INFRASTRUCTURE_ACL_RGW
permit icmp any any echo-reply
deny ip any host 142.99.4.1
permit ip any 142.99.4.0 0.0.0.63
deny ip host 0.0.0.0 any
deny ip 127.0.0.0 0.255.255.255 any
deny ip 192.0.2.0 0.0.0.255 any
deny ip 224.0.0.0 31.255.255.255 any
deny ip 10.0.0.0 0.255.255.255 any
deny ip 172.16.0.0 0.15.255.255 any
deny ip 192.168.0.0 0.0.255.255 any
deny ip 142.99.0.0 0.0.255.255 any
permit tcp host 100.100.99.2 host 100.100.99.1 eq bgp
permit tcp host 100.100.99.2 eq bgp host 100.100.99.1
deny ip any 142.99.0.0 0.0.255.255

```