![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1735486196000f40c98.png)

Whats new here?
1. GRE
2. IPSEC VPN

## Modify Topology

##### R1
```
!---Right side
int 
ip add
ipv6 add
no sh

!---Configure OSPF Ipv4
int 
ip ospf 1 area 60
ipv6 ospf network point-to-point
ipv6 ospf 1 area 60

!---Remove Null Route
no ip route 142.99.0.0 255.255.0.0 Null0

!---Configure so that R1 is no longer the default gateway
router ospf 1
no default-information originate always

ipv6 router ospf 1 
no default-information originate always
```

##### R-GW
```
!---Basic stuff
ip routing
ip unicast-routing

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

!---Configure Null route
ip route 142.99.0.0 255.255.0.0 Null0

!---Configure OSPF Ipv4 & Ipv6
router ospf 1 
router-id 

int 
ip ospf 1 area 60
ipv6 ospf network point-to-point
ipv6 ospf 1 area 60

!---Configure RGW as NEW default gateway of the network
router ospf 1
default-information originate always

ipv6 router ospf 1
default-information originate always

!---Configure eBGP
router bgp 29
bgp router-id 9.9.9.9
neighbor 100.100.99.2 remote-as 19
network 142.99.0.0 mask 255.255.0.0

bgp log-neighbor-changes
address-family ipv6
neighbor 2001:100:100:99::1 remote-as 19
neighbor 2001:100:100:99::1 activate
network 2001:142:99::/48
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

## Configuring ISP for peering purposes

As stated in the instruction, the option of not having enough interfaces to connect all members of the team is a problem. So to fix this problem, we create subinterfaces.

Let's analyze the situation. First, there a total of 5 members in one group. Each have to connect to the ISP router but the thing is a router can't have that many interfaces. Remember back inter vlan routing. With inter vlan routing, a single interface is all it takes to make it routable between vlans with the help of sub-interfaces. So using the same concept of router on the stick technique, this should solve the problem.

##### ISP
```

```