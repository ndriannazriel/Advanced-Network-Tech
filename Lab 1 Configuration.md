![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733746637000phtu3y.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733747790000t2ts3t.png)

## Enable VTP

DSW1 and DSW2 in server mode whilst others are VTP clients.

```
VTP mode
vtp mode server
vtp domain ANDRIAN
vtp password ANDRIAN

vtp mode client
vtp domain ANDRIAN
vtp password ANDRIAN
```

## Enable IP routing
```
ip routing
ipv6 unicast-routing
```

## Configure STP root bridge
Root bridge vlan 101,103 on DSW1
Root bridge vlan 102 on DSW2

- The default STP priority is `32768`. To make a switch the root bridge for a VLAN, assign it a **lower priority** than other switches for that VLAN.
- Priority values increment in multiples of `4096`. Use a value like `24576` for root bridge preference

##### DSW1
```
spanning-tree vlan 101 priority 24576
spanning-tree vlan 103 priority 24576
```

##### DSW2
```
spanning-tree vlan 102 priority 24576
```

## Configuring Basic Connectivity

##### DSW1
```

```

##### DSW2
```

```

##### DSW3
```

```

##### DSW4
```

```


##### PC1
```

```

##### PC2
```

```

##### PC3
```

```

##### PC4
```

```