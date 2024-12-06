### "Best Path Equals Longest Match"
The longest match is a process the router uses to find a match between the destination IP address of the packet and a routing entry in the routing table.

The routing table contains route entries consisting of a prefix (network address) and prefix length. For there to be a match between the destination IP address of a packet and a route in the routing table, a minimum number of far-left bits must match between the IP address of the packet and the route in the routing table. The prefix length of the route in the routing table is used to determine the minimum number of far-left bits that must match. Remember that an IP packet only contains the destination IP address and not the prefix length.

The longest match is the route in the routing table that has the greatest number of far-left matching bits with the destination IP address of the packet. The route with the greatest number of equivalent far-left bits, or the longest match, is always the preferred route.

**Note:** The term prefix length will be used to refer to the network portion of both IPv4 and IPv6 addresses.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images17334153720007vutnx.png)
Of the three routes, 172.16.0.0/26 has the longest match and would be chosen to forward the packet.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733415456000fl0xqd.png)

### Filtering Command Output
Another useful feature that improves user experience in the command-line interface (CLI) is filtering **show** output. Filtering commands can be used to display specific sections of output. To enable the filtering command, enter a pipe (**|**) character after the **show** command and then enter a filtering parameter and a filtering expression.

The filtering parameters that can be configured after the pipe include:

- **section** - This displays the entire section that starts with the filtering expression.
- **include** - This includes all output lines that match the filtering expression.
- **exclude** - This excludes all output lines that match the filtering expression.
- **begin** - This displays all the output lines from a certain point, starting with the line that matches the filtering expression.

**Note:** Output filters can be used in combination with any **show** command.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images173341628700089gdia.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733416308000jutcgk.png)

### Packet Forwarding Mechanisms
As mentioned previously, the primary responsibility of the packet forwarding function is to encapsulate packets in the appropriate data link frame type for the outgoing interface. The more efficiently a router can perform this task, the faster packets can be forwarded by the router. Routers support the following three packet forwarding mechanisms:

- Process switching
- Fast switching
- Cisco Express Forwarding (CEF)

A common analogy used to describe these three different packet-forwarding mechanisms is as follows:

- Process switching solves a problem by doing math long hand, even if it is the identical problem that was just solved.
- Fast switching solves a problem by doing math long hand one time and remembering the answer for subsequent identical problems.
- CEF solves every possible problem ahead of time in a spreadsheet.

### Static Route and Dynamic Route
Static routes are commonly used in the following scenarios:

- As a default route forwarding packets to a service provider
- For routes outside the routing domain and not learned by the dynamic routing protocol
- When the network administrator wants to explicitly define the path for a specific network
- For routing between stub networks

Static routes are useful for smaller networks with only one path to an outside network. They also provide security in a larger network for certain types of traffic, or links to other networks that need more control.

Dynamic routing protocols are commonly used in the following scenarios:

- In networks consisting of more than just a few routers
- When a change in the network topology requires the network to automatically determine another path
- For scalability. As the network grows, the dynamic routing protocol automatically learns about any new networks.

![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733475476000ep35m6.png)

The table classifies the current routing protocols. Interior Gateway Protocols (IGPs) are routing protocols used to exchange routing information within a routing domain administered by a single organization. There is only one EGP and it is BGP. BGP is used to exchange routing information between different organizations, known as autonomous systems (AS). BGP is used by ISPs to route packets over the internet. Distance vector, link-state, and path vector routing protocols refer to the **type of routing algorithm** used to determine best path.


![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733475678000wif2qd.png)

### Determination of route in the routing table(Summary)
### 1. **Longest Prefix Match (Most Specific Route)**

- The route with the most specific subnet mask (longest prefix) is chosen.  
    Example:
    - Destination: `192.168.1.1`
    - Routes available:
        - `192.168.1.0/24`
        - `192.168.1.0/25`
    - **Chosen**: `192.168.1.0/25` because `/25` is more specific.
### 2. **Administrative Distance (AD)**

- If multiple routes to the same destination exist (same prefix length) but are learned from different sources, the route with the **lowest AD** is selected.  
    Example:
    - Static route (AD = 1)
    - OSPF route (AD = 110)
    - **Chosen**: Static route.
### 3. **Metric**

- If multiple routes to the same destination exist (same prefix length and AD) within the same protocol, the route with the **lowest metric** is chosen.  
    Example:
    - OSPF route via path A (Cost = 10)
    - OSPF route via path B (Cost = 5)
    - **Chosen**: Path B (Cost = 5).
### 4. **Load Balancing**

- If multiple routes have the **same prefix length, AD, and metric**, load balancing occurs (if supported by the routing protocol and enabled).  
    Example: Equal-cost multipath (ECMP) in OSPF or EIGRP.
