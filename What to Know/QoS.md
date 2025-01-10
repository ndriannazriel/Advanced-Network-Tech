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
