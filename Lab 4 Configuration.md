![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733065059000drn94b.png)

## Add Net 105 (VLAN 105)
##### R3 
int g2/0
ip add 192.168.1.1 255.255.255.252
no shut

##### ESW7
int f0/0
ip add 192.168.1.2 255.255.255.252
no shut

vlan 105
name VLAN105

int vlan 105
ip add 192.168.0.1 255.255.255.128
no shut

int f1/1
switchport mode access
switchport access vlan 105

##### PC6
ip 192.168.0.2 255.255.255.128 192.168.0.1

##  Configure IPv4 PAT 
Need to use acl - allow the internal network to be translated 192.168.0.0/25

##### R3
access-list 1 permit 192.168.0.0 0.0.0.63
access-list 2 permit 192.168.0.64 0.0.0.63
ip nat pool PAT-POOL1 142.99.5.1 142.99.5.1 netmask 255.255.255.252
ip nat pool PAT-POOL2 142.99.5.2 142.99.5.2 netmask 255.255.255.252
ip nat inside source list 1 pool PAT-POOL1 overload
ip nat inside source list 2 pool PAT-POOL2 overload

Since OSPF can't be used to advertise this network, 