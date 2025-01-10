## Prioritizing Traffic
Congestion occurs when multiple communication lines aggregate onto a single device such as a router, and then much of that data is placed on just a few outbound interfaces, or onto a slower interface. Congestion can also occur when large data packets prevent smaller packets from being transmitted in a timely manner.

When the volume of traffic is greater than what can be transported across the network, devices queue (hold) the packets in memory until resources become available to transmit them. Queuing packets causes delay because new packets cannot be transmitted until previous packets have been processed. If the number of packets to be queued continues to increase, the memory within the device fills up and packets are dropped. One QoS technique that can help with this problem is to classify data into multiple queues, as shown in the figure.

**Note:** A device implements QoS only when it is experiencing some type of congestion.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17365123190008c2vhw.png)

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736512389000ynadoc.png)

Delay or latency refers to the time it takes for a packet to travel from the source to the destination. Two types of delays are fixed and variable. A fixed delay is a specific amount of time a specific process takes, such as how long it takes to place a bit on the transmission media. A variable delay takes an unspecified amount of time and is affected by factors such as how much traffic is being processed.

Jitter is the variation in the delay of received packets. At the sending side, packets are sent in a continuous stream with the packets spaced evenly apart. Due to network congestion, improper queuing, or configuration errors, the delay between each packet can vary instead of remaining constant. Both delay and jitter need to be controlled and minimized to support real-time and interactive traffic.

##### Sources of Delay
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736512486000g09xnd.png)

### Packet Loss
When a router receives a Real-Time Protocol (RTP) digital audio stream for Voice over IP (VoIP), it must compensate for the jitter that is encountered. The mechanism that handles this function is the playout delay buffer. The playout delay buffer must buffer these packets and then play them out in a steady stream, as shown in the figure. The digital packets are later converted back to an analog audio stream.

If the jitter is so large that it causes packets to be received out of the range of this buffer, the out-of-range packets are discarded and dropouts are heard in the audio, as shown in the figure.
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736512669000xh7jqm.png)

For losses as small as one packet, the digital signal processor (DSP) interpolates what it thinks the audio should be and no problem is audible to the user. However, when jitter exceeds what the DSP can do to make up for the missing packets, audio problems are heard.

Packet loss is a very common cause of voice quality problems on an IP network. In a properly designed network, packet loss should be near zero. The voice codecs used by the DSP can tolerate some degree of packet loss without a dramatic effect on voice quality. Network engineers use QoS mechanisms to classify voice packets for zero packet loss. Bandwidth is guaranteed for the voice calls by giving priority to voice traffic over traffic that is not sensitive to delays.

## Traffic Characteristics
Voice and Video traffic are most of the times the reason QoS is needed due to their characteristics.

### Voice Traffic
Voice traffic is predictable and smooth, as shown in the figure. However, voice is very sensitive to delays and dropped packets. It makes no sense to re-transmit voice if packets are lost; therefore, voice packets must receive a higher priority than other types of traffic. For example, Cisco products use the RTP port range 16384 to 32767 to prioritize voice traffic. Voice can tolerate a certain amount of latency, jitter, and loss without any noticeable effects. 

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17365132560008gjs9j.png)

### Video Traffic
Without QoS and a significant amount of extra bandwidth capacity, video quality typically degrades. The picture appears blurry, jagged, or in slow motion. The audio portion of the feed may become unsynchronized with the video.

Video traffic tends to be unpredictable, inconsistent, and bursty compared to voice traffic. Compared to voice, video is less resilient to loss and has a higher volume of data per packet. Notice in the figure how voice packets arrive every 20 ms and are a predictable 200 bytes each.

UDP ports such as 554, are used for the Real-Time Streaming Protocol (RSTP) and should be given priority over other, less delay-sensitive, network traffic. Similar to voice, video can tolerate a certain amount of latency, jitter, and loss without any noticeable effects.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736513323000xeaktq.png)

### Data Traffic 
Most applications use either TCP or UDP. Unlike UDP, TCP performs error recovery. Data applications that have no tolerance for data loss, such as email and web pages, use TCP to ensure that, if packets are lost in transit, they will be resent. Data traffic can be smooth or bursty. Network control traffic is usually smooth and predictable. When there is a topology change, the network control traffic may burst for a few seconds. But the capacity of today’s networks can easily handle the increase in network control traffic as the network converges.

