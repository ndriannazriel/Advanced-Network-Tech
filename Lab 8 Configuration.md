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

