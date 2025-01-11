## Cisco Discovery Protocol(CDP)
CDP is a Cisco proprietary Layer 2 protocol that is used to gather information about Cisco devices which share the same data link. CDP is media and protocol independent and runs on all Cisco devices, such as routers, switches, and access servers.

These advertisements share information about the type of device that is discovered, the name of the devices, and the number and type of the interfaces.

Because most network devices are connected to other devices, CDP can assist in network design decisions, troubleshooting, and making changes to equipment. CDP can also be used as a network discovery tool to determine the information about the neighboring devices. This information gathered from CDP can help build a logical topology of a network when documentation is missing or lacking in detail.

### Configure CDP
For Cisco devices, CDP is enabled by default. For security reasons, it may be desirable to disable CDP on a network device globally, or per interface. With CDP, an attacker can gather valuable insight about the network layout, such as IP addresses, IOS versions, and types of devices.

To enable CDP globally for all the supported interfaces on the device, enter **cdp run** in the global configuration mode. CDP can be disabled for all the interfaces on the device with the **no cdp run** command in the global configuration mode.

```
no cdp run

sh cdp
```

To disable CDP on a specific interface, such as the interface facing an ISP, enter **no cdp enable** in the interface configuration mode. CDP is still enabled on the device; however, no more CDP advertisements will be sent out that interface. To enable CDP on the specific interface again, enter **cdp enable**.

```
int 
cdp enable
```

To verify the status of CDP and display a list of neighbors, use the **show cdp neighbors** command in the privileged EXEC mode. The **show cdp neighbors** command displays important information about the CDP neighbors.

```
sh cdp neighbors
```

Use the **show cdp interface** command to display the interfaces that are CDP-enabled on a device. The status of each interface is also displayed.

```
sh cdp interface
```

### Discovering Devices
The **show cdp neighbors** command provides helpful information about each CDP neighbor device, including the following:

- **Device identifiers** - This is the host name of the neighbor device (S1).
- **Port identifier** - This is the name of the local and remote port (G0/0/1 and F0/5, respectively).
- **Capabilities list** - This shows whether the device is a router or a switch (S for switch; I for IGMP is beyond scope for this course).
- **Platform** - This is the hardware platform of the device (WS-C3560 for Cisco 3560 switch).

To see the ip address of a device, you can use the command below
```
sh cdp neighbors detail
```

## Link Layer Discovery Protocol (LLDP)
The Link Layer Discovery Protocol (LLDP) does the same thing as CDP, but it is not specific to Cisco devices. As a bonus, you can still use it if you have Cisco devices. One way or another, you will get your network map.

LLDP is a vendor-neutral neighbor discovery protocol similar to CDP. LLDP works with network devices, such as routers, switches, and wireless LAN access points. This protocol advertises its identity and capabilities to other devices and receives the information from a physically-connected Layer 2 device.

### Configure LLDP
Depending on the device, LLDP may be enabled by default. To enable LLDP globally on a Cisco network device, enter the **lldp run** command in the global configuration mode. To disable LLDP, enter the **no lldp run** command in the global configuration mode.

Similar to CDP, LLDP can be configured on specific interfaces. However, LLDP must be configured separately to transmit and receive LLDP packets.

1. To disable the sending of LLDP messages on an interface enter no lldp transmit
2. To disable the receiving of LLDP messages on the interface enter no lldp receive

```
lldp run
int 
lldp transmit
lldp receive
end

sh lldp
sh lldp neighbors
sh lldp neighbors detail
```


