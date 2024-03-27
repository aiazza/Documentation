### Network Fundamentals

#### Life of a Packet: Understanding the Journey

**What happens when I type google.com in my browser?**

- **DNS Query Initiation**
  - The process starts with identifying the IP address for google.com. This involves sending a DNS request, usually facilitated by DHCP.
- **Formation and Encapsulation of DNS Query**
  - A DNS query is formulated and encapsulated within a UDP segment, typically using port 57.
- **IP Packet Encapsulation**
  - The UDP segment is then encapsulated in an IP packet, destined for the DNS server.
- **Ethernet Frame Encapsulation**
  - This IP packet is further encapsulated in an Ethernet frame, targeting the default gateway's MAC address obtained via an ARP request.
- **Transmission**
  - The packet is transmitted through the network.
- **Routing through the Network**
  - Upon reaching the router, it is forwarded based on the routing table entries to the DNS server.
- **DNS Resolution**
  - The DNS server resolves google.com to its corresponding IP address and responds.
- **Secure Communication Initiation**
  - The application layer initiates a secure HTTPS connection, starting with a TLS payload for negotiation.
- **TCP Connection Establishment**
  - A TCP SYN packet is sent to establish a connection.
- **Journey Through the Network**
  - The packet traverses the network, potentially passing through various devices like firewalls.

**Exploring Network Tools and Concepts**

- **Traceroute**: Understanding the Path of Packets
- **Network Buffers**: Managing Data Flow
- **Routing Tables and Forwarding**: The Role of RIB, FIB, and TCAM

### TCP/IP Protocols and Operations

**Ensuring Reliable Communication**

- **The 3-Way Handshake**: Establishing a TCP Connection
- **Selective Acknowledgement**: Improving Efficiency
- **TCP Flags**: Understanding Their Role
- **TCP Variants**: Exploring Different Implementations
- **Congestion Control**: Managing Network Traffic
- **Window Scaling**: Enhancing Throughput
- **TCP Header Analysis**: Size, Fields, and MSS

### OSPF: Open Shortest Path First

**Diving Into OSPF Dynamics**

- **LSA Types**: Dissecting Link State Advertisements
- **OSPF Areas**: Understanding Different Types
- **Interface Types in OSPF**
- **Router ID (RID) Concept**
- **DR/BDR Election Process**: Electing Designated Routers
- **Quiet IGP**: Why OSPF Fits This Description
- **Neighbor States in OSPF**
- **Database Description Packets**
- **Convergence Process**: Ensuring Network Stability
- **Route Types in OSPF**

### BGP: Border Gateway Protocol

**The Complex World of BGP**

- **BGP Attributes**: Essential for Path Selection
  - **AS_PATH**: Tracing the Route
  - **Atomic Aggregate and Aggregator**
  - **ASN Sizes**: 16-bit vs 32-bit
- **BGP Traffic Engineering**: Strategies for Optimal Routing
  - **AS Path Prepend**
  - **Route Advertisement Strategies**
  - **Community Tags**
  - **Route Suppression**
- **BGP Messaging System**: Keeping the Network Informed
- **BGP States**: Understanding Neighbor Relationships
- **Route Reflectors vs Confederations**: Scalability and Redundancy
- **iBGP vs eBGP**: Internal vs External BGP Peering

### Automation in Networking

**Leveraging Technology for Efficiency**

- **Data Structures and Algorithms**: The Backbone of Automation
- **Big O Notation**: Evaluating Performance

