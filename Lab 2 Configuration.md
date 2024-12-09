![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733650671000eu2ij2.png)



| Device Name | Interface | Ipv4         | SubMask         | Ipv6                   | DG    | DG Ipv6               | VLAN |
| ----------- | --------- | ------------ | --------------- | ---------------------- | ----- | --------------------- | ---- |
| DSW1        | VLAN101   | 142.99.2.129 | 255.255.255.128 | 2001:142:99:101::1/64  |       |                       |      |
|             | VLAN102   | 142.99.2.1   | 255.255.255.128 | 2001:142:99:102::1/64  |       |                       |      |
|             | VLAN103   | 142.99.0.1   | 255.255.254.0   | 2001:142:99:103::1/64  |       |                       |      |
| DSW2        | VLAN101   | 142.99.2.130 | 255.255.255.128 | 2001:142:99:101::2/64  |       |                       |      |
|             | VLAN102   | 142.99.2.2   | 255.255.255.128 | 2001:142:99:102::2/64  |       |                       |      |
|             | VLAN103   | 142.99.0.2   | 255.255.254.0   | 2001:142:99:103::2/64  |       |                       |      |
| DSW3        |           |              |                 |                        |       |                       |      |
| DSW4        |           |              |                 |                        |       |                       |      |
| PC1         |           | 142.99.2.140 | 255.255.255.128 | 2001:142:99:101::10/64 | VIP_1 | 2001:142:99:101::1/64 | 101  |
| PC2         |           | 142.99.0.10  | 255.255.254.0   | 2001:142:99:103::10/64 | VIP_3 | 2001:142:99:103::1/64 | 103  |
| PC3         |           | 142.99.2.10  | 255.255.255.128 | 2001:142:99:102::10/64 | VIP_2 | 2001:142:99:103::1/64 | 102  |
| PC4         |           | 142.99.0.20  | 255.255.254.0   | 2001:142:99:103::20/64 | VIP_3 | 2001:142:99:103::1/64 | 103  |
|             | VIP_1     | 142.99.2.131 |                 |                        |       |                       | 101  |
|             | VIP_4     | FE80::1      |                 |                        |       |                       | 101  |
|             | VIP_2     | 142.99.2.3   |                 |                        |       |                       | 102  |
|             | VIP_5     | FE80::2      |                 |                        |       |                       | 102  |
|             | VIP_3     | 142.99.0.3   |                 |                        |       |                       | 103  |
|             | VIP_6     | FE80::3      |                 |                        |       |                       | 103  |

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

## Configure HSRP with Tracking
##### DSW1(Vlan 101 and 103 is active)
```
track 1 int fa0/0 line-protocol
track 2 int fa0/1 line-protocol

standby 1 ip 142.99.2.131 
standby 1 priority 110
standby 1 preempt
standby 1 track 1 decrement 30
standby 1 track 2 decrement 30

standby 4 ipv6 FE80::1 
standby 4 priority 110
standby 4 preempt
standby 4 track 1 decrement 30
standby 4 track 2 decrement 30

standby 2 ip 142.99.2.3 
standby 2 priority 90
standby 2 preempt

standby 5 ipv6 FE80::2 
standby 5 priority 90
standby 5 preempt

standby 3 ip 142.99.0.3 
standby 3 priority 110
standby 3 preempt
standby 3 track 1 decrement 30
standby 3 track 2 decrement 30

standby 6 ipv6 FE80::3
standby 6 priority 110
standby 6 preempt
standby 6 track 1 decrement 30
standby 6 track 2 decrement 30

```

