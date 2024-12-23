![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1734969308000iib9pn.png)

## Configure DHCP for Net105 on R3

##### R3
```
ip dhcp excluded-address 192.168.0.1 192.168.0.10
ip dhcp pool POOL_NET105
network 192.168.0.0 255.255.255.0
default-router 192.168.0.1
```

## Configure ACLs

### Net105 only have access to DMZ and Internet
