## Configuring DCHP for VLANs

```
ip dhcp excluded-address 192.168.102.1 192.168.102.9
ip dhcp pool vlan102
network 192.168.102.0 255.255.255.0
default-router 192.168.102.1 (SVI in this case/might be the VIP if using HSRP)
```

```
show running-config | section dhcp
```

IP helper address(When the dhcp server is on a different network)
