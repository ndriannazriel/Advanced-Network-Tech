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
```

## Create VPN Tunnel

##### R1
```
ip access-list extended R1->R4
permit ip 192.168.99.0 0.0.0.255 192.168.95.0 0.0.0.255

crypto isakmp policy 1
encryption aes
hash sha
authentication pre-share
group 2

crypto isakmp key 0 mypass address 101.100.133.95
crypto ipsec transform-set mytset esp-aes esp-sha-hmac
exit

crypto map CRYPTOMAP 10 ipsec-isakmp
set peer 101.100.133.95
set transform-set mytset
match address R1->R4

int g3/0
crypto map CRYPTOMAP
```

##### R4
```
ip access-list extended R4->R1
permit ip 192.168.95.0 0.0.0.255 192.168.99.0 0.0.0.255

crypto isakmp policy 1
encryption aes
hash sha
authentication pre-share
group 2

crypto isakmp key 0 mypass address 101.100.133.99
crypto ipsec transform-set mytset esp-aes esp-sha-hmac
exit

crypto map CRYPTOMAP 10 ipsec-isakmp
set peer 101.100.133.99
set transform-set mytset
match address R4->R1

int g3/0
crypto map CRYPTOMAP
```

