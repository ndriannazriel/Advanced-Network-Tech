| Device Name  | Interface | Ipv4           | SubMask         | Ipv6                    | DG  | DG Ipv6 | VLAN |
| ------------ | --------- | -------------- | --------------- | ----------------------- | --- | ------- | ---- |
| R3           | loopback  | 133.99.99.99   | 255.255.255.255 | 2001:133:99:99::99/128  |     |         |      |
|              | g1/0      | 133.99.1.1     | 255.255.255.252 | 2001:133:99:1::0/127    |     |         |      |
|              |           |                |                 |                         |     |         |      |
|              |           |                |                 |                         |     |         |      |
|              |           |                |                 |                         |     |         |      |
|              |           |                |                 |                         |     |         |      |
| R2           | g1/0      | 133.9.1.2      | 255.255.255.252 | 2001:133;99:1::1/127    |     |         |      |
|              | g2/0      | 133.99.1.5     | 255.255.255.252 | 2001:133:99:2::0/127    |     |         |      |
|              |           |                |                 |                         |     |         |      |
| R1(Physical) | g0/2      | 133.99.1.6     | 255.255.255.252 | 2001:133:99:2::1/127    |     |         |      |
|              | l0        | 133.99.99.100  | 255.255.255.255 |                         |     |         |      |
|              | g0/1      | 101.100.133.99 | 255.255.255.0   | 2001:101:100:133::99/64 |     |         |      |
|              |           |                |                 |                         |     |         |      |
## Create loopback
##### R3
```
int l0
ip add 133.99.99.99 255.255.255.255
ipv6 add 2001:133:99:99::99/128
```

##### R1
```
int l0
ip add 133.99.99.100 255.255.255.255
```
## Configure OSPF

##### R3
```
ip routing
ipv6 unicast-routing

router ospf 1
router-id 1.1.1.1

int l0
ip ospf 1 area 0

int g1/0
ip ospf 1 area 0
```

```
int 
ipv6 ospf network point-to-point
ipv6 ospf 1 area 0
```
##### R2(Default Gateway)
```
ip routing
ipv6 unicast-routing

router ospf 1
router-id 2.2.2.2
default-information originate always

int 
ip ospf 1 area 0
```

```
int 
ipv6 ospf network point-to-point
ipv6 ospf 1 area 0

ipv6 router ospf 1
default-information originate always
```

Verify




## Configure eBGP
##### R1
```
router bgp 19
bgp router-id 1.1.1.1
neighbor 101.100.133.71 remote-as 17
network 101.100.133.0 mask 255.255.255.0
network 133.99.0.0 mask 255.255.0.0

bgp log-neighbor-changes
address-family ipv6
neighbor 2001:101:71:99:: remote-as 17
neighbor 2001:100:71:99:: activate
network 2001:101:100:133::/64
network 2001:133:99::/48
```

```
ip route 133.99.0.0 255.255.0.0 Null0
ipv6 route 2001:133:99::/48 Null0
```

## Configure Static routes on R2 and R1

##### R2
```
ip route 133.71.0.0 255.255.0.0 g2/0
ipv6 route 2001:133:71::/48 2001:133:99:2::1
```

##### R1
```
ip route 133.99.99.99 255.255.255.255 g0/2
ipv6 route 2001:133:99:99::9/128 2001:133:99:2::
```


