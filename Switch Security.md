Layer 2 devices are considered to be the weakest link in a company's security infrastructure.
## Implement Port Security
Prevent MAC address table overflow attacks. Port security limits the number of valid MAC addresses allowed on a port. It allows an administrator to manually configure MAC addresses for a port or to permit the switch to dynamically learn a limited number of MAC addresses. When a port configured with port security receives a frame, the source MAC address of the frame is compared to the list of secure source MAC addresses that were manually configured or dynamically learned on the port.
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733264987000ei9946.png)
##### 1. Secure Unused Ports
```
S1(config)# interface range fa0/8 - 24
S1(config-if-range)# shutdown
%LINK-5-CHANGED: Interface FastEthernet0/8, changed state to administratively down
(output omitted)
%LINK-5-CHANGED: Interface FastEthernet0/24, changed state to administratively down
S1(config-if-range)#
```
##### 2. Enable Port Security
```
S1(config)# interface fa0/1
S1(config-if)# switchport mode access
S1(config-if)# switchport port-security
S1(config-if)# switchport port-security maximum 2
S1(config-if)# switchport port-security mac-address aaaa.bbbb.1234
S1(config-if)# switchport port-security mac-address sticky
```

**Security Violation Mode descriptions**
`Switch(config-if)# switchport port-security violation { protect | restrict | shutdown}`
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733265560000d97sff.png)

##### 3. Verify Port Security
```
show port-security
show port-security address
```


## Mitigate VLAN Attacks
As a quick review, a VLAN hopping attack can be launched in one of three ways:

- Spoofing DTP messages from the attacking host to cause the switch to enter trunking mode. From here, the attacker can send traffic tagged with the target VLAN, and the switch then delivers the packets to the destination.
- Introducing a rogue switch and enabling trunking. The attacker can then access all the VLANs on the victim switch from the rogue switch.
- Another type of VLAN hopping attack is a double-tagging (or double-encapsulated) attack. This attack takes advantage of the way hardware on most switches operate.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733265829000869kn4.png)


## Mitigate DHCP Attacks
