## Configuring DCHP for VLANs
Because the L3 switch that I'm using is a legacy device then, you need to manually enable dhcp on it by using "service dhcp"

```
service dhcp
ip dhcp excluded-address 192.168.102.1 192.168.102.9
ip dhcp pool vlan102
network 192.168.102.0 255.255.255.0
default-router 192.168.102.1 (SVI in this case/might be the VIP if using HSRP)
```

```
show running-config | section dhcp
```

IP helper address(When the dhcp server is on a different network)
```
int vlan 102
ip helper-address 10.10.10.6 (address of the dhcp server)
```

### Troubleshooting
1. After configuring ip helper address, don't forget you still need routes to get to the dhcp server.
2. Don't forget service dhcp
3. Configure ip helper address on the SVI