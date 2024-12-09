![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733746637000phtu3y.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733747790000t2ts3t.png)

## Create VLANs and Enable VTP

DSW1 and DSW2 in server mode whilst others are VTP clients.

```
vlan 101
name VLAN101
vlan 102
name VLAN102
vlan 103
name VLAN103
```

```
VTP mode
vtp mode server
vtp domain ANDRIAN
vtp password ANDRIAN

vtp mode client
vtp domain ANDRIAN
vtp password ANDRIAN
```

## Enable IP routing
```
ip routing
ipv6 unicast-routing
```

## Configure STP root bridge
Root bridge vlan 101,103 on DSW1
Root bridge vlan 102 on DSW2

- The default STP priority is `32768`. To make a switch the root bridge for a VLAN, assign it a **lower priority** than other switches for that VLAN.
- Priority values increment in multiples of `4096`. Use a value like `24576` for root bridge preference

##### DSW1
```
spanning-tree vlan 101 priority 24576
spanning-tree vlan 103 priority 24576
```

##### DSW2
```
spanning-tree vlan 102 priority 24576
```

## Configuring Basic Connectivity
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

##### DSW1
```
int <>
switchport trunk encap dot1q
switchport mode trunk
switchport trunk allowed vlan all

int vlan 101
ip add 142.99.2.129 255.255.255.128 
ipv6 add 2001:142:99:101::1/64

int vlan 102
ip add 142.99.2.1 255.255.255.128
ipv6 add 2001:142:99:102::1/64

int vlan 103
ip add 142.99.0.1 255.255.254.0
ipv6 add 2001:142:99:103::1/64


```

##### DSW2
```
int <>
switchport trunk encap dot1q
switchport mode trunk
switchport trunk allowed vlan all

int vlan 101
ip add 142.99.2.130 255.255.255.128 
ipv6 add 2001:142:99:101::2/64

int vlan 102
ip add 142.99.2.2 255.255.255.128
ipv6 add 2001:142:99:102::2/64

int vlan 103
ip add 142.99.0.2 255.255.254.0
ipv6 add 2001:142:99:103::2/64

```

##### DSW3
```
int <>
switchport trunk encap dot1q
switchport mode trunk
switchport trunk allowed vlan all

int
switchport mode access
switchport access vlan 
```

##### DSW4
```
int <>
switchport trunk encap dot1q
switchport mode trunk
switchport trunk allowed vlan all

int switchport mode access
switchport access vlan
```


##### PC1
```
ip  142.99.2.140 255.255.255.128 142.99.2.129
ip 2001:142:99:101::10/64 2001:142:99:101::1/64
save
```

##### PC2
```

```

##### PC3
```

```

##### PC4
```

```