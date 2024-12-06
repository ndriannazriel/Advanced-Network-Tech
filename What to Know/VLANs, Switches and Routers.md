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
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733467301000pi283s.png)

- Layer 2 switches support SVIs strictly only for management, not for routing.
- To route between VLANs, you need a **Layer 3 device**.

An SVI on a Layer 2 switch is limited to:

- Providing a management IP for the switch itself (e.g., SSH or Telnet access).
- Communicating with devices within the same VLAN.

Why?
Because switches operate at a data link layer and have no way for routing. That's why SVIs on L2 switches are created for the purpose of management only through SSH.

If want to route between VLANs but you don't have a layer 3 switch, use router on a stick.

## Inter Vlan Routing (Router on a stick)
**On router**
```
interface GigabitEthernet0/0.10
   encapsulation dot1q 10
   ip address 192.168.10.1 255.255.255.0
interface GigabitEthernet0/0.20
   encapsulation dot1q 20
   ip address 192.168.20.1 255.255.255.0
```
1. Configure sub interfaces on router as above
2. Enable trunking on ports connected to the switch

