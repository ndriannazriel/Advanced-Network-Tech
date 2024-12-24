### Inbound ACLs VS Outbound ACLs

An inbound ACL filters packets before they are routed to the outbound interface. An inbound ACL is efficient because it saves the overhead of routing lookups if the packet is discarded. If the packet is permitted by the ACL, it is then processed for routing. Inbound ACLs are best used when you only want to check the traffic coming **into your router** from a specific direction or network. 
For example, here are the operational steps used when traffic has entered a router interface with an inbound standard IPv4 ACL configured.

1. The router extracts the source IPv4 address from the packet header.
2. The router starts at the top of the ACL and compares the source IPv4 address to each ACE in a sequential order.
3. When a match is made, the router carries out the instruction, either permitting or denying the packet, and the remaining ACEs in the ACL, if any, are not analyzed.
4. If the source IPv4 address does not match any ACEs in the ACL, the packet is discarded because there is an implicit deny ACE automatically applied to all ACLs.

An outbound ACL filters packets after being routed, regardless of the inbound interface. Outbound ACLs check packets just before they leave the router through a specific interface. Outbound ACLs are best used when:

1. **You want to control what traffic leaves your network** through a particular interface.
2. **The same type of filtering applies to multiple incoming networks**, so you filter everything at the point it’s leaving, not when it’s coming in.

--------------------------------------------------------------------------
### Wildcard masks

**Wildcard to Match a Host**
access-list 10 permit 192.168.1.1 0.0.0.0 

**Wildcard Mask to Match an IPv4 Subnet** - Permitting a /24 subnet
access-list 10 permit 192.168.1.0 0.0.0.255 

**Wildcard Mask to Match an IPv4 Address Range**
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1735051770000703k4x.png)

Some other examples :
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1735052070000p8jqzr.png)

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17350521030006t82mm.png)

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1735052293000xev1n7.png)

How to summarize the network?

Given a range of networks:
1.
- **192.168.16.0/24** to **192.168.31.0/24**.
- These networks increment in blocks of 1 in the third octet (16, 17, 18, ..., 31).
2.
- Binary representation of the third octet for this range:
    - **16** = `00010000`
    - **31** = `00011111`
- Notice the shared **4 most significant bits** (`0001` in binary) in this range.
3.
A network is summarized based on how many bits remain the **same** for all IPs in the range.

- In this case, the first **20 bits** (16 bits for the first two octets and 4 bits for the third octet) are the same for all IPs from 192.168.16.0 to 192.168.31.255.
- Therefore, the range can be summarized as **192.168.16.0/20**.


--------------------------------------------------------------------------
### Numbered ACLs VS Named ACLs

#### Numbered ACLs
```
access-list 101 permit ip 142.99.2.128 0.0.0.127 any
access-list 101 deny tcp any any eq 23

access-class 101 in
```

#### Named ACLs
```
ip access-list standard SSH_ACCESS
permit 142.99.2.128 0.0.0.127
deny tcp any any eq telnet

access-class SSH_ACCESS in
```

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1734966486000nmsrew.png)




Standard IP access list 1
    10 permit 192.168.0.0, wildcard bits 0.0.0.63
    20 deny 192.168.1.0, wildcard bits 0.0.0.255
    30 permit any

In this case:

- Rule **10** is evaluated first.
- Rule **20** is evaluated if rule 10 does not match.
- Rule **30** is evaluated last.

In summary, the sequence number represents the **position of the rule** within the access list and the lower the sequence number, the higher priority.