![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1734969308000iib9pn.png)

## Configure DHCP for Net105 on R3

##### R3
```
ip dhcp excluded-address 192.168.0.1 192.168.0.10
ip dhcp pool POOL_NET105
network 192.168.0.0 255.255.255.0
default-router 192.168.0.1
```

```
ip helper-address 
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
access-class DMZ_Internet_Access in
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
access-class DMZ_FROM_EXTERNAL in
```

### iii) Internal network access all network except Net105

Source : All
Destination : Net105

First question, do you use standard ACL or extended ACL?
Standard

Next question to ask yourself is use INBOUND or OUTBOUND ACLs?
Outbound
##### R3
```
ip access-list standard BLOCK_FROM_INTERNAL
deny 142.99.0.0 0.0.255.255

int g2/0
access-class DENY_FROM_INTERNAL out
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
deny ip 142.99.4.0 0.0.0.63 any

int g3/0
access-class BLOCK_DMZ_OUTBOUND out
```

### v) Enable Infrastructure Protection ACL on R1

##### R1
```

```
