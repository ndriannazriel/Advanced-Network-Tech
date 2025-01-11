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
SNMP agents that reside on managed devices collect and store information about the device and its operation. This information is stored by the agent locally in the MIB. The SNMP manager then uses the SNMP agent to access information within the MIB.

There are two primary SNMP manager requests, get and set. A get request is used by the NMS to query the device for data. A set request is used by the NMS to change configuration variables in the agent device. A set request can also initiate actions within a device. For example, a set can cause a router to reboot, send a configuration file, or receive a configuration file. The SNMP manager uses the get and set actions to perform the operations described in the table.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17365807640009d4ndv.png)

The SNMP agent responds to SNMP manager requests as follows:

- **Get an MIB variable** - The SNMP agent performs this function in response to a GetRequest-PDU from the network manager. The agent retrieves the value of the requested MIB variable and responds to the network manager with that value.
- **Set an MIB variable** - The SNMP agent performs this function in response to a SetRequest-PDU from the network manager. The SNMP agent changes the value of the MIB variable to the value specified by the network manager. An SNMP agent reply to a set request includes the new settings in the device.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17365808450001av4zt.png)

### SNMP Agent Traps
An NMS periodically polls the SNMP agents that are residing on managed devices using the get request. The NMS queries the device for data. Using this process, a network management application can collect information to monitor traffic loads and to verify the device configurations of managed devices. The information can be displayed via a GUI on the NMS. Averages, minimums, or maximums can be calculated. The data can be graphed, or thresholds can be set to trigger a notification process when the thresholds are exceeded. For example, an NMS can monitor CPU utilization of a Cisco router. The SNMP manager samples the value periodically and presents this information in a graph for the network administrator to use in creating a baseline, creating a report, or viewing real time information.

Periodic SNMP polling does have disadvantages. First, there is a delay between the time that an event occurs and the time that it is noticed (via polling) by the NMS. Second, there is a trade-off between polling frequency and bandwidth usage.

To mitigate these disadvantages, it is possible for SNMP agents to generate and send traps to inform the NMS immediately of certain events. Traps are unsolicited messages alerting the SNMP manager to a condition or event on the network. Examples of trap conditions include, but are not limited to, improper user authentication, restarts, link status (up or down), MAC address tracking, closing of a TCP connection, loss of connection to a neighbor, or other significant events. Trap-directed notifications reduce network and agent resources by eliminating the need for some of SNMP polling requests.

The figure illustrates the use of an SNMP trap to alert the network administrator that interface G0/0/0 has failed. The NMS software can send the network administrator a text message, pop up a window on the NMS software, or turn the router icon red in the NMS GUI.

The exchange of all SNMP messages is illustrated in the figure.
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736581095000sxssu2.png)
### SNMP Versions
There are several versions of SNMP:

- **SNMPv1** - This is the Simple Network Management Protocol, a Full Internet Standard, that is defined in RFC 1157.
- **SNMPv2c** - This is defined in RFCs 1901 to 1908. It uses a community-string-based Administrative Framework.
- **SNMPv3** - This is an interoperable standards-based protocol originally defined in RFCs 2273 to 2275. It provides secure access to devices by authenticating and encrypting packets over the network. It includes these security features: message integrity to ensure that a packet was not tampered with in transit, authentication to determine that the message is from a valid source, and encryption to prevent the contents of a message from being read by an unauthorized source.

All versions use SNMP managers, agents, and MIBs. Cisco IOS software supports the above three versions. Version 1 is a legacy solution and is not often encountered in networks today; therefore, this course focuses on versions 2c and 3.

Both SNMPv1 and SNMPv2c use a community-based form of security. The community of managers that is able to access the MIB of the agent is defined by a community string.

Unlike SNMPv1, SNMPv2c includes a bulk retrieval mechanism and more detailed error message reporting to management stations. The bulk retrieval mechanism retrieves tables and large quantities of information, minimizing the number of round-trips required. The SNMPv2c improved error-handling includes expanded error codes that distinguish different kinds of error conditions. These conditions are reported through a single error code in SNMPv1. Error return codes in SNMPv2c include the error type.

**Note:** SNMPv1 and SNMPv2c offer minimal security features. Specifically, SNMPv1 and SNMPv2c can neither authenticate the source of a management message nor provide encryption. SNMPv3 is most currently described in RFCs 3410 to 3415. It adds methods to ensure the secure transmission of critical data between managed devices.

SNMPv3 provides for both security models and security levels. A security model is an authentication strategy set up for a user and the group within which the user resides. A security level is the permitted level of security within a security model. A combination of the security level and the security model determine which security mechanism is used when handling an SNMP packet. Available security models are SNMPv1, SNMPv2c, and SNMPv3.

A network administrator must configure the SNMP agent to use the SNMP version supported by the management station. Because an agent can communicate with multiple SNMP managers, it is possible to configure the software to support communications by using SNMPv1, SNMPv2c, or SNMPv3.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736581699000ds7i7n.png)

### Community Strings
For SNMP to operate, the NMS must have access to the MIB. To ensure that access requests are valid, some form of authentication must be in place.

SNMPv1 and SNMPv2c use community strings that control access to the MIB. Community strings are plaintext passwords. SNMP community strings authenticate access to MIB objects.

There are two types of community strings:

- **Read-only (ro)** - This type provides access to the MIB variables, but does not allow these variables to be changed, only read. Because security is minimal in version 2c, many organizations use SNMPv2c in read-only mode.
- **Read-write (rw)** - This type provides read and write access to all objects in the MIB.

To view or set MIB variables, the user must specify the appropriate community string for read or write access.

How it works?
1. SNMP management station sends request to Agent with the community string (get 192.168.1.10 2#B7!9)
2. Agent checks if the community string matches and the ip address matches then sends back the statistics.

### MIB Object ID
The MIB organizes variables hierarchically. MIB variables enable the management software to monitor and control the network device. Formally, the MIB defines each variable as an object ID (OID). OIDs uniquely identify managed objects in the MIB hierarchy. The MIB organizes the OIDs based on RFC standards into a hierarchy of OIDs, usually shown as a tree.

The MIB tree for any given device includes some branches with variables common to many networking devices and some branches with variables specific to that device or vendor.

RFCs define some common public variables. Most devices implement these MIB variables. In addition, networking equipment vendors, like Cisco, can define their own private branches of the tree to accommodate new variables specific to their devices.

The figure shows portions of the MIB structure defined by Cisco. Note how the OID can be described in words or numbers to help locate a particular variable in the tree. OIDs belonging to Cisco, are numbered as follows: .iso (1).org (3).dod (6).internet (1).private (4).enterprises (1).cisco (9). Therefore, the OID is 1.3.6.1.4.1.9.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17365824230002ogyx6.png)

### SNMP Polling Scenario
SNMP can be used is to observe CPU utilization over a period of time by polling devices. CPU statistics can then be compiled on the NMS and graphed. This creates a baseline for the network administrator. Threshold values can then be set relative to this baseline. When CPU utilization exceeds this threshold, notifications are sent. The figure illustrates 5-minute samples of router CPU utilization over the period of a few weeks.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736582489000z9byrs.png)

The data is retrieved via the snmpget utility, issued on the NMS. Using the snmpget utility, you can manually retrieve real-time data, or have the NMS run a report. This report would give you a period of time that you could use the data to get the average. The snmpget utility requires that the SNMP version, the correct community, the IP address of the network device to query, and the OID number are set. The figure demonstrates the use of the freeware snmpget utility, which allows quick retrieval of information from the MIB.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736582549000r0tkxo.png)

The snmpget utility command has several parameters, including:

- -v2c - This is the version of SNMP.
- -c community - This is the SNMP password, called a community string.
- 10.250.250.14 - This is the IP address of the monitored device.
- 1.3.6.1.4.1.9.2.1.58.0 - This is the OID of MIB variable.

The last line shows the response. The output shows a shortened version of the MIB variable. It then lists the actual value in the MIB location. In this case, the 5-minute moving average of the CPU busy percentage is 11 percent.

