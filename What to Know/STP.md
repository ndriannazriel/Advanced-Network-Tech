Spanning Tree Protocol (STP) is a loop-prevention network protocol that allows for redundancy while creating a loop-free Layer 2 topology. IEEE 802.1D is the original IEEE MAC Bridging standard for STP.

Unlike the Layer 3 protocols, IPv4 and IPv6, Layer 2 Ethernet does not include a mechanism to recognize and eliminate endlessly looping frames. Both IPv4 and IPv6 include a mechanism that limits the number of times a Layer 3 networking device can retransmit a packet. A router will decrement the TTL (Time to Live) in every IPv4 packet, and the Hop Limit field in every IPv6 packet. When these fields are decremented to 0, a router will drop the packet. Ethernet and Ethernet switches have no comparable mechanism for limiting the number of times a switch retransmits a Layer 2 frame. STP was developed specifically as a loop prevention mechanism for Layer 2 Ethernet.

## Spanning Tree Algorithm(STA)

1. Elect the root bridge.
2. Elect the root ports.
3. Elect designated ports.
4. Elect alternate (blocked) ports.

During STA and STP functions, switches use Bridge Protocol Data Units (BPDUs) to share information about themselves and their connections. BPDUs are used to elect the root bridge, root ports, designated ports, and alternate ports. Each BPDU contains a bridge ID (BID) that identifies which switch sent the BPDU. The BID is involved in making many of the STA decisions including root bridge and port roles. As shown in the figure, the BID contains a priority value, an extended system ID, and the MAC address of the switch. The lowest BID value is determined by the combination of these three fields.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17336287070006ep0jg.png)

**Designated Port When There is a Root Port**

If one end of a segment is a root port, then the other end is a designated port. To demonstrate this, the figure shows that switch S4 is connected to S3. The Fa0/1 interface on S4 is its root port because it has the best and only path to the root bridge. The Fa0/3 interface on S3 at the other end of the segment would therefore, be the designated port.

**Note:** All switch ports with end devices (hosts) attached are designated ports.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733628885000nbd5lb.png)


**Designated Port When There is No Root Port**

This leaves only segments between two switches where neither of the switches is the root bridge. In this case, the port on the switch with the least-cost path to the root bridge is the designated port for the segment. For example, in the figure, the last segment is the one between S2 and S3. Both S2 and S3 have the same path cost to the root bridge. The spanning tree algorithm will use the bridge ID as a tie breaker. Although not shown in the figure, S2 has a lower BID. Therefore, the F0/2 port of S2 will be chosen as the designated port. Designated ports are in forwarding state.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733628916000c57f9j.png)

#### Port states in STP
STP convergence requires three timers, as follows:

- **Hello Timer** -The hello time is the interval between BPDUs. The default is 2 seconds but can be modified to between 1 and 10 seconds.
- **Forward Delay Timer** -The forward delay is the time that is spent in the listening and learning state. The default is 15 seconds but can be modified to between 4 and 30 seconds.
- **Max Age Timer** -The max age is the maximum length of time that a switch waits before attempting to change the STP topology. The default is 20 seconds but be modified to between 6 and 40 seconds.

**Note:** The default times can be changed on the root bridge, which dictates the value of these timers for the STP domain.****

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733628964000u65mie.png)
