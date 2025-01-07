![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1735486196000f40c98.png)

Whats new here?
1. GRE
2. IPSEC VPN

## Modify Topology

##### R1
```
!---Right side
int g4/0
ip add 142.99.3.29 255.255.255.252
ipv6 add 2001:142:99:10::/127
no sh

!---Configure OSPF Ipv4
int g4/0
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
int g0/1
ip add 142.99.3.30 255.255.255.252
ipv6 add 2001:142:99:10::1/127
no sh

!---Right side
int g0/2
ip add 100.100.99.1 255.255.255.252
ipv6 add 2001:100:100:99::/127
no sh

!---Configure Null route
ip route 142.99.0.0 255.255.0.0 Null0
ipv6 route 2001:142:99::/48 Null0

!---Configure OSPF Ipv4 & Ipv6
router ospf 1 
router-id 10.10.10.10

int g0/1
ip ospf 1 area 60
ipv6 ospf 1 area 60

!---Configure RGW as NEW default gateway of the network
router ospf 1
default-information originate always

ipv6 router ospf 1
default-information originate always

!---Configure eBGP
router bgp 29
bgp router-id 9.9.9.9
neighbor 100.100.99.2 remote-as 184
network 142.99.0.0 mask 255.255.0.0
network 100.100.99.0 mask 255.255.255.252

bgp log-neighbor-changes
neighbor 2001:100:100:99::1 remote-as 184
address-family ipv6
neighbor 2001:100:100:99::1 activate
network 2001:142:99::/48
network 2001:100:100:99::/127
```

-Buang point to point 
-make sure no passive interface

##### R3
```
S    192.168.0.0/16 [1/0] via 172.16.0.2
S    192.168.99.0/24 [1/0] via 192.168.1.2
```
## Shift R1 ACL to R-GW

##### R1
```
no ip access-list extended INFRASTRUCTURE_ACL_R1
no ipv6 access-list INFRASTRUCTURE_ACL_R1_IPV6
```

##### R-GW
```
ip access-list extended INFRASTRUCTURE_ACL_RGW
permit icmp any any echo-reply
deny ip any host 142.99.4.1
permit ip any 142.99.4.0 0.0.0.63
permit udp any any eq 500
permit esp any any

permit gre host 142.70.4.5 host 142.99.3.22 

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

ipv6 access-list INFRASTRUCTURE_ACL_RGW_IPV6
permit icmp any any echo-reply
deny ipv6 any host 2001:142:99:104::1
permit ipv6 any 2001:142:99:104::/64
deny ipv6 2001:142:99::/48 any
permit tcp host 2001:100:100:99::2 host 2001:100:100:99::1 eq bgp
permit tcp host 2001:100:100:99::2 eq bgp host 2001:100:100:99::1
deny ipv6 any 2001:142:99::/48
```

## Configuring ISP for peering purposes

As stated in the instruction, the option of not having enough interfaces to connect all members of the team is a problem. So to fix this problem, we create subinterfaces.

Let's analyze the situation. First, there a total of 5 members in one group. Each have to connect to the ISP router but the thing is a router can't have that many interfaces. Remember back inter vlan routing. With inter vlan routing, a single interface is all it takes to make it routable between vlans with the help of sub-interfaces. So using the same concept of router on the stick technique, this should solve the problem.

##### Switch
```
!---Create VLANs
conf t
vlan 10
vlan 20
vlan 30
vlan 40
...

!---Configure Trunk
int f0/1
sw mode trunk
sw trunk allowed vlan all

!---Assign access ports
int g0/5
sw mode access 
sw acc vlan 10
no sh

```
##### ISP
```
!---Configure Subinterfaces
int g0/0.10
encapsulation dot1q 10
ip add 100.100.99.2 255.255.255.252
ipv6 add 2001:100:100:99::1/127
no sh

!---Configure BGP
router bgp 184
neighbor 100.100.99.1 remote-as 29

bgp log-neighbor-changes
address-family ipv6
neighbor 2001:100:100:99:: remote-as 29
neighbor 2001:100:100:99:: activate
```

## Configure GRE interconnecting Net105

Since we are going from Net105 to another Net105 of another internal network, that will be our source.

##### RGW
```
ip access-list extended INFRASTRUCTURE_ACL_RGW
permit udp any any eq 500
permit esp any any
```
ACL is needed to allow only specific hosts into my Net105
##### R3
```
ip access-list extended INTERNAL_BLOCK
permit ip 192.168.0.0 0.0.0.127 any

int tunnel 1
ip add 172.16.0.1 255.255.255.252
tunnel source 142.99.3.22
tunnel dest 142.70.3.25
```

