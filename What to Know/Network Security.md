Networks are susceptible to the following types of attacks:

- Reconnaissance Attacks - Gathering information
- Access Attacks - Exploit vulnerabilities in auth services, FTP services and web services
- DoS Attacks - Creates disruption of network services to users, devices and applications.

## Recon Attacks
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736398332000kjf6tq.png)


![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736398610000ebzzdo.png)

After performing a ping sweep to see the active addresses, a threat actor performs port scans using NMAP for example to get the services that are active.

### Access Attacks
**Password Attacks**
In a password attack, the threat actor attempts to discover critical system passwords using various methods. Password attacks are very common and can be launched using a variety of password cracking tools.

**Spoofing Attacks**
In spoofing attacks, the threat actor device attempts to pose as another device by falsifying data. Common spoofing attacks include IP spoofing, MAC spoofing, and DHCP spoofing. These spoofing attacks will be discussed in more detail later in this module

Other Access attacks include:

- Trust exploitations
- Port redirections
- Man-in-the-middle attacks
- Buffer overflow attacks

## IP Vulnerabilities and Threats

### Amplification and Reflection Attacks
1. Send a packet to all devices in a network and the source ip being the targeted host(Spoofed IP address)
2. When ICMP reply, the ping will come back to the targeted host from all the devices.
3. DoS attack happens.

### Address Spoofing Attacks

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17364005430003omtar.png)

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736400654000u6bsfw.png)

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736400667000jibqtr.png)

## TCP and UDP Vulnerabilities

While some attacks target IP, this topic discusses attacks that target TCP and UDP.

TCP segment information appears immediately after the IP header. The fields of the TCP segment and the flags for the Control Bits field are displayed in the figure.

The following are the six control bits of the TCP segment:

- **URG** - Urgent pointer field significant
- **ACK** - Acknowledgment field significant
- **PSH** - Push function
- **RST**- Reset the connection
- **SYN** - Synchronize sequence numbers
- **FIN** - No more data from sender

### TCP Attacks

Network applications use TCP or UDP ports. Threat actors conduct port scans of target devices to discover which services they offer.

**TCP SYN Flood Attack**
The TCP SYN Flood attack exploits the TCP three-way handshake. A threat actor continually sending TCP SYN session request packets with a randomly spoofed source IP address to a target. The target device replies with a TCP SYN-ACK packet to the spoofed IP address and waits for a TCP ACK packet. Those responses never arrive. Eventually the target host is overwhelmed with half-open TCP connections, and TCP services are denied to legitimate users.

1. The threat actor sends multiple SYN requests to a web server.
2. The web server replies with SYN-ACKs for each SYN request and waits to complete the three-way handshake. The threat actor does not respond to the SYN-ACKs.
3. A valid user cannot access the web server because the web server has too many half-opened TCP connections.

**TCP Reset Attack**
A TCP reset attack can be used to terminate TCP communications between two hosts. TCP can terminate a connection in a civilized (i.e., normal) manner and uncivilized (i.e., abrupt) manner.

The figure displays the civilized manner when TCP uses a four-way exchange consisting of a pair of FIN and ACK segments from each TCP endpoint to close the TCP connection.

The uncivilized manner is when a host receives an TCP segment with the RST bit set. This is an abrupt way to tear down the TCP connection and inform the receiving host to immediately stop using the TCP connection.

A threat actor could do a TCP reset attack and send a spoofed packet containing a TCP RST to one or both endpoints.

**Terminating a TCP Connection**
1. When the client has no more data to send in the stream, it sends a segment with the FIN flag set.
2. The server sends an ACK to acknowledge the receipt of the FIN to terminate the session from client to server.
3. The server sends a FIN to the client to terminate the server-to-client session.
4. The client responds with an ACK to acknowledge the FIN from the server.

**TCP Session Hijacking**
TCP session hijacking is another TCP vulnerability. Although difficult to conduct, a threat actor takes over an already-authenticated host as it communicates with the target. The threat actor must spoof the IP address of one host, predict the next sequence number, and send an ACK to the other host. If successful, the threat actor could send, but not receive, data from the target device.

### UDP Attacks

UDP is not protected by any encryption. You can add encryption to UDP, but it is not available by default. The lack of encryption means that anyone can see the traffic, change it, and send it on to its destination. Changing the data in the traffic will alter the 16-bit checksum, but the checksum is optional and is not always used. When the checksum is used, the threat actor can create a new checksum based on the new data payload, and then record it in the header as a new checksum. The destination device will find that the checksum matches the data without knowing that the data has been altered. This type of attack is not widely used.

**UDP Flood Attacks**
You are more likely to see a UDP flood attack. In a UDP flood attack, all the resources on a network are consumed. The threat actor must use a tool like UDP Unicorn or Low Orbit Ion Cannon. These tools send a flood of UDP packets, often from a spoofed host, to a server on the subnet. The program will sweep through all the known ports trying to find closed ports. This will cause the server to reply with an ICMP port unreachable message. Because there are many closed ports on the server, this creates a lot of traffic on the segment, which uses up most of the bandwidth. The result is very similar to a DoS attack.

## IP Services

Earlier in this module you learned about vulnerabilities with IP, TCP and UDP. The TCP/IP protocol suite was never built for security. Therefore, the services that IP uses for addressing functions such as ARP, DNS, and DHCP, are also not secure, as you will learn in this topic.

Any client can send an unsolicited ARP Reply called a “gratuitous ARP.” This is often done when a device first boots up to inform all other devices on the local network of the new device’s MAC address. When a host sends a gratuitous ARP, other hosts on the subnet store the MAC address and IP address contained in the gratuitous ARP in their ARP tables.

This feature of ARP also means that any host can claim to be the owner of any IP or MAC. A threat actor can poison the ARP cache of devices on the local network, creating an MITM attack to redirect traffic. The goal is to target a victim host, and have it change its default gateway to the threat actor’s device. This positions the threat actor in between the victim and all other systems outside of the local subnet.

### ARP Cache Poisoning

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736413231000fnlih6.png)

In the figure, the threat actor sends two spoofed gratuitous ARP Replies using its own MAC address for the indicated destination IP addresses. PC-A updates its ARP cache with its default gateway which is now pointing to the threat actor’s host MAC address. R1 also updates its ARP cache with the IP address of PC-A pointing to the threat actor’s MAC address.

The threat actor’s host is executing an ARP poisoning attack. The ARP poisoning attack can be passive or active. Passive ARP poisoning is where threat actors steal confidential information. Active ARP poisoning is where threat actors modify data in transit, or inject malicious data.

### ARP Spoofing

- **Goal:** Trick devices on a network to send data to the attacker instead of the intended destination.
- **How it Works:**
    1. The attacker sends **fake ARP replies** to devices on the network.
    2. These replies link the attacker's MAC address to the target's IP address.
    3. Devices update their ARP table with the wrong MAC address.
    4. Data meant for the target goes to the attacker, who can read, modify, or forward it.

It's like pretending to be someone else in a group chat so messages are sent to you instead of them.

What's the difference between ARP Cache Poisoning and ARP Spoofing?
ARP Cache Poisoning is the outcome of ARP spoofing and ARP spoofing is the process that leads to poisoning.

### DNS Attacks

Securing DNS is often overlooked. However, it is crucial to the operation of a network and should be secured accordingly.

DNS attacks include the following:
- DNS open resolver attacks
- DNS stealth attacks
- DNS domain shadowing attacks
- DNS tunneling attacks

=> Add more info here when you're free.
### DNS Tunneling

Threat actors who use DNS tunneling place non-DNS traffic within DNS traffic. This method often circumvents security solutions when a threat actor wishes to communicate with bots inside a protected network, or exfiltrate data from the organization, such as a password database. When the threat actor uses DNS tunneling, the different types of DNS records are altered. This is how DNS tunneling works for CnC commands sent to a botnet:

1. The command data is split into multiple encoded chunks.
2. Each chunk is placed into a lower level domain name label of the DNS query.
3. Because there is no response from the local or networked DNS for the query, the request is sent to the ISP’s recursive DNS servers.
4. The recursive DNS service will forward the query to the threat actor’s authoritative name server.
5. The process is repeated until all the queries containing the chunks of are sent.
6. When the threat actor’s authoritative name server receives the DNS queries from the infected devices, it sends responses for each DNS query, which contain the encapsulated, encoded CnC commands.
7. The malware on the compromised host recombines the chunks and executes the commands hidden within the DNS record.

To stop DNS tunneling, the network administrator must use a filter that inspects DNS traffic. Pay close attention to DNS queries that are longer than average, or those that have a suspicious domain name. DNS solutions, like Cisco OpenDNS, block much of the DNS tunneling traffic by identifying suspicious domains.

### DHCP Attacks

