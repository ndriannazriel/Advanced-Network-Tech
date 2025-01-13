When determining which type of address is used, it is important to remember that NAT terminology is always applied from the perspective of the device with the translated address:

- **Inside address** - The address of the device which is being translated by NAT.
- **Outside address** - The address of the destination device.

NAT also uses the concept of local or global with respect to addresses:

- **Local address** - A local address is any address that appears on the inside portion of the network.
- **Global address** - A global address is any address that appears on the outside portion of the network.

The terms, inside and outside, are combined with the terms local and global to refer to specific addresses. The NAT router, R2 in the figure, is the demarcation point between the inside and outside networks. R2 is configured with a pool of public addresses to assign to inside hosts. Refer to the network and NAT table in the figure for the following discussion of each of the NAT address types.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17367718220006k8ydf.png)
### Static NAT
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736772378000e4k14w.png)
1. The client wants to open a connection to the web server. The client sends a packet to the web server using the public IPv4 destination address of 209.165.201.5. This is the inside global address of the web server.
2. The first packet that R2 receives from the client on its NAT outside interface causes R2 to check its NAT table. The destination IPv4 address of 209.165.201.5 is located in the NAT table and is translated to 192.168.10.254.
3. R2 replaces the inside global address of 209.165.201.5 with the inside local address of 192.168.10.254. R2 then forwards the packet towards the web server.
4. The web server receives the packet and responds to the client using the inside local address, 192.168.10.254 as the source address of the response packet.
5. (a) R2 receives the packet from the web server on its NAT inside interface with source address of the inside local address of the web server, 192.168.10.254.  
    (b) R2 checks the NAT table for a translation for the inside local address. The address is found in the NAT table. R2 translates the source address 192.168.10.254 to the inside global address of 209.165.201.5 and forwards the packet toward the client.
6. (Not shown) The client receives the packet and continues the conversation. The NAT router performs Steps 2 to 5b for each packet.

Static NAT maps one private IP address to one public IP address.
```
Router(config)# ip nat inside source static 192.168.1.10 203.0.113.10
Router(config)# interface g0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# ip nat inside
Router(config-if)# exit
Router(config)# interface g0/1
Router(config-if)# ip address 203.0.113.1 255.255.255.0
Router(config-if)# ip nat outside
Router(config-if)# exit
```

### Dynamic NAT
Dynamic NAT uses a pool of public IP addresses for mapping private IP addresses.
```
Router(config)# ip nat pool NAT-POOL 203.0.113.10 203.0.113.20 netmask 255.255.255.0
Router(config)# access-list 1 permit 192.168.1.0 0.0.0.255
Router(config)# ip nat inside source list 1 pool NAT-POOL
Router(config)# interface g0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# ip nat inside
Router(config-if)# exit
Router(config)# interface g0/1
Router(config-if)# ip address 203.0.113.1 255.255.255.0
Router(config-if)# ip nat outside
Router(config-if)# exit
```

### PAT
PAT maps multiple private IP addresses to a single public IP address using different port numbers.
```
Router(config)# access-list 1 permit 192.168.1.0 0.0.0.255
Router(config)# ip nat inside source list 1 interface g0/1 overload
Router(config)# interface g0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# ip nat inside
Router(config-if)# exit
Router(config)# interface g0/1
Router(config-if)# ip address 203.0.113.1 255.255.255.0
Router(config-if)# ip nat outside
Router(config-if)# exit
```
FYI : The outside ip address the 203.0.113.1 will be the used by after the translation

### Special case from Lab 4
Each global IP should only support 64 addresses
```
access-list 1 permit 192.168.0.0 0.0.0.63
access-list 2 permit 192.168.0.64 0.0.0.63
ip nat pool PAT-POOL1 142.99.5.1 142.99.5.1 netmask 255.255.255.252
ip nat pool PAT-POOL2 142.99.5.2 142.99.5.2 netmask 255.255.255.252
ip nat inside source list 1 pool PAT-POOL1 overload
ip nat inside source list 2 pool PAT-POOL2 overload
```

1. The /25 subnet (128 hosts but needs 125 hosts) is split to be half 
2. Create a pool
3. Bind the acl to the pool
4. Don't forget to add the overload keyword