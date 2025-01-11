## Network Documentation

Common network documentation includes the following:

- Physical and logical network topology diagrams
- Network device documentation that records all pertinent device information
- Network performance baseline documentation

All network documentation should be kept in a single location, either as hard copy or on the network on a protected server. Backup documentation should be maintained and kept in a separate location.

##### Physical Topology
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17365944960009j79u5.png)

##### Logical IPv4 and IPv6 Topology
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736594553000sxpw7p.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736594569000l3kclo.png)

### Network Device Documentation]
Network device documentation should contain accurate, up-to-date records of the network hardware and software. Documentation should include all pertinent information about the network devices.

Many organizations create documents with tables or spreadsheets to capture relevant device information.
##### Router Documentation
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736594637000us29r4.png)

##### LAN Switch Documentation
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736594695000r6m7d5.png)

##### End System Documentation Files
End-system documentation focuses on the hardware and software used in servers, network management consoles, and user workstations. An incorrectly configured end-system can have a negative impact on the overall performance of a network. For this reason, having access to end-system device documentation can be very useful when troubleshooting.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736594741000zgmf2b.png)

### Establish a Network Baseline
A network baseline should answer the following questions:

- How does the network perform during a normal or average day?
- Where are the most errors occurring?
- What part of the network is most heavily used?
- What part of the network is least used?
- Which devices should be monitored and what alert thresholds should be set?
- Can the network meet the identified policies?

Steps
1. Determine What Types of Data to Collect
2. Identify Devices and Ports of Interest
3. Determine the Baseline Duration

## Troubleshooting Process
The figure displays a more detailed seven-step troubleshooting process. Notice how some steps interconnect. This is because, some technicians may be able to jump between steps based on their level of experience.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736594963000fneclp.png)

### Structured Troubleshooting Methods

1. Bottom-Up
2. Top-Down
3. Divide and Conquer
4. Follow the path
5. Substitution
6. Comparison
7. Educated Guess

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images173659511700016w418.png)

## Symptoms and Causes of Network Problems
### Physical Layer 
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736602239000mnuxxr.png)

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736602329000arzy0q.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images173660234200057cco9.png)

### Data Link Layer
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736602280000kn9m8s.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17366023760007elc9c.png)

### Network Layer
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736602414000lj5b35.png)

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736602435000ouvlts.png)

### Transport Layer(ACL)
Network problems can arise from transport layer problems on the router, particularly at the edge of the network where traffic is examined and modified. For instance, both access control lists (ACLs) and Network Address Translation (NAT) operate at the network layer and may involve operations at the transport layer, as shown in the figure.
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736602496000hhbvdn.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17366025140006f3waj.png)

### Transport Layer (NAT)
There are several problems with NAT, such as not interacting with services like DHCP and tunneling. These can include misconfigured NAT inside, NAT outside, or ACLs. Other issues include interoperability with other network technologies, especially those that contain or derive information from host network addressing in the packet.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736602629000cksqfo.png)

### Application Layer
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736602693000p57sov.png)
The types of symptoms and causes depend upon the actual application itself.

Application layer problems prevent services from being provided to application programs. A problem at the application layer can result in unreachable or unusable resources when the physical, data link, network, and transport layers are functional. It is possible to have full network connectivity, but the application simply cannot provide data.

Another type of problem at the application layer occurs when the physical, data link, network, and transport layers are functional, but the data transfer and requests for network services from a single network service or application do not meet the normal expectations of a user.

A problem at the application layer may cause users to complain that the network or an application that they are working with is sluggish or slower than usual when transferring data or requesting network services.
