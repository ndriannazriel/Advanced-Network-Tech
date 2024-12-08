### Static NAT
Static NAT maps one private IP address to one public IP address.
```
Router(config)# ip nat inside source static 192.168.1.10 203.0.113.10
Router(config)# interface g0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# ip nat inside
Router(config-if)# exit
Router(config)# interface g0/1
Router(config-if)# ip address 203.0.113.1 255.255.255.0
Router(config-if)# ip nat outside
Router(config-if)# exit
```

### Dynamic NAT
Dynamic NAT uses a pool of public IP addresses for mapping private IP addresses.
```
Router(config)# ip nat pool NAT-POOL 203.0.113.10 203.0.113.20 netmask 255.255.255.0
Router(config)# access-list 1 permit 192.168.1.0 0.0.0.255
Router(config)# ip nat inside source list 1 pool NAT-POOL
Router(config)# interface g0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# ip nat inside
Router(config-if)# exit
Router(config)# interface g0/1
Router(config-if)# ip address 203.0.113.1 255.255.255.0
Router(config-if)# ip nat outside
Router(config-if)# exit
```

### PAT
PAT maps multiple private IP addresses to a single public IP address using different port numbers.
```
Router(config)# access-list 1 permit 192.168.1.0 0.0.0.255
Router(config)# ip nat inside source list 1 interface g0/1 overload
Router(config)# interface g0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# ip nat inside
Router(config-if)# exit
Router(config)# interface g0/1
Router(config-if)# ip address 203.0.113.1 255.255.255.0
Router(config-if)# ip nat outside
Router(config-if)# exit
```
FYI : The outside ip address the 203.0.113.1 will be the used by after the translation

### Special case from Lab 4
Each global IP should only support 64 addresses
```
access-list 1 permit 192.168.0.0 0.0.0.63
access-list 2 permit 192.168.0.64 0.0.0.63
ip nat pool PAT-POOL1 142.99.5.1 142.99.5.1 netmask 255.255.255.252
ip nat pool PAT-POOL2 142.99.5.2 142.99.5.2 netmask 255.255.255.252
ip nat inside source list 1 pool PAT-POOL1 overload
ip nat inside source list 2 pool PAT-POOL2 overload
```

1. The /25 subnet (128 hosts but needs 125 hosts) is split to be half 
2. Create a pool
3. Bind the acl to the pool
4. Don't forget to add the overload keyword