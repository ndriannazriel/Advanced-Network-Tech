
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1732526463000wtzsqk.png)

## Setting Up the GNS3 Server
http://20.210.105.88:8003/49d650ba-b0b5-11ef-a510-0022486800a8/gns3vm.ovpn to setup your OpenVPN client after rebooting the server
ssh -i "GNS3ServerKeyNew.pem" ndriannazriel04@20.210.105.88 (Azure)

**Installation**
cd /tmp
curl https://raw.githubusercontent.com/GNS3/gns3-server/master/scripts/remote-install.sh > gns3-remote-install.sh
sudo bash gns3-remote-install.sh --with-openvpn --with-iou --with-i386-repository

## Peering ISP with group members using ebgp

##### ISP1 (MY ISP)
router bgp 19
bgp router-id 2.2.2.2 
neighbor 100.100.99.1 remote-as 29
network 100.100.99.0 mask 255.255.255.252
network 150.99.0.0 mask 255.255.0.0

router bgp 19
bgp log-neighbor-changes
address-family ipv6
neighbor 2001:100:100:99:: remote-as 29
neighbor 2001:100:100:99:: activate
network 2001:100:100:99::/127
network 2001:150:99::/48


## Configure Local AAA for SSHv2 on ISP

### Requirements 
- Only users from VLAN 101 can initiate SSH sessions to the ISP router.
- Users must authenticate using locally defined credentials.

username admin privilege 15 secret your_password
username admin_ANDRIAN privilege 15 secret mypass

aaa new-model
aaa authentication login SSH_LOGIN local / aaa authentication login default local

access-list 101 permit ip 142.99.2.128 0.0.0.127 any

line vty 0 4
access-class 101 in
login authentication SSH_LOGIN
transport input ssh

ssh admin@142.99.2.1

## Reconfigure DSW4 After Changing

vtp mode client
vtp domain ANDRIAN
vtp password ANDRIAN

interface <>
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan all

## Enable Port Security

- Apply **only** on access port

##### DSW4
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown/protect/restrict
switchport port-security mac-address <MAC_ADDRESS>

show port-security

## Change Native Vlan 99
Apply this change to **all trunk interfaces** on switches DSW1 to DSW4 (10 interfaces total).

vlan 99
name NATIVE

interface <>
switchport trunk native vlan 99

## Enable Spanning Tree Port-Fast And BDPU Guard

##### DSW3
interface <>
spanning-tree portfast
spanning-tree bpduguard enable

spanning-tree interface <> detail
show spanning-tree summary

## Configure R2 as DHCP Server
Ensure service dhcp is configured on dsw1 and dsw2

**Configure IP helper address on DSW1 and DSW2**
int vlan 102
ip helper-address (ip on the interface connecting to R2)

##### R2
ip dhcp excluded-address 142.99.2.1 142.99.2.9
ip dhcp pool vlan102
network 142.99.2.0 255.255.255.128
default-router 142.99.2.3 (VIP for VLAN102) - Tells devices in VLAN102 to use this as DG

**Optional**
dns-server 8.8.8.8
domain-name example.com

show ip dhcp binding

## Configure IP DHCP Snooping and Dynamic ARP Inspection(DAI)

##### DSW4 
**DHCP Snooping**
ip dhcp snooping
ip dhcp snooping vlan 102
ip dhcp snooping vlan 103

**Mark trusted interfaces**
Trusted interfaces are where DHCP servers or relay agents are connected. These interfaces are allowed to send DHCP server messages. *On Trunk Ports.*

int f1/0, f1/1 
ip dhcp inspection trust

show ip dhcp snooping
show ip dhcp snooping binding

**DAI**
ip arp inspection vlan 101
ip arp inspection vlan 102
ip arp inspection vlan 103

int f1/0, f1/1
ip arp inspection trust

show ip arp inspection
show ip arp inspection vlan <vlan-id>
