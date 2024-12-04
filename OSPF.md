OSPF is a link-state routing protocol that uses the concept of areas. A network administrator can divide the routing domain into distinct areas that help control routing update traffic.

##### OSPF Packets
Routers running OSPF exchange messages to convey routing information using five types of packets. These packets, as shown in the figure, are as follows:

- Hello packet
- Database description packet
- Link-state request packet
- Link-state update packet
- Link-state acknowledgment packet

These packets are used to discover neighboring routers and also to exchange routing information to maintain accurate information about the network.

##### OSPF Databases
OSPF messages are used to create and maintain three OSPF databases, as follows:

- **Adjacency database** - This creates the neighbor table.
- **Link-state database (LSDB)** - This creates the topology table.
- **Forwarding database** - This creates the routing table

##### Algorithm
The router builds the topology table using results of calculations based on the Dijkstra shortest-path first (SPF) algorithm. The SPF algorithm is based on the cumulative cost to reach a destination.

The SPF algorithm creates an SPF tree by placing each router at the root of the tree and calculating the shortest path to each node. The SPF tree is then used to calculate the best routes. OSPF places the best routes into the forwarding database, which is used to make the routing table.

Diagram of how the SPF algorithm takes the costs of router links to create an SPF tree by placing each router at the root of the tree and calculating the shortest path to each node. The SPF tree is then used to calculate the best routes.

##### Link State Operation 
To maintain routing information, OSPF routers complete a generic link-state routing process to reach a state of convergence. The figure shows a five router topology. Each link between routers is labeled with a cost value. In OSPF, cost is used to determine the best path to the destination. The following are the link-state routing steps that are completed by a router:

1. Establish Neighbor Adjacencies
2. Exchange Link-State Advertisements
3. Build the Link State Database/Topology table
4. Execute the SPF Algorithm
5. Choose the Best Route

##### Multi Area OSPF
With multiarea OSPF, one large routing domain can be divided into smaller areas, to support hierarchical routing. Routing still occurs between the areas (interarea routing), while many of the processor intensive routing operations, such as recalculating the database, are kept within an area.

For instance, any time a router receives new information about a topology change within the area (including the addition, deletion, or modification of a link) the router must rerun the SPF algorithm, create a new SPF tree, and update the routing table.

**Note:** Routers in other areas receive updates regarding topology changes, but these routers only update the routing table, not rerun the SPF algorithm.

