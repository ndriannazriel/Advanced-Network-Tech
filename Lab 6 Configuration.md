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
ip helper-address 192.168.1.1
```

## Configure ACLs

### i) Net105 only have access to DMZ and Internet

Source : Net105 (192.168.0.0/25)
Destination : DMZ and Internet

-Only blocking IPv4
-Only configure on R3

First question, do you use standard ACL or extended ACL?
Here, since the question requires you to configure it only on R3, then it means it should block the traffic at the origin and we know that Extended ACLs are placed as close as possible to the source. With an extended ACL, we can manipulate the destination ip address so that it only permits destination ip address to DMZ and Internet.

Next question to ask yourself is use INBOUND or OUTBOUND ACLs?
Inbound because if configured at OUTBOUND, the packets would reach the egress interface and then only be dropped or permitted if they violated the ACL. And of course, the less work(processing) the router needs to do, the better.

Goal : Configure Extended ACL at R3 inbound permitting traffic to DMZ and Internet only.

##### R3
```
ip access-list extended DMZ_Internet_Access
permit ip 192.168.0.0 0.0.0.127 142.99.4.0 0.0.0.63
permit ip 192.168.0.0 0.0.0.127 150.99.0.1 0.0.0.0

int g2/0
ip access-group DMZ_Internet_Access in
```

### ii) All external traffic can only access DMZ

Source : External traffic e.g 142.71.0.0/16
Destination : DMZ

First question, do you use standard ACL or extended ACL?
Use extended ACL because a standard ACL can't filter traffic based on the destination ip.

Next question to ask yourself is use INBOUND or OUTBOUND ACLs?
Inbound because same reason as before.
##### R1
```
ip access-list extended DMZ_FROM_EXTERNAL
permit ip any 142.99.4.0 0.0.0.63

int g4/0
ip access-group DMZ_FROM_EXTERNAL in
```

### iii) Internal network access all network except Net105

Source : All
Destination : Net105

First question, do you use standard ACL or extended ACL?
Standard.

Next question to ask yourself is use INBOUND or OUTBOUND ACLs?
Outbound. 
##### R3.1(Use this and send to JY)
```
ip access-list standard BLOCK_FROM_INTERNAL
deny 142.99.0.0 0.0.255.255

int g2/0
ip access-group BLOCK_FROM_INTERNAL out
```
##### R3.2
```
ip access-list standard BLOCK_FROM_INTERNAL
permit 192.168.0.0 0.0.0.127

int port-channel 1
ip access-group BLOCK_FROM_INTERNAL out
```

### iv) DMZ can't initialize access to anywhere 

Source : DMZ
Destination : Any network besides DMZ

First question, do you use standard ACL or extended ACL?
Extended

Next question to ask yourself is use INBOUND or OUTBOUND ACLs?
Outbound

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

Note : I don't understand the permit ip any any ace YET.
### v) Enable Infrastructure Protection ACL on R1

What is Infrastructure Protection ACL?
The singular purpose of the infrastructure protection ACL is to restrict on a granular level what protocols and sources can access critical infrastructure equipment.

**Edge filtering via infrastructure ACLs**
ACLs can be applied to the edge of the network. In the case of a service provider (SP), this is the edge of the AS. This ACL explicitly filters traffic destined for infrastructure address space. Deployment of edge infrastructure ACLs requires that you clearly define your infrastructure space and the required/authorized protocols that access this space. The ACL is applied at ingress to your network on all externally facing connections, such as peering connections, customer connections, and so forth.

Define infrastructure

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1735313675000xip4e3.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1735313718000j8bywp.png)

##### R1
```
ip access-list extended DMZ_FROM_EXTERNAL
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
ipv6 access-list iacl

!--- Deny your space as source from entering your AS. !--- Deploy only at the AS edge.
deny ipv6 YOUR_CIDR_BLOCK_IP any

!--- Permit multiprotocol BGP.
permit tcp host bgp_peer_ipv6 host router_ipv6 eq bgp
permit tcp host bgp_peer_ipv6 eq bgp host router_ipv6

!--- Deny access to internal infrastructure addresses.
deny ipv6 any INTERNAL_INFRASTRUCTURE_ADDRESSES_IPV6

!--- Permit transit traffic.
permit ipv6 any any
```

