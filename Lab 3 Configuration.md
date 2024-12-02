![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733122462000tgtcv7.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17331225540009lr476.png)

## Add Net104(VLAN104)
##### ESW5
vlan 104
name VLAN104

int vlan 104
ip add 142.99.4.1 255.255.255.192
ipv6 add 2001:142:99:104::1/64
no shut

##### PC 5
ip 142.99.4.2 255.255.255.192 142.99.4.1
ip 2001:142:99:104::2/64

## Configure Layer 3 EtherChannel
==Because this is a layer 3 link (Between R2 and R3), the way to configure EtherChannel is a little bit different.==

##### R2
int port-channel 1
ip add 142.99.3.21 255.255.255.252
channel-group 1 - recheck malam ni balik
##### R3
int port-channel 1
ip add 142.99.3.22 255.255.255.252
channel-group 1

## Configure Routing Between R1 and ISP
##### R1
int g4/0
ip add 100.100.99.1 255.255.255.252
ipv6 add 2001:100:100:99::0/127
no shut

ip route 150.99.0.1 255.255.255.255 100.100.99.2 - next hop ip
ip route 150.99.0.1 255.255.255.255 g4/0 - exit interface
ipv6 route 2001:150:99::1/48 2001:100:100:99::1

==Configure Null route at each gateway device/edge router of the internal network==
ip route 142.99.0.0 255.255.0.0 Null0
ipv6 route 2001:142:99::/48 Null0
##### ISP
int g4/0 
ip add 100.100.99.2 255.255.255.252
ipv6 add 2001:100:100:99::1/127
no shut
int l0
ip add 150.99.0.1 255.255.255.255
ipv6 add 2001:150:99::1/48
no shut

ip route 142.99.0.0 255.255.0.0 100.100.99.1
ip route 142.99.0.0 255.255.0.0 g4/0
ipv6 route 2001:142:99::/48 2001:100:100:99:0

ip route 150.99.0.0 255.255.0.0 Null0
ipv6 route 2001:150:99::/48 Null0

## Configure OSPF with Auth
OSPF advertises connected networks.
Default-information originate VS Default-information originate always

##### R1
router ospf 1
router-id 5.5.5.5
network 142.99.3.4 0.0.0.3 area 0 
network 142.99.3.16 0.0.0.3 area 0
network 142.99.3.8 0.0.0.3 area 0
network 142.99.100.1 0.0.0.0 area 0 
passive-interface g4/0
default-information originate always - ==This basically installs a default route into every router that is connected through ospf.==

##### R2
router ospf 1
router-id 4.4.4.4
network 142.99.3.4 0.0.0.3 area 0 
network 142.99.3.12 0.0.0.3 area 0
network 142.99.3.0 0.0.0.3 area 0
network 142.99.100.2 0.0.0.0 area 0 - ==loopback address of R2==
network 142.99.3.20 0.0.0.3 area 50 

##### R3
router ospf 1
router-id 3.3.3.3
network 142.99.3.20 0.0.0.3 area 50
network 142.99.3.24 0.0.0.3 area 50
passive-interface g3/0

##### DSW1
router ospf 1
router-id 2.2.2.2 
network 142.99.3.0 0.0.0.3 area 0
network 142.99.3.16 0.0.0.3 area 0
network 142.99.2.128 0.0.0.127 area 0
network 142.99.2.0 0.0.0.127 area 0
network 142.99.0.0 0.0.1.0 area 0 

##### DSW2
router ospf 1
router-id 1.1.1.1 
network 142.99.3.8 0.0.0.3 area 0 
network 142.99.3.12 0.0.0.3 area 0
network 142.99.2.128 0.0.0.127 area 0
network 142.99.2.0 0.0.0.127 area 0
network 142.99.0.0 0.0.1.0 area 0

##### ESW5
router ospf 1
router-id 6.6.6.6 
network 142.99.4.0 0.0.0.63 area 50(VLAN 104)
network 142.99.3.24 0.0.0.3 area 50

**Verify**
show ip ospf neighbor
show ip ospf
show ip ospf database
show ip route


