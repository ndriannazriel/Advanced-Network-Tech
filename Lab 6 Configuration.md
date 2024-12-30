![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1734969308000iib9pn.png)

## Configure DHCP for Net105 on R3

##### R3
```
ip dhcp excluded-address 192.168.0.1 192.168.0.10
ip dhcp pool POOL_NET105
network 192.168.0.0 255.255.255.0
default-router 192.168.0.1
```

##### ESW7
```
service dhcp
ip helper-address 192.168.1.1
```

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1735483155000xhvkq2.png)

## Configure ACLs

### i) Net105 only have access to DMZ and Internet

Source : Net105 (192.168.0.0/25)
Destination : DMZ and Internet

-Only blocking IPv4
-Ipv6 acl not needed
-No need to worry about ospf packets

Goal : Configure Extended ACL at R3 inbound permitting traffic to DMZ and Internet only.

##### R3
```
ip access-list extended DMZ_Internet_Access
permit ip 192.168.0.0 0.0.0.127 142.99.4.0 0.0.0.63
permit ip 192.168.0.0 0.0.0.127 any

int g2/0
ip access-group DMZ_Internet_Access in
```

### ii) All external traffic can only access DMZ

Source : External traffic e.g 142.71.0.0/16
Destination : DMZ

##### R1
```
ip access-list extended INFRASTRUCTURE_ACL_R1
permit ip any 142.99.4.0 0.0.0.63

int g4/0
ip access-group INFRASTRUCTURE_ACL_R1 in
```

```
ipv6 access-list INFRASTRUCTURE_ACL_R1_IPV6
permit ipv6 any 2001:142:99:104::/64

int g4/0
ipv6 traffic-filter INFRASTRUCTURE_ACL_R1_IPV6 in
```
### iii) Internal network access all network except Net105

Source : All
Destination : Net105

-IPv6 not needed because net105 is a ipv4 network.
-No need to worry about ospf packets

##### R3.1
```
ip access-list standard INTERNAL_BLOCK
deny 142.99.0.0 0.0.255.255

int g2/0
ip access-group INTERNAL_BLOCK out
```
I can't place this here because if I were to do that, then PC from Net105 won't get the ping back because on the way back, it would need to go through this check and if it originates from for example 142.99.4.2(Net104/DMZ), then the packet would be dropped resulting in the ping not coming back.
##### R3.2(Use this)
```
ip access-list extended INTERNAL_BLOCK
permit ip 192.168.0.0 0.0.0.127 any
permit icmp any any echo-reply

int g2/0
ip access-group INTERNAL_BLOCK out
```

### iv) DMZ can't initialize access to anywhere 

Source : DMZ
Destination : Any network besides DMZ

-Consider both Ipv4 and Ipv6
-OSPF packets need to be taken into account

Goal : DMZ can't ping anywhere outside itself but outside can ping it.

##### R3
```
ip access-list extended BLOCK_DMZ_OUTBOUND
permit icmp any any echo-reply 
permit ospf any any
deny ip 142.99.4.0 0.0.0.63 any
permit ip any any

int g3/0
ip access-group BLOCK_DMZ_OUTBOUND in
```

```
ipv6 access-list BLOCK_DMZ_OUTBOUND_IPV6
permit icmp any any echo-reply 
permit 89 FE80::/10 any
deny ipv6 2001:142:99:104::/64 any
permit ipv6 any any

int g3/0
ipv6 traffic-filter BLOCK_DMZ_OUTBOUND_IPV6 in
```

### v) Enable Infrastructure Protection ACL on R1

What is Infrastructure Protection ACL?
The singular purpose of the infrastructure protection ACL is to restrict on a granular level what protocols and sources can access critical infrastructure equipment.

**Edge filtering via infrastructure ACLs**
ACLs can be applied to the edge of the network. In the case of a service provider (SP), this is the edge of the AS. This ACL explicitly filters traffic destined for infrastructure address space. Deployment of edge infrastructure ACLs requires that you clearly define your infrastructure space and the required/authorized protocols that access this space. The ACL is applied at ingress to your network on all externally facing connections, such as peering connections, customer connections, and so forth.

Define infrastructure at the edge which in this case is our R1.
##### R1
```
ip access-list extended INFRASTRUCTURE_ACL_R1
!--- Deny special-use address sources. 
deny ip host 0.0.0.0 any
deny ip 127.0.0.0 0.255.255.255 any
deny ip 192.0.2.0 0.0.0.255 any
deny ip 224.0.0.0 31.255.255.255 any

!--- Filter RFC 1918 space.
deny ip 10.0.0.0 0.255.255.255 any
deny ip 172.16.0.0 0.15.255.255 any
deny ip 192.168.0.0 0.0.255.255 any

!--- Deny your space as source from entering your AS. !--- Deploy only at the AS edge
deny ip 142.99.0.0 0.0.255.255 any

!--- Permit BGP.
permit tcp host 100.100.99.2 host 100.100.99.1 eq bgp
permit tcp host 100.100.99.2 eq bgp host 100.100.99.1

!--- Deny access to internal infrastructure addresses.
deny ip any 142.99.0.0 0.0.255.255

!--- Permit transit traffic.
permit ip any any

```

##### R1(IPv6)
```
!--- Configure the access-list.
ipv6 access-list INFRASTRUCTURE_ACL_R1_IPV6

!--- Deny your space as source from entering your AS. !--- Deploy only at the AS edge.
deny ipv6 2001:142:99::/48 any

!--- Permit multiprotocol BGP.
permit tcp host 2001:100:100:99::2 host 2001:100:100:99::1 eq bgp
permit tcp host 2001:100:100:99::2 eq bgp host 2001:100:100:99::1

!--- Deny access to internal infrastructure addresses.
deny ipv6 any 2001:142:99::/48

!--- Permit transit traffic.
permit ipv6 any any
```

## Verify ACLs Configured

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1735486617000z4i5h3.png)

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1735486641000tiy28p.png)

**![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17354866950005g0w49.png)

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1735486735000ray4mm.png)

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17354867650000oofkv.png)