However, some TCP applications can consume a large portion of network capacity. FTP will consume as much bandwidth as it can get when you download a large file, such as a movie or game. The table summarizes data traffic characteristics.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736513400000r9vk2j.png)

Although data traffic is relatively insensitive to drops and delays compared to voice and video, a network administrator still needs to consider the quality of the user experience, sometimes referred to as Quality of Experience or QoE. There are two main factors that a network administrator needs to ask about the flow of data traffic:

- Does the data come from an interactive application?
- Is the data mission critical?

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736513438000plpmqn.png)

## Queuing Algorithms
Queuing is a congestion management tool that can buffer, prioritize, and, if required, reorder packets before being transmitted to the destination.

A number of queuing algorithms are available. For the purposes of this course, we will focus on the following:

- First-In, First-Out (FIFO)
- Weighted Fair Queuing (WFQ)
- Class-Based Weighted Fair Queuing (CBWFQ)
- Low Latency Queuing (LLQ)

### FIFO
FIFO has no concept of priority or classes of traffic and consequently, makes no decision about packet priority. There is only one queue, and all packets are treated equally.

When FIFO is used, important or time-sensitive traffic can be dropped when there is congestion on the router or switch interface. When no other queuing strategies are configured, all interfaces, except serial interfaces at E1 (2.048 Mbps) and below, use FIFO by default. (Serial interfaces at E1 and below use WFQ by default.)

FIFO, which is the fastest method of queuing, is effective for large links that have little delay and minimal congestion. If your link has very little congestion, FIFO queuing may be the only queuing you need to use.

### Weighted Fair Queuing (WFQ)
Weighted Fair Queuing (WFQ) is an automated scheduling method that provides fair bandwidth allocation to all network traffic. WFQ does not allow classification options to be configured. WFQ applies priority, or weights, to identified traffic and classifies it into conversations or flows, as shown in the figure.
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736519042000nkov04.png)

WFQ then determines how much bandwidth each flow is allowed relative to other flows. The flow-based algorithm used by WFQ simultaneously schedules interactive traffic to the front of a queue to reduce response time. It then fairly shares the remaining bandwidth among high-bandwidth flows. WFQ allows you to give low-volume, interactive traffic, such as Telnet sessions and voice, priority over high-volume traffic, such as FTP sessions. When multiple file transfers flows are occurring simultaneously, the transfers are given comparable bandwidth.

WFQ classifies traffic into different flows based on packet header addressing, including such characteristics as source and destination IP addresses, MAC addresses, port numbers, protocol, and Type of Service (ToS) value. The ToS value in the IP header can be used to classify traffic.

Low-bandwidth traffic flows, which comprise the majority of traffic, receive preferential service which allows their entire offered loads to be sent in a timely fashion. High-volume traffic flows share the remaining capacity proportionally among themselves.

**Limitations**

WFQ is not supported with tunneling and encryption because these features modify the packet content information required by WFQ for classification.

Although WFQ automatically adapts to changing network traffic conditions, it does not offer the degree of precise control over bandwidth allocation that CBWFQ offers.

### Class-Based Weighted Fair Queuing (CBWFQ)
Class-Based Weighted Fair Queuing (CBWFQ) extends the standard WFQ functionality to provide support for user-defined traffic classes. With CBWFQ, you define traffic classes based on match criteria including protocols, access control lists (ACLs), and input interfaces. Packets satisfying the match criteria for a class constitute the traffic for that class. A FIFO queue is reserved for each class, and traffic belonging to a class is directed to the queue for that class, as shown in the figure.

When a class has been defined according to its match criteria, you can assign it characteristics. To characterize a class, you assign it bandwidth, weight, and maximum packet limit. The bandwidth assigned to a class is the guaranteed bandwidth delivered to the class during congestion.

To characterize a class, you also specify the queue limit for that class, which is the maximum number of packets allowed to accumulate in the queue for the class. Packets belonging to a class are subject to the bandwidth and queue limits that characterize the class.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736519113000jd5pw8.png)

After a queue has reached its configured queue limit, adding more packets to the class causes tail drop or packet drop to take effect, depending on how class policy is configured. Tail drop means a router simply discards any packet that arrives at the tail end of a queue that has completely used up its packet-holding resources. This is the default queuing response to congestion. Tail drop treats all traffic equally and does not differentiate between classes of service.

