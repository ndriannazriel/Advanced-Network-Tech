OSPF is a link-state routing protocol that uses the concept of areas. A network administrator can divide the routing domain into distinct areas that help control routing update traffic.

### OSPF Packets
Routers running OSPF exchange messages to convey routing information using five types of packets. These packets, as shown in the figure, are as follows:

- Hello packet
- Database description packet
- Link-state request packet
- Link-state update packet
- Link-state acknowledgment packet

These packets are used to discover neighboring routers and also to exchange routing information to maintain accurate information about the network.

### OSPF Databases
OSPF messages are used to create and maintain three OSPF databases, as follows:

- **Adjacency database** - This creates the neighbor table.
- **Link-state database (LSDB)** - This creates the topology table.
- **Forwarding database** - This creates the routing table

### Algorithm
The router builds the topology table using results of calculations based on the Dijkstra shortest-path first (SPF) algorithm. The SPF algorithm is based on the cumulative cost to reach a destination.

The SPF algorithm creates an SPF tree by placing each router at the root of the tree and calculating the shortest path to each node. The SPF tree is then used to calculate the best routes. OSPF places the best routes into the forwarding database, which is used to make the routing table.

Diagram of how the SPF algorithm takes the costs of router links to create an SPF tree by placing each router at the root of the tree and calculating the shortest path to each node. The SPF tree is then used to calculate the best routes.

### Link State Operation 
To maintain routing information, OSPF routers complete a generic link-state routing process to reach a state of convergence. The figure shows a five router topology. Each link between routers is labeled with a cost value. In OSPF, cost is used to determine the best path to the destination. The following are the link-state routing steps that are completed by a router:

1. Establish Neighbor Adjacencies
2. Exchange Link-State Advertisements
3. Build the Link State Database/Topology table
4. Execute the SPF Algorithm
5. Choose the Best Route


| State    | Description                                                                                                                                           |
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| Down     | No hello packets received. Router sends hello packets and transitions into Init state.                                                                |
| Init     | Received hello packets with the router id of the sender then transition into 2 way state.                                                             |
| 2 way    | Bidirectional communication. If on multiaccess link, elect DR and BDR then transition to Exstart state.                                               |
| Exstart  | On p2p networks, the 2 routers decide who will decide who send the DBD packet first and then compare the DBD sequence number.                         |
| Exchange | Routers exchange DBD packets. If need additional info, transition to Loading otherwise transition to Full state.                                      |
| Loading  | LSRs and LSUs are used to gain additional information like a link change. Routes are processed using the SPF algorithm then transition to Full state. |
| Full     | Link state database(LSDB) fully synchronized.                                                                                                         |


### OSPF/Link State Packets
Link-state packets are the tools used by OSPF to help determine the fastest available route for a packet.
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733379753000p3sd8a.png)
The OSPF Type 1 packet is the Hello packet. Hello packets are used to do the following:

- Discover OSPF neighbors and establish neighbor adjacencies.
- Advertise parameters on which two routers must agree to become neighbors.
- Elect the Designated Router (DR) and Backup Designated Router (BDR) on multiaccess networks like Ethernet. Point-to-point links do not require DR or BDR.

Routers initially exchange Type 2 DBD packets, which is an abbreviated list of the LSDB of the sending router. It is used by receiving routers to check against the local LSDB.

A Type 3 LSR packet is used by the receiving routers to request more information about an entry in the DBD.

The Type 4 LSU packet is used to reply to an LSR packet.

A Type 5 packet is used to acknowledge the receipt of a Type 4 LSU.

LSUs are also used to forward OSPF routing updates, such as link changes. Specifically, an LSU packet can contain 11 different types of OSPFv2 LSAs. OSPFv3 renamed several of these LSAs and also contains two additional LSAs.

**Note:** The difference between the LSU and LSA terms can sometimes be confusing because these terms are often used interchangeably. However, an LSU contains one or more LSAs.
### Multi Area OSPF
With multiarea OSPF, one large routing domain can be divided into smaller areas, to support hierarchical routing. Routing still occurs between the areas (interarea routing), while many of the processor intensive routing operations, such as recalculating the database, are kept within an area.

