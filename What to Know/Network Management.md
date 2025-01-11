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

## Network Time Protocol(NTP)
Before you get really deep into network management, the one thing that will help keep you on track is ensuring that all of your components are set to the same time and date.

The software clock on a router or switch starts when the system boots. It is the primary source of time for the system. It is important to synchronize the time across all devices on the network because all aspects of managing, securing, troubleshooting, and planning networks require accurate timestamping. When the time is not synchronized between devices, it will be impossible to determine the order of the events and the cause of an event.

Typically, the date and time settings on a router or switch can be set by using one of two methods You can manually configure the date and time, as shown in the example, or configure the Network Time Protocol (NTP).

Manually setting the time:
```
clock set 16:01:00 sept 25 2020
```

As a network grows, it becomes difficult to ensure that all infrastructure devices are operating with synchronized time. Even in a smaller network environment, the manual method is not ideal. If a router reboots, how will it get an accurate date and timestamp?

A better solution is to configure the NTP on the network. This protocol allows routers on the network to synchronize their time settings with an NTP server. A group of NTP clients that obtain time and date information from a single source have more consistent time settings. When NTP is implemented in the network, it can be set up to synchronize to a private master clock, or it can synchronize to a publicly available NTP server on the internet.

NTP uses UDP port 123 and is documented in RFC 1305.

### NTP Operation
NTP networks use a hierarchical system of time sources. Each level in this hierarchical system is called a stratum. The stratum level is defined as the number of hop counts from the authoritative source. The synchronized time is distributed across the network by using NTP. The figure displays a sample NTP network.

NTP servers are arranged in three levels showing the three strata. Stratum 1 is connected to Stratum 0 clocks.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736574060000ur5p7c.png)

**Stratum 0**
An NTP network gets the time from authoritative time sources. Stratum 0 devices such as atomic and GPS clocks are the most accurate authoritative time sources. Specifically, stratum 0 devices are non-network high-precision timekeeping devices assumed to be accurate and with little or no delay associated with them. In the figure, they are represented by the clock icon.

**Stratum 1**
The stratum 1 devices are network devices that are directly connected to the authoritative time sources. They function as the primary network time standard to stratum 2 devices using NTP.

**Stratum 2 and Lower**
The stratum 2 servers are connected to stratum 1 devices through network connections. Stratum 2 devices, such as NTP clients, synchronize their time by using the NTP packets from stratum 1 servers. They could also act as servers for stratum 3 devices.

Smaller stratum numbers indicate that the server is closer to the authorized time source than larger stratum numbers. The larger the stratum number, the lower the stratum level. The max hop count is 15. Stratum 16, the lowest stratum level, indicates that a device is unsynchronized. Time servers on the same stratum level can be configured to act as a peer with other time servers on the same stratum level for backup or verification of time.

### Configure NTP 
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736574109000wtrt6d.png)
Before NTP is configured on the network, the **show clock** command displays the current time on the software clock, as shown in the example. With the **detail** option, notice that the time source is user configuration. That means the time was manually configured with the **clock** command.

```
R1#show clock detail
20:55:10.207 UTC Fri Nov 15 2019
Time source is user configuration
```

The **ntp server** _ip-address_ command is issued in global configuration mode to configure 209.165.200.225 as the NTP server for R1. To verify the time source is set to NTP, use the **show clock detail** command. Notice that now the time source is NTP.
##### R1
```
ntp server 209.165.200.225
end
sh clock detail
```

In the next example, the **show ntp associations** and **show ntp status** commands are used to verify that R1 is synchronized with the NTP server at 209.165.200.225. Notice that R1 is synchronized with a stratum 1 NTP server at 209.165.200.225, which is synchronized with a GPS clock. The **show ntp status** command displays that R1 is now a stratum 2 device that is synchronized with the NTP server at 209.165.220.225.

**Note:** The highlight **st** stands for stratum.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736574312000k48qka.png)

Next, the clock on S1 is configured to synchronize to R1 with the **ntp server** command and then the configuration is verified with the **show ntp associations** command, as displayed.

##### S1
```
ntp server 192.168.1.1
end
sh ntp associations
```

Output from the **show ntp associations** command verifies that the clock on S1 is now synchronized with R1 at 192.168.1.1 via NTP. R1 is a stratum 2 device and NTP server to S1. Now S1 is a stratum 3 device that can provide NTP service to other devices in the network, such as end devices.

### Configuring as NTP Master
##### Router(Master)
```
ntp master 3
ntp logging
```

##### Other Routers
```
ntp server <ip address of the ntp master>
```
## SNMP
Now that your network is mapped and all of your components are using the same clock, it is time to look at how you can manage your network by using Simple Network Management Protocol (SNMP).

SNMP was developed to allow administrators to manage nodes such as servers, workstations, routers, switches, and security appliances, on an IP network. It enables network administrators to monitor and manage network performance, find and solve network problems, and plan for network growth.

SNMP is an application layer protocol that provides a message format for communication between managers and agents. The SNMP system consists of three elements:

- SNMP manager
- SNMP agents (managed node)
- Management Information Base (MIB)

To configure SNMP on a networking device, it is first necessary to define the relationship between the manager and the agent.

The SNMP manager is part of a network management system (NMS). The SNMP manager runs SNMP management software. As shown in the figure, the SNMP manager can collect information from an SNMP agent by using the “get” action and can change configurations on an agent by using the “set” action. In addition, SNMP agents can forward information directly to a network manager by using “traps”.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736580510000n32gxb.png)

The SNMP agent and MIB reside on SNMP client devices. Network devices that must be managed, such as switches, routers, servers, firewalls, and workstations, are equipped with an SNMP agent software module. MIBs store data about the device and operational statistics and are meant to be available to authenticated remote users. The SNMP agent is responsible for providing access to the local MIB.

SNMP defines how management information is exchanged between network management applications and management agents. The SNMP manager polls the agents and queries the MIB for SNMP agents on UDP port 161. SNMP agents send any SNMP traps to the SNMP manager on UDP port 162.

### SNMP Operation