### Low Latency Queuing (LLQ)
The Low Latency Queuing (LLQ) feature brings strict priority queuing (PQ) to CBWFQ. Strict PQ allows delay-sensitive packets such as voice to be sent before packets in other queues. LLQ provides strict priority queuing for CBWFQ, reducing jitter in voice conversations, as shown in the figure.

Without LLQ, CBWFQ provides WFQ based on defined classes with no strict priority queue available for real-time traffic. The weight for a packet belonging to a specific class is derived from the bandwidth you assigned to the class when you configured it. Therefore, the bandwidth assigned to the packets of a class determines the order in which packets are sent. All packets are serviced fairly based on weight; no class of packets may be granted strict priority. This scheme poses problems for voice traffic that is largely intolerant of delay, especially variation in delay. For voice traffic, variations in delay introduce irregularities of transmission manifesting as jitter in the heard conversation.

LLQ allows delay-sensitive packets such as voice to be sent first (before packets in other queues), giving delay-sensitive packets preferential treatment over other traffic. Although it is possible to classify various types of real-time traffic to the strict priority queue, Cisco recommends that only voice traffic be directed to the priority queue.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736519160000v3qsxy.png)

## QoS Models
How can QoS be implemented in a network? There are three models for implementing QoS:

- Best-effort model
- Integrated services (IntServ)
- Differentiated services (DiffServ)

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17365196620004sajhl.png)

### Best Effort
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17365197060000rcv6l.png)

### Integrated Services
The IntServ architecture model (RFC 1633, 2211, and 2212) was developed in 1994 to meet the needs of real-time applications, such as remote video, multimedia conferencing, data visualization applications, and virtual reality. IntServ is a multiple-service model that can accommodate many QoS requirements.

IntServ delivers the end-to-end QoS that real-time applications require. IntServ explicitly manages network resources to provide QoS to individual flows or streams, sometimes called microflows. It uses resource reservation and admission-control mechanisms as building blocks to establish and maintain QoS. This is similar to a concept known as “hard QoS.” Hard QoS guarantees traffic characteristics, such as bandwidth, delay, and packet-loss rates, from end to end. Hard QoS ensures both predictable and guaranteed service levels for mission-critical applications.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images173651979300091y1ht.png)

IntServ uses a connection-oriented approach inherited from telephony network design. Each individual communication must explicitly specify its traffic descriptor and requested resources to the network. The edge router performs admission control to ensure that available resources are sufficient in the network. The IntServ standard assumes that routers along a path set and maintain the state for each individual communication.

In the IntServ model, the application requests a specific kind of service from the network before sending data. The application informs the network of its traffic profile and requests a particular kind of service that can encompass its bandwidth and delay requirements. IntServ uses the Resource Reservation Protocol (RSVP) to signal the QoS needs of an application’s traffic along devices in the end-to-end path through the network. If network devices along the path can reserve the necessary bandwidth, the originating application can begin transmitting. If the requested reservation fails along the path, the originating application does not send any data.

The edge router performs admission control based on information from the application and available network resources. The network commits to meeting the QoS requirements of the application as long as the traffic remains within the profile specifications. The network fulfills its commitment by maintaining the per-flow state and then performing packet classification, policing, and intelligent queuing based on that state.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736519845000fr9amt.png)

### Differentiated Services
The differentiated services (DiffServ) QoS model specifies a simple and scalable mechanism for classifying and managing network traffic. For example, DiffServ can provide low-latency guaranteed service to critical network traffic such as voice or video, while providing simple best-effort traffic guarantees to non-critical services such as web traffic or file transfers.

The DiffServ design overcomes the limitations of both the best-effort and IntServ models. The DiffServ model is described in RFCs 2474, 2597, 2598, 3246, 4594. DiffServ can provide an “almost guaranteed” QoS while still being cost-effective and scalable.

The DiffServ model is similar in concept to sending a package using a delivery service. You request (and pay for) a level of service when you send a package. Throughout the package network, the level of service you paid for is recognized and your package is given either preferential or normal service, depending on what you requested.

