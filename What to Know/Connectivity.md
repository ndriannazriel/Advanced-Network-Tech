![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17334569850003gsq6d.png)
Figure 1.

Configuration
1. Create SVI 
2. Select layer 3 interface (f0/0) on the layer 3 switch
3. Assign IP address to f0/0
4. Ensure connectivity

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17334571120004et96v.png)
Figure 2.

1. Assign IP to the router's interface
2. Assign IP to the PC
3. Ensure connectivity


### Remote Connectivity Using SVI
To prepare a switch for remote management access, the switch must have a switch virtual interface (SVI) configured with an IPv4 address and subnet mask or an IPv6 address and a prefix length for IPv6. The SVI is a virtual interface, not a physical port on the switch. Keep in mind that to manage the switch from a remote network, the switch must be configured with a default gateway. This is very similar to configuring the IP address information on host devices.
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733458569000pbuuz6.png)


#### When to use SVI and when to use inter-vlan routing?
