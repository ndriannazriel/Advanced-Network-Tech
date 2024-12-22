![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733065059000drn94b.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733066082000m0jl3o.png)

## Add Net 105 (VLAN 105)
##### R3 
```
int g2/0
ip add 192.168.1.1 255.255.255.252
no shut
```

##### ESW7
```
int f0/0
ip add 192.168.1.2 255.255.255.252
no shut

vlan 105
name VLAN105

int vlan 105
ip add 192.168.0.1 255.255.255.128
no shut

int f1/1
switchport mode access
switchport access vlan 105
```

##### PC6
```
ip 192.168.0.2 255.255.255.128 192.168.0.1
```

## Configure IPv4 PAT 
Need to use acl - allow the internal network to be translated (192.168.0.0/25)


ACL1 allows traffic from the subnet **192.168.0.0/26** (IP range: 192.168.0.0–192.168.0.63) to be used for NAT.
ACL2 allows traffic from the subnet **192.168.0.64/26** (IP range: 192.168.0.64–192.168.0.127) to be used for NAT.
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1734847859000uytfk6.png)

##### R3
```
access-list 1 permit 192.168.0.0 0.0.0.63
access-list 2 permit 192.168.0.64 0.0.0.63
ip nat pool PAT-POOL1 142.99.5.1 142.99.5.1 netmask 255.255.255.252
ip nat pool PAT-POOL2 142.99.5.2 142.99.5.2 netmask 255.255.255.252
ip nat inside source list 1 pool PAT-POOL1 overload
ip nat inside source list 2 pool PAT-POOL2 overload
```

Since OSPF can't be used to advertise this network directly, I created a loopback address and instead advertise this network to the rest of the internal network making routing possible.

```
interface Loopback1
ip address 142.99.5.1 255.255.255.255

int loopback2
ip add 142.99.5.2 255.255.255.255

router ospf 1
network 142.99.5.0 0.0.0.255 area 50

int g2/0
ip nat inside
int port-channel 1
ip nat outside

router ospf 1
passive-interface g2/0
```

**Verify**
```
show ip nat translations
show ip nat statistics
```
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images173306718100009z3b3.png)

## Remove static route to ISP and configure eBGP

##### R1
```
no ip route 150.99.0.0 255.255.0.0 100.100.99.2
no ipv6 route 2001:150:99::/48 2001:100:100:99::1

router bgp 29
bgp router-id 1.1.1.1
neighbor 100.100.99.2 remote-as 19
network 142.99.0.0 mask 255.255.0.0
network 100.100.99.0 mask 255.255.255.252
```

```
bgp log-neighbor-changes
address-family ipv6
neighbor 2001:100:100:99::1 remote-as 19
neighbor 2001:100:100:99::1 activate
network 2001:142:99::/48 
network 2001:100:100:99::/127 
```

Make sure 150.99.0.0/16 Null route is configured first so that bgp knows that route exists(a route must first exist in the routing table for bgp to take place)

##### ISP
```
router bgp 19
bgp router-id 2.2.2.2 
neighbor 100.100.99.1 remote-as 29
network 100.100.99.0 mask 255.255.255.252
network 150.99.0.0 mask 255.255.0.0
```

```
bgp log-neighbor-changes
address-family ipv6
neighbor 2001:100:100:99:: remote-as 29
neighbor 2001:100:100:99:: activate
network 2001:100:100:99::/127
network 2001:150:99::/48
```

**VERIFY**
```
show ip bgp 
show ip bgp summary
show bgp ipv6 unicast 
show bgp ipv6 unicast summary
show bgp ipv6 neighbors
```
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733067387000m3uouj.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733067409000sb781j.png)
The 100.100.99.4/30 network can be ignored.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733067445000f2hdf4.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733067251000vgh91h.png)

show ip route
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733067769000dejgja.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733067808000gjx18q.png)



**Random Note**  ![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17330663210008xlkbm.png)
When you see the same network 100.100.99.0/30 listed twice in the BGP table—one entry with a Next Hop of 100.100.99.2 (learned from a BGP neighbor) and one more the empty one is locally originated(The route is also being advertised locally by the router itself)
==0.0.0.0 also means that this device is the one sending out bgp packets to the bgp neighbors==