DiffServ is not an end-to-end QoS strategy because it cannot enforce end-to-end guarantees. However, DiffServ QoS is a more scalable approach to implementing QoS. Unlike IntServ and hard QoS, in which the end-hosts signal their QoS needs to the network, DiffServ does not use signaling. Instead, DiffServ uses a “soft QoS” approach. It works on the provisioned-QoS model, where network elements are set up to service multiple classes of traffic each with varying QoS requirements.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17365199130001l62u2.png)

As a host forwards traffic to a router, the router classifies the flows into aggregates (classes) and provides the appropriate QoS policy for the classes. DiffServ enforces and applies QoS mechanisms on a hop-by-hop basis, uniformly applying global meaning to each traffic class to provide both flexibility and scalability. For example, DiffServ could be configured to group all TCP flows as a single class, and allocate bandwidth for that class, rather than for the individual flows as IntServ would do. In addition to classifying traffic, DiffServ minimizes signaling and state maintenance requirements on each network node.

Specifically, DiffServ divides network traffic into classes based on business requirements. Each of the classes can then be assigned a different level of service. As the packets traverse a network, each of the network devices identifies the packet class and services the packet according to that class. It is possible to choose many levels of service with DiffServ. For example, voice traffic from IP phones is usually given preferential treatment over all other application traffic, email is generally given best-effort service, and nonbusiness traffic can either be given very poor service or blocked entirely.

**Note:** Modern networks primarily use the DiffServ model. However, due to the increasing volumes of delay- and jitter-sensitive traffic, IntServ and RSVP are sometimes **co-deployed.**

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736519965000qgp234.png)

## QoS Implementation Techniques

### Avoiding Packet Loss
 Packet loss is usually the result of congestion on an interface. Most applications that use TCP experience slowdown because TCP automatically adjusts to network congestion. Dropped TCP segments cause TCP sessions to reduce their window sizes. Some applications do not use TCP and cannot handle drops (fragile flows).

The following approaches can prevent drops in sensitive applications:

- Increase link capacity to ease or prevent congestion.
- Guarantee enough bandwidth and increase buffer space to accommodate bursts of traffic from fragile flows. WFQ, CBWFQ, and LLQ can guarantee bandwidth and provide prioritized forwarding to drop-sensitive applications.
- Drop lower-priority packets before congestion occurs. Cisco IOS QoS provides queuing mechanisms, such as weighted random early detection (WRED), that start dropping lower-priority packets before congestion occurs.

### QoS Tools
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736521576000cis59p.png)

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736521604000inie6b.png)

### Classification and Marking
Before a packet can have a QoS policy applied to it, the packet has to be classified. Classification and marking allows us to identify or “mark” types of packets. Classification determines the class of traffic to which packets or frames belong. Only after traffic is marked can policies be applied to it.

How a packet is classified depends on the QoS implementation. Methods of classifying traffic flows at Layer 2 and 3 include using interfaces, ACLs, and class maps. Traffic can also be classified at Layers 4 to 7 using Network Based Application Recognition (NBAR).

**Note:** NBAR is a classification and protocol discovery feature of Cisco IOS software that works with QoS features. NBAR is out of scope for this course.

Marking means that we are adding a value to the packet header. Devices receiving the packet look at the marked field to see if it matches a defined policy. Marking should be done as close to the source device as possible. This establishes the trust boundary.

How traffic is marked usually depends on the technology. The table in the figure describes some the marking fields used in various technologies. The decision of whether to mark traffic at Layers 2 or 3 (or both) is not trivial and should be made after consideration of the following points:

- Layer 2 marking of frames can be performed for non-IP traffic.
- Layer 2 marking of frames is the only QoS option available for switches that are not “IP aware”.
- Layer 3 marking will carry the QoS information end-to-end.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17365216730000yt873.png)

### Marking at Layer 2
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736521762000w0jjzs.png)

### Marking at Layer 3
IPv4 and IPv6 specify an 8-bit field in their packet headers to mark packets. As shown in the figure, both IPv4 and IPv6 support an 8-bit field for marking: the Type of Service (ToS) field for IPv4 and the Traffic Class field for IPv6.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736521853000r8xh23.png)

### Type of Service and Traffic Class Field
The Type of Service (IPv4) and Traffic Class (IPv6) carry the packet marking as assigned by the QoS classification tools. The field is then referred to by receiving devices which forward the packets based on the appropriate assigned QoS policy.