For instance, any time a router receives new information about a topology change within the area (including the addition, deletion, or modification of a link) the router must rerun the SPF algorithm, create a new SPF tree, and update the routing table.

**Note:** Routers in other areas receive updates regarding topology changes, but these routers only update the routing table, not rerun the SPF algorithm.

--------------------------------------------------------------------------
## OSPF Configuration

Some things to note:
A 32-bit host route would not get advertised as a route to other OSPF routers.
When configuring ospf, make sure to choose either using the network command or the ip ospf command to avoid overlap or unnecessary redundancy.
Configuring a **passive interface** in OSPF stops OSPF **Hello packets** from being sent on that interface, preventing neighbor relationships from forming. However, the network on that interface is still advertised in OSPF.

OSPFv2
```
router ospf 1
router-id 1.1.1.1
passive-interface

int g0/1
ip ospf 1 area 0
```

OSPFv3
```
ipv6 unicast-routing

ipv6 router ospf 1
router-id 1.1.1.1
passive-interface

interface GigabitEthernet0/1                     
ipv6 ospf 1 area 0
```
##### Verify
```
!---It'll show where you configured passive interfaces on the router
show ip protocols
```
--------------------------------------------------------------------------
## Network Point-to-Point 

By default, Cisco routers elect a DR and BDR on Ethernet interfaces, even if there is only one other device on the link. You can verify this with the **show ip ospf interface** command.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736305178000zt0p5b.png)

The DR/ BDR election process is unnecessary as there can only be two routers on the point-to-point network between R1 and R2. Notice in the output that the router has designated the network type as BROADCAST.

This is because ;
OSPF forms a **full adjacency** directly between the two routers without needing a central coordinator (DR/BDR).
A DR/BDR is used in **multi-access networks** (e.g., Ethernet LAN) to reduce OSPF overhead by limiting the number of LSAs being advertised to the other neighbors.
DR/BDR functionality is critical for managing OSPF communication over **broadcast** networks, where routers communicate via multicast 224.0.0.5(meant for all ospf routers)
In a point-to-point link, OSPF packets are sent directly to the neighbor, bypassing the need for multicast coordination by a DR.

A DR/BDR is unnecessary in point-to-point networks because:

- OSPF adjacency is limited to two routers per link.
- OSPF communication is direct, not multicast.
- The topology inherently prevents adjacency overload.

Note to self: Refrain from configuring point to point ospf because it can cause issues with advertising the ospf routes to the other areas. Only configure if you have 2 routers.

##### Loopback and P2P
We use loopbacks to provide additional interfaces for a variety of purposes. In this case, we are using loopbacks to simulate more networks than the equipment can support. By default, loopback interfaces are advertised as /32 host routes. For example, R1 would advertise the 10.10.1.0/24 network as 10.10.1.1/32 to R2 and R3.
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17363056720004yctro.png)

--------------------------------------------------------------------------
## Multiaccess network

Routers can be connected to the same switch to form a multiaccess network, as shown in the figure. Ethernet LANs are the most common example of broadcast multiaccess networks. In broadcast networks, all devices on the network see all broadcast and multicast frames.

DROTHERs use the multiaccess address 224.0.0.6 (all designated routers) to send OSPF packets to the DR and BDR. Only the DR and BDR listen for 224.0.0.6.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736309495000iymoji.png)

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736310033000ktyqvs.png)

In the multiaccess topology shown in the figure, there are three routers interconnected over a common Ethernet multiaccess network, 192.168.1.0/24. Each router is configured with the indicated IPv4 address on the Gigabit Ethernet 0/0/0 interface.

Because the routers are connected over a common multiaccess network, OSPF has automatically elected a DR and BDR. In this example, R3 has been elected as the DR because its router ID is 3.3.3.3, which is the highest in this network. R2 is the BDR because it has the second highest router ID in the network.

To check if the router is a DROTHER,DR or a BDR, use the command `sh ip ospf int g0/0/0`
