1. Configure Topology as above.
2. Ensure loopback end to end connectivity.
3. Enable SSH on all routers with cisco:cisco
4. Add private network at R3 and use NAT to resolve private to public ip addresses.
5. Create a GRE tunnel interconnecting both R3.
6. Create a VPN tunnel at the default gateway(R2). Make sure static routes exist between R2 and R1.
7. Configure NTP for all devices. Make R1 as NTP master.
8. Configure PRTG on lab PC with SNMPv3 to monitor traffic.

## Enable SSH on all routers
```
username ANDRIAN secret mypass
ip domain-name andrian.com
crypto key generate rsa
How many bits in the modulus [512]: 2048

ip ssh version 2

access-list 10 permit host <x.x.x.x>

line vty 0 4
access-class 10 in
transport input ssh
```

## Configure Basic Settings

##### R1
```
int g0/1
ip add 133.99.1.6 255.255.255.252 
ipv6 add 2001:133:99:2::1/127
no sh

int g0/2
ip add 101.100.133.99 255.255.255.0
ipv6 add 2001:101:100:133::99/64
no sh

ip route 133.99.99.99 255.255.255.255 133.99.1.5
ipv6 route 2001:133:99:99::99/128 2001:133:99:2::

!---Might not need this
ip route 133.99.1.0 255.255.255.252 133.99.1.5
ipv6 route 2001:133:99:1::/127 2001:133:99:2::

ip route 133.99.0.0 255.255.0.0 Null0
ipv6 route 2001:133:99::/48 Null0

router bgp 19
bgp router-id 1.1.1.1
neighbor 101.100.133.95 remote-as 15
network 133.99.0.0 mask 255.255.0.0

bgp log-neighbor-changes
neighbor 2001:101:100:133::95 remote-as 159
address-family ipv6
neighbor 2001:101:100:133::95 activate
network 2001:133:99::/48


```
## Create Private Network on R3 and Configure NAT

##### R3
```
access-list 1 permit 192.168.99.0 0.0.0.255
ip nat inside source list 1 interface <outside interface> overload

int g2/0
ip nat inside

int g1/0
ip nat outside
```

Make sure to configure the static routing properly.

## Create GRE tunnel on R3

##### R3
```
int tunnel 1
ip add 172.16.0.1 255.255.255.252
tunnel source 133.99.1.1
tunnel dest 133.95.1.1

int tunnel 2
ip add 172.16.1.1 255.255.255.252
tunnel source l0
tunnel dest 133.95.99.99
```

##### R6
```
int tunnel 1
ip add 172.16.0.2 255.255.255.252
tunnel source 133.95.1.1
tunnel dest 133.99.1.1

int tunnel 2
ip add 172.16.1.2 255.255.255.252
tunnel source l0
tunnel dest 133.99.99.99
```

## Create VPN Tunnel

##### R2 IPV4
```
ip access-list extended R2->R5
permit ip 192.168.99.0 0.0.0.255 192.168.95.0 0.0.0.255

crypto isakmp policy 1
encryption aes
hash sha
authentication pre-share
group 2

crypto isakmp key 0 mypass address 133.95.1.5
crypto ipsec transform-set mytset esp-aes esp-sha-hmac
mode tunnel
exit

crypto map CRYPTOMAP 10 ipsec-isakmp
set peer 133.95.1.5
set transform-set mytset
match address R2->R5

int g2/0
crypto map CRYPTOMAP
```

##### R2 IPV6
```
ipv6 access-list R2->R5-IPV6
permit ip 

crypto isakmp policy 1
encryption aes
hash sha
authentication pre-share
group 2

crypto isakmp key 0 mypass address ipv6 
crypto ipsec transform-set mytsetIPV6 esp-aes esp-sha-hmac
mode tunnel

crypto map ipv6 IPV6-CM 10 ipsec-isakmp
set peer
set transform-set mytsetIPV6
match address R2->R5-IPV6

int g3/0
ipv6 enable
ipv6 crypto map IPV6-CM
```

--------------------------------------------------------------------------
##### R5
```
ip access-list extended R5->R2
permit ip 192.168.95.0 0.0.0.255 192.168.99.0 0.0.0.255

crypto isakmp policy 1
encryption aes
hash sha
authentication pre-share
group 2

crypto isakmp key 0 mypass address 133.99.1.5
crypto ipsec transform-set mytset esp-aes esp-sha-hmac
mode tunnel
exit

crypto map CRYPTOMAP 10 ipsec-isakmp
set peer 133.99.1.5
set transform-set mytset
match address R5->R2

int g2/0
crypto map CRYPTOMAP
```

## Configure NTP for all devices

##### R1
```
ntp master 3
ntp logging
```

##### R2,R3
```
ntp server 133.99.1.6
```

##### Verify
```
sh clock detail
sh ntp status
sh run ntp
sh ntp association
```

## Configure SNMPv3

##### R1
``` 
snmp-server group SNMP-GROUP v3 priv
snmp-server user SNMP-USER SNMP-GROUP v3 auth md5 CISCO12345 priv aes 128 CISCO12345
snmp-server host <PC lab ip> version 3 priv host-user

logging host <PC lab ip>
logging trap informational
logging source lo 0
```

s