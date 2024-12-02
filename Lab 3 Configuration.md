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

ip route 150.99.0.0