The figure displays the contents of the 8-bit field. In RFC 791, the original IP standard specified the IP Precedence (IPP) field to be used for QoS markings. However, in practice, these three bits did not provide enough granularity to implement QoS.

RFC 2474 supersedes RFC 791 and redefines the ToS field by renaming and extending the IPP field. The new field, as shown in the figure, has 6-bits allocated for QoS. Called the Differentiated Services Code Point (DSCP) field, these six bits offer a maximum of 64 possible classes of service. The remaining two IP Explicit Congestion Notification (ECN) bits can be used by ECN-aware routers to mark packets instead of dropping them. The ECN marking informs downstream routers that there is congestion in the packet flow.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736521913000an4wrq.png)

### DSCP Values
The 64 DSCP values are organized into three categories:

- **Best-Effort (BE)** - This is the default for all IP packets. The DSCP value is 0. The per-hop behavior is normal routing. When a router experiences congestion, these packets will be dropped. No QoS plan is implemented.
- **Expedited Forwarding (EF)** - RFC 3246 defines EF as the DSCP decimal value 46 (binary 101110). The first 3 bits (101) map directly to the Layer 2 CoS value 5 used for voice traffic. At Layer 3, Cisco recommends that EF only be used to mark voice packets.
- **Assured Forwarding (AF)** - RFC 2597 defines AF to use the 5 most significant DSCP bits to indicate queues and drop preference. The definition of AF is illustrated in the figure.

##### Assured Forwarding Values
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17365219740000zajn9.png)

The **AFxy** formula is specified as follows:

- The first 3 most significant bits are used to designate the class. Class 4 is the best queue and Class 1 is the worst queue.
- The 4th and 5th most significant bits are used to designate the drop preference.
- The 6th most significant bit is set to zero.

For example, AF32 belongs to class 3 (binary 011) and has a medium drop preference (binary 10). The full DSCP value is 28 because you include the 6th 0 bit (binary 011100).

### Class Selector Bits
Because the first 3 most significant bits of the DSCP field indicate the class, these bits are also called the Class Selector (CS) bits. These 3 bits map directly to the 3 bits of the CoS field and the IPP field to maintain compatibility with 802.1p and RFC 791, as shown in the figure.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736522053000h2pjac.png)

The table in the figure shows how the CoS values map to the Class Selectors and the corresponding DSCP 6-bit value. This same table can be used to map IPP values to the Class Selectors.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17365220860006tkaep.png)

### Trust Boundaries
Where should markings occur? Traffic should be classified and marked as close to its source as technically and administratively feasible. This defines the trust boundary, as shown in the figure.

1. Trusted endpoints have the capabilities and intelligence to mark application traffic to the appropriate Layer 2 CoS and/or Layer 3 DSCP values. Examples of trusted endpoints include IP phones, wireless access points, videoconferencing gateways and systems, IP conferencing stations, and more.
2. Secure endpoints can have traffic marked at the Layer 2 switch.
3. Traffic can also be marked at Layer 3 switches / routers.

Re-marking traffic, for example, re-marking CoS values to IP Precedent or DSCP values, is typically necessary.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17365221360004i3b3f.png)

### Congestion Avoidance
Congestion management includes queuing and scheduling methods where excess traffic is buffered or queued (and sometimes dropped) while it waits to be sent out an egress interface. Congestion avoidance tools are simpler. They monitor network traffic loads in an effort to anticipate and avoid congestion at common network and internetwork bottlenecks before congestion becomes a problem. These tools can monitor the average depth of the queue, as represented in the figure. When the queue is below the minimum threshold, there are no drops. As the queue fills up to the maximum threshold, a small percentage of packets are dropped. When the maximum threshold is passed, all packets are dropped.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736522171000wfujqh.png)

Some congestion avoidance techniques provide preferential treatment for which packets will get dropped. For example, Cisco IOS QoS includes weighted random early detection (WRED) as a possible congestion avoidance solution. The WRED algorithm allows for congestion avoidance on network interfaces by providing buffer management and allowing TCP traffic to decrease, or throttle back, before buffers are exhausted. Using WRED helps avoid tail drops and maximizes network use and TCP-based application performance. There is no congestion avoidance for User Datagram Protocol (UDP)-based traffic, such as voice traffic. In case of UDP-based traffic, methods such as queuing and compression techniques help to reduce and even prevent UDP packet loss.