##### DSW2
```
track 1 int fa0/0 line-protocol
track 2 int fa0/1 line-protocol

standby 1 ip 142.99.2.131 
standby 1 priority 90
standby 1 preempt

standby 4 ipv6 FE80::1 
standby 4 priority 90
standby 4 preempt

standby 2 ip 142.99.2.3 
standby 2 priority 110
standby 2 preempt
standby 2 track 1 decrement 30
standby 2 track 2 decrement 30

standby 5 ipv6 FE80::2 
standby 5 priority 110
standby 5 preempt
standby 5 track 1 decrement 30
standby 5 track 2 decrement 30

standby 3 ip 142.99.0.3
standby 3 priority 90
standby 3 preempt

standby 6 ipv6 FE80::3 
standby 6 priority 90
standby 6 preempt
```

## Assign IPs to all interfaces in core area
```
R2 to DSW1 : ip add 142.99.3.1 255.255.255.252/ipv6 address 2001:142:99:3::1/64
DSW1 to R2 : ip add 142.99.3.2 255.255.255.252/ipv6 address 2001:142:99:3::2/64
R2 to R1   : ip add 142.99.3.5 255.255.255.252/ipv6 address 2001:142:99:4::1/64
R1 to R2   : ip add 142.99.3.6 255.255.255.252/ipv6 address 2001:142:99:4::2/64
R1 to DSW2 : ip add 142.99.3.9 255.255.255.252/ipv6 address 2001:142:99:5::1/64
DSW2 to R1 : ip add 142.99.3.10 255.255.255.252/ipv6 address 2001:142:99:5::2/64
DSW2 to R2 : ip add 142.99.3.13 255.255.255.252/ipv6 address 2001:142:99:6::1/64
R2 to DSW2 : ip add 142.99.3.14 255.255.255.252/ipv6 address 2001:142:99:6::2/64
DSW1 to R1 : ip add 142.99.3.17 255.255.255.252/ipv6 address 2001:142:99:7::1/64
R1 to DSW1 : ip add 142.99.3.18 255.255.255.252/ipv6 address 2001:142:99:7::2/64
```
## Configure Static and Floating Routes

##### R1
Vlan 101
```
ip route 142.99.2.128 255.255.255.128 142.99.3.17
ip route 142.99.2.128 255.255.255.128 142.99.3.10 200

ipv6 route 2001:142:99:101::/64 2001:142:99:7::1
ipv6 route 2001:142:99:101::/64 2001:142:99:5::2 200
```

Vlan 102
```
ip route 142.99.2.0 255.255.255.128 142.99.3.10
ip route 142.99.2.0 255.255.255.128 142.99.3.17 200

ipv6 route 2001:142:99:102::/64 2001:142:99:5::2
ipv6 route 2001:142:99:102::/64 2001:142:99:7::1 200
```

Vlan 103
```
ip route 142.99.0.0 255.255.254.0 142.99.3.17
ip route 142.99.0.0 255.255.254.0 142.99.3.10 200

ipv6 route 2001:142:99:103::/64 2001:142:99:7::1
ipv6 route 2001:142:99:103::/64 2001:142:99:5::2 200
```

##### R2
Vlan 101
```
ip route 142.99.2.128 255.255.255.128 142.99.3.2
ip route 142.99.2.128 255.255.255.128 142.99.3.13 200

ipv6 route 2001:142:99:101::/64 2001:142:99:3::2
ipv6 route 2001:142:99:101::/64 2001:142:99:6::1 200
```

Vlan 102
```
ip route 142.99.2.0 255.255.255.128 142.99.3.13
ip route 142.99.2.0 255.255.255.128 142.99.3.2 200

ipv6 route 2001:142:99:102::/64 2001:142:99:6::1
ipv6 route 2001:142:99:102::/64 2001:142:99:3::2 200
```

Vlan 103
```
ip route 142.99.0.0 255.255.254.0 142.99.3.2
ip route 142.99.0.0 255.255.254.0 142.99.3.13 200

ipv6 route 2001:142:99:103::/64 2001:142:99:3::2
ipv6 route 2001:142:99:103::/64 2001:142:99:6::1 200
```

## Configure Loopback and Routes to get there

##### DSW1
```

```

##### DSW2
```

```

