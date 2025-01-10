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

