
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1732526463000wtzsqk.png)

## Setting Up the GNS3 Server
Download http://35.240.240.97:8003/25b4853a-b312-11ef-b954-3bcc513fa6ad/gns3vm.ovpn to setup your OpenVPN client after rebooting the server
~~ssh -i "GNS3ServerNdrian_key.pem" ndriannazriel04@20.243.132.11 (Azure)~~

**Prerequisites**
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17334099070005rgpmn.png)
For more info, visit https://cloud.google.com/compute/docs/instances/nested-virtualization/overview

**Installation**
cd /tmp
curl https://raw.githubusercontent.com/GNS3/gns3-server/master/scripts/remote-install.sh > gns3-remote-install.sh
sudo bash gns3-remote-install.sh --with-openvpn --with-iou --with-i386-repository

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1734698233000kaggqy.png)


Firewall Rule / Security Rule to use
custom - udp - 1194
custom - tcp - 8003

After setting up your VM, KVM(Nested virtualization) is not enabled by default. Here's a great source step by step to enable it on GCP.
https://github.com/infotechca/gns3-on-gcp/blob/main/README.md

Troubleshooting in case the same problems come up.
**"Cannot connect to compute 'Gns3Server' with request POST /projects"**
For this, that means that the server/vm has a problem so try restarting it or if worst comes to worst, create a whole new vm.
**"The image vios_l2-adventerprisek9-m.vmdk.SSA.152-4.0.55.E is missing"**
For this, delete the image and add back.

The problem here is if the vios image is in the lab and I off both the VM and OpenVPN, losing connection to the gns3 server, then once I reopen again, I notice that I can't open it and will get both of the messages above indicating something went wrong.

## Peering ISP with group members using ebgp

##### ISP1 (MY PHYSICAL ISP)
``` 
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
```

## Configure Local AAA for SSHv2 on ISP

### Requirements 
- Only users from VLAN 101 can initiate SSH sessions to the ISP router.
- Users must authenticate using locally defined credentials.

```
aaa new-model
username ANDRIAN secret mypass 

ip domain-name andrian.com
crypto key generate rsa
How many bits in the modulus [512]: 2048

ip ssh version 2

access-list 101 permit ip 142.99.2.128 0.0.0.127 any

line vty 0 4
access-class 101 in
transport input ssh
```

**Verify**
```
show ip ssh
show running-config | section aaa
show access-lists 101
show running-config | section line vty
```

ssh admin@100.100.99.2

## Reconfigure DSW4 After Changing
```
int g0/0
no nego auto
duplex full

int g0/1
no nego auto
duplex full

vtp mode client
vtp domain ANDRIAN
vtp password ANDRIAN

interface g0/0 
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan all

interface g0/1
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan all

int range g0/2 , g0/3
sw mode acc

int g0/3
sw access vlan 102
int g0/2
sw access vlan 103
```

##### Verify
```
sh vlan
sh ip int br
```

## Enable Port Security

- Apply **only** on access port
switchport port-security maximum 1
switchport port-security violation shutdown/protect/restrict
switchport port-security mac-address <MAC_ADDRESS> / sticky
##### DSW4
```
int g0/3
switchport port-security
switchport port-security maximum 1
switchport port-security violation restrict
switchport port-security mac-address sticky

int g0/2
switchport port-security
switchport port-security maximum 1
switchport port-security violation restrict
switchport port-security mac-address sticky
```

##### Verify
```
show port-security
```
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17349995680006j7k6c.png)

## Change Native Vlan 99
Apply this change to **all trunk interfaces** on switches DSW1 to DSW4 (10 interfaces total).

##### DSW1 (VTP server)
```
vlan 99
name NATIVE

interface port-channel 1, f1/1 , f1/2
switchport trunk native vlan 99
```

##### DSW2
```
interface port-channel 1, f1/1 , f1/2
switchport trunk native vlan 99
```

##### DSW3
```
int range f1/0 , f1/1
switchport trunk native vlan 99
```

##### DSW4
```
int range g0/0 , g0/1
switchport trunk native vlan 99
```

##### Verify
```
sh ip int br
sh int trunk
```
## Enable Spanning Tree Port-Fast And BDPU Guard

##### DSW3
```
spanning-tree portfast default
spanning-tree portfast bpduguard default
```

Use this one below instead.
```
spanning-tree portfast bpduguard 

interface f1/2
spanning-tree portfast

interface f1/3
spanning-tree portfast
```

**Verify**
```
show spanning-tree summary
spanning-tree interface <> br
```
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1734999824000lwxxdf.png)

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1734999869000rgq2n7.png)

## Configure R2 as DHCP Server
Ensure service dhcp is configured on dsw1 and dsw2

**Configure IP helper address on DSW1 and DSW2**
##### DSW1
```
service dhcp
int vlan 102
ip helper-address 142.99.3.1 redundancy
```

##### DSW2
```
service dhcp
int vlan 102
ip helper-address 142.99.3.14 redundancy
```
##### R2
```
ip dhcp excluded-address 142.99.2.1 142.99.2.10
ip dhcp pool VLAN102
network 142.99.2.0 255.255.255.128
default-router 142.99.2.3 (VIP for VLAN102) - Tells devices in VLAN102 to use this as DG
```

**Optional**
```
dns-server 8.8.8.8
domain-name andrian.com
```

**Verify**
```
show ip dhcp binding
sh ip dhcp pool
show ip helper address
```
## Configure IP DHCP Snooping and Dynamic ARP Inspection(DAI)
**Mark trusted interfaces**
Trusted interfaces are where DHCP servers or relay agents are connected. These interfaces are allowed to send DHCP server messages. *On Trunk Ports.*

##### DSW4 
**DHCP Snooping**
```
ip dhcp snooping
ip dhcp snooping vlan 102
ip dhcp snooping vlan 103

int f1/0, f1/1 
ip dhcp inspection trust
```
**Verify**
```
show ip dhcp snooping
show ip dhcp snooping binding
```
**DAI**
```
ip arp inspection vlan 102
ip arp inspection vlan 103

int f1/0, f1/1
ip arp inspection trust
```
**Verify**
```
show ip arp inspection
show ip arp inspection vlan <vlan-id>
```