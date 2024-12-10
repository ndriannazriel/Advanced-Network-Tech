| Device Name  | Interface | Ipv4          | SubMask         | Ipv6                   | DG  | DG Ipv6 | VLAN |
| ------------ | --------- | ------------- | --------------- | ---------------------- | --- | ------- | ---- |
| R3           | loopback  | 133.99.99.99  | 255.255.255.255 | 2001:133:99:99::99/128 |     |         |      |
|              | g1/0      | 133.99.1.1    | 255.255.255.252 | 2001:133:99:1::0/127   |     |         |      |
|              |           |               |                 |                        |     |         |      |
|              |           |               |                 |                        |     |         |      |
|              |           |               |                 |                        |     |         |      |
|              |           |               |                 |                        |     |         |      |
| R2           | g1/0      | 133.9.1.2     | 255.255.255.252 | 2001:133;99:1::1/127   |     |         |      |
|              | g2/0      | 133.99.1.5    | 255.255.255.252 | 2001:133:99:2::0/127   |     |         |      |
|              |           |               |                 |                        |     |         |      |
| R1(Physical) | g0/2      | 133.99.1.6    | 255.255.255.252 | 2001:133:99:2::1/127   |     |         |      |
|              | l0        | 133.99.99.100 | 255.255.255.255 |                        |     |         |      |
|              |           |               |                 |                        |     |         |      |
|              |           |               |                 |                        |     |         |      |
## Create loopback
##### R3
int l0
ip add 133.99.99.99 255.255.255.255
ipv6 add 2001:133:99:99::99/128

##### R1
int l0
ip add 133.99.99.100 255.255.255.255

## Configure OSPF

##### R3
router ospf 1
router-id 1.1.1.1

int l0
ip ospf 1 area 0

int g1/0
ip ospf 1 area 0

##### R2
router ospf 1
router-id 2.2.2.2
default-information originate always

int 
ip ospf 1 area 0


## Configure eBGP
##### R1
router bgp 19
bgp router-id 1.1.1.1
neighbor 

ip route 133.99.0.0 255.255.0.0 Null0
ipv6 route 2001:133:99::/48 Null0

## Configure Static routes on R2
ip route 