##### Verify
```
sh int tunnel 1
```

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736211931000ap2s2l.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736226989000jpg4m3.png)

## Configure IPSEC VPN
(VLAN103 to DMZ)
(DMZ to VLAN103)
##### RGW
```
!---Configure ACL permitting VLAN103 to DMZ
ip access-list extended RGW->DMZ
permit ip 142.99.0.0 0.0.1.255 142.70.4.0 0.0.0.63 
permit ip 142.99.4.0 0.0.0.63 142.70.0.0 0.0.1.255 
permit ip 142.99.2.128 0.0.0.127 142.70.4.0 0.0.0.63
permit ip 142.99.4.0 0.0.0.63 142.70.2.128 0.0.0.127

crypto isakmp policy 1
encryption aes
hash sha256
authentication pre-share
group 2

crypto isakmp key 0 mypass address 100.100.70.1
crypto ipsec transform-set mytset esp-aes esp-sha-hmac

crypto map CRYPTOMAP 10 ipsec-isakmp
set peer 100.100.70.1
set transform-set mytset
match address RGW->DMZ

!--_Configure Ipv6
ipv6 access-list RGW->DMZ_IPv6
no permit ipv6 2001:142:99:103::/64 2001:142:70:104::/64
no permit ipv6 2001:142:99:104::/64 2001:142:70:103::/64
no permit ipv6 2001:142:99:101::/64 2001:142:70:104::/64
no permit ipv6 2001:142:99:104::/64 2001:142:70:101::/64

permit ipv6 2001:142:99::/48 2001:142:70::/48
permit ipv6 2001:142:70::/48 2001:142:99::/48

crypto isakmp policy 1
encryption aes
hash sha256
authentication pre-share
group 2
exi

crypto isakmp key 0 mypass address ipv6 2001:100:100:70::1/127
crypto ipsec transform-set mytset esp-aes esp-sha-hmac

crypto map ipv6 CRYPTOMAP_IPV6-2 10 ipsec-isakmp
set peer 2001:100:100:70::1
set transform-set mytset
match address RGW->DMZ_IPv6

interface g0/2
ipv6 crypto map CRYPTOMAP_IPV6-2
cryto map CRYPTOMAP
ipv6 enable

```

##### Verify
```
sh crypto ipsec sa
sh crypto isakmp sa
```
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736218837000rhu6u3.png)

## RECONFIGURE NET 105

##### R3
```
no access-list 1 permit 192.168.0.0 0.0.0.63
no access-list 2 permit 192.168.0.64 0.0.0.63

access-list 1 permit 192.168.99.0 0.0.0.63
access-list 2 permit 192.168.99.64 0.0.0.63
ip nat pool PAT-POOL1 142.99.5.1 142.99.5.1 netmask 255.255.255.252
ip nat pool PAT-POOL2 142.99.5.2 142.99.5.2 netmask 255.255.255.252
ip nat inside source list 1 pool PAT-POOL1 overload
ip nat inside source list 2 pool PAT-POOL2 overload

no ip dhcp excluded-address 192.168.0.1 192.168.0.10
no ip dhcp pool POOL_NET105
no network 192.168.0.0 255.255.255.0
no default-router 192.168.0.1

ip dhcp excluded-address 192.168.99.1 192.168.99.10
ip dhcp pool POOL_NET105
network 192.168.99.0 255.255.255.0
default-router 192.168.99.1

!---RECONFIGURE ACLS
no ip access-list extended DMZ_Internet_Access

ip access-list extended DMZ_Internet_Access
permit ip 192.168.99.0 0.0.0.127 142.99.4.0 0.0.0.63
deny ip 192.168.99.0 0.0.0.127 142.99.0.0 0.0.255.255
permit ip 192.168.99.0 0.0.0.127 any
permit udp any any eq 67
permit udp any any eq 68
deny tcp any any
deny udp any any

no ip access-list extended INTERNAL_BLOCK

ip access-list extended INTERNAL_BLOCK
permit ip 192.168.99.0 0.0.0.127 any
permit icmp any any echo-reply
```

!---Buang this for testing because of looping
S    192.168.0.0/16 [1/0] via 192.168.1.2
##### ESW7
```
int vlan 105
no ip add
ip add 192.168.99.1 255.255.255.128
no shut
```

##### PC6
```
ip 192.168.99.2 255.255.255.128 192.168.99.1
```

## Reconfigure VLAN103
