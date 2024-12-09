![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733650671000eu2ij2.png)



| Device Name | Interface | Ipv4         | SubMask         | Ipv6                   | DG           | DG Ipv6               | VLAN |
| ----------- | --------- | ------------ | --------------- | ---------------------- | ------------ | --------------------- | ---- |
| DSW1        | VLAN101   | 142.99.2.129 | 255.255.255.128 | 2001:142:99:101::1/64  |              |                       |      |
|             | VLAN102   | 142.99.2.1   | 255.255.255.128 | 2001:142:99:102::1/64  |              |                       |      |
|             | VLAN103   | 142.99.0.1   | 255.255.254.0   | 2001:142:99:103::1/64  |              |                       |      |
| DSW2        | VLAN101   | 142.99.2.130 | 255.255.255.128 | 2001:142:99:101::2/64  |              |                       |      |
|             | VLAN102   | 142.99.2.2   | 255.255.255.128 | 2001:142:99:102::2/64  |              |                       |      |
|             | VLAN103   | 142.99.0.2   | 255.255.254.0   | 2001:142:99:103::2/64  |              |                       |      |
| DSW3        |           |              |                 |                        |              |                       |      |
| DSW4        |           |              |                 |                        |              |                       |      |
| PC1         |           | 142.99.2.140 | 255.255.255.128 | 2001:142:99:101::10/64 | 142.99.2.129 | 2001:142:99:101::1/64 | 101  |
| PC2         |           | 142.99.0.10  | 255.255.254.0   | 2001:142:99:103::10/64 | 142.99.0.1   | 2001:142:99:103::1/64 | 103  |
| PC3         |           | 142.99.2.10  | 255.255.255.128 | 2001:142:99:102::10/64 | 142.99.2.1   | 2001:142:99:103::1/64 | 102  |
| PC4         |           | 142.99.0.20  | 255.255.254.0   | 2001:142:99:103::20/64 | 142.99.0.1   | 2001:142:99:103::1/64 | 103  |
|             |           |              |                 |                        |              |                       |      |
|             |           |              |                 |                        |              |                       |      |

## Configure L2 EtherChannel with Tracking

##### DSW1 and DSW2
```
interface range fa1/0 , fa1/1
channel-group 1 mode on
switchport mode trunk
no shut

interface Port-channel 1
switchport mode trunk
```

When changing the etherchannel's load balance setting, you are required to reset the etherchannel configuration.
```
port-channel load-balance src-ip    
```

Remove, recreate and readd members
```
interface range fa1/0 , fa1/1      
no channel-group 1                   
exit
no interface port-channel 1   

below no need to do i think
interface Port-channel 1
port-channel load-balance src-ip

interface range fa1/0, fa1/1
channel-group 1 mode on
```

**Verify**
```
show running-config interface port-channel <channel-number>
show etherchannel load-balance
show etherchannel summary
show etherchannel 1 detail
```