### SNMP Object Navigator
The snmpget utility gives some insight into the basic mechanics of how SNMP works. However, working with long MIB variable names like 1.3.6.1.4.1.9.2.1.58.0 can be problematic for the average user. More commonly, the network operations staff uses a network management product with an easy-to-use GUI, which makes the entire MIB data variable naming transparent to the user.

Search for “Cisco SNMP Object Navigator tool” to find the Cisco tool that allows a network administrator to research details about a particular OID. The figure displays an example of using the navigator to research the OID information for the **whyReload** object.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736582650000fgmlmk.png)

## SYSLOG
On Cisco network devices, the syslog protocol starts by sending system messages and **debug** output to a local logging process that is internal to the device. How the logging process manages these messages and outputs is based on device configurations. For example, syslog messages may be sent across the network to an external syslog server. These messages can be retrieved without needing to access the actual device. Log messages and outputs stored on the external server can be pulled into various reports for easier reading.

Alternatively, syslog messages may be sent to an internal buffer. Messages sent to the internal buffer are only viewable through the CLI of the device.

Finally, the network administrator may specify that only certain types of system messages be sent to various destinations. For example, the device may be configured to forward all system messages to an external syslog server. However, debug-level messages are forwarded to the internal buffer and are only accessible by the administrator from the CLI.

As shown in the figure, popular destinations for syslog messages include the following:

- Logging buffer (RAM inside a router or switch)
- Console line
- Terminal line
- Syslog server

It is possible to remotely monitor system messages by viewing the logs on a syslog server, or by accessing the device through Telnet, SSH, or through the console port.

### Syslog message formats
The smaller numerical levels are the more critical syslog alarms. The severity level of the messages can be set to control where each type of message is displayed (i.e. on the console or the other destinations). The complete list of syslog levels is shown in the table.
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736583533000ygygpg.png)

Each syslog level has its own meaning:

- **Warning Level 4 - Emergency Level 0:** These messages are error messages about software or hardware malfunctions; these types of messages mean that the functionality of the device is affected. The severity of the issue determines the actual syslog level applied.
- **Notification Level 5:** This notifications level is for normal, but significant events. For example, interface up or down transitions, and system restart messages are displayed at the notifications level.
- **Informational Level 6:** This is a normal information message that does not affect device functionality. For example, when a Cisco device is booting, you might see the following informational message: %LICENSE-6-EULA_ACCEPT_ALL: The Right to Use End User License Agreement is accepted.
- **Debugging Level 7:** This level indicates that the messages are output generated from issuing various **debug** commands.

### Syslog Facilities
In addition to specifying the severity, syslog messages also contain information on the facility. Syslog facilities are service identifiers that identify and categorize system state data for error and event message reporting. The logging facility options that are available are specific to the networking device. For example, Cisco 2960 Series switches running Cisco IOS Release 15.0(2) and Cisco 1941 routers running Cisco IOS Release 15.2(4) support 24 facility options that are categorized into 12 facility types.

Some common syslog message facility codes reported on Cisco IOS routers include:

- **IF** - Identifies that the syslog message was generated by an interface.
- **IP** - Identifies that the syslog message was generated by IP.
- **OSPF** - Identifies that the syslog message was generated by the OSPF routing protocol.
- **SYS** - Identifies that the syslog message was generated by the device operating system.
- **IPSEC** - Identifies that the syslog message was generated by the IP Security encryption protocol.

By default, the format of syslog messages on the Cisco IOS Software is as follows:

```
%facility-severity-MNEMONIC: description
```

Here's an example you always see
```
%LINK-3-UPDOWN: Interface Port-channel1, changed state to up
```

If ACL logging is configured, the device generates syslog messages when packets match a parameter condition.

### Configure Syslog Timestamp
By default, log messages are not timestamped. In the example, the R1 GigabitEthernet 0/0/0 interface is shutdown. The message logged to the console does not identify when the interface state was changed. Log messages should be timestamped so that when they are sent to another destination, such as a Syslog server, there is record of when the message was generated.

Use the command **service timestamps log datetime** to force logged events to display the date and time. As shown in the command output, when the R1 GigabitEthernet 0/0/0 interface is reactivated, the log messages now contain the date and time.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736583812000m0y04q.png)

## Router and Switch File Maintanence
