![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images173589032000079054k.png)

## Configure LLDP on switches

##### PHYSICAL SWITCH
```
conf t
lldp run
exit

int 
lldp transmit
lldp receive
```

##### Verify
```
show lldp neighbors detail
```

You should be able to see the other RGW physical routers(Other members) when this command is used.

## Configure RGW to sync the time

**Enable NTP Master**: Use the `ntp master` command to set the router as the NTP master. Specify the stratum level (lower values are more authoritative; commonly 1 or 2 is used).

##### RGW
```
ntp master 2
```

##### R1
```
ntp server 142.99.3.30
```

##### R2
```
ntp server 142.99.3.30
```

##### R3
```
ntp server 142.99.3.30
```

## Configure SNMPv3 



##### RGW
```
!---Create SNMPv3 user
conf t

snmp-server group SNMPv3-GROUP v3 priv snmp-server user SNMPv3-USER SNMPv3-GROUP v3 auth md5 AuthPassword priv aes 128 PrivPassword
```
- `SNMPv3-GROUP` with your group name.
- `SNMPv3-USER` with the username.
- `AuthPassword` with a strong authentication password.
- `PrivPassword` with a strong encryption password.
##### R1
```

```