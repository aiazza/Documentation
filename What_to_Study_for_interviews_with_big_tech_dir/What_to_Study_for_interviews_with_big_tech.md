# Network Fundamentals

## Life of a Packet
### What happens when I type google.com in my browser
- DNS Query Initiation
- Formation and Encapsulation of DNS Query
- IP Packet Encapsulation
- Ethernet Frame Encapsulation
- Transmission
- Routing through the Network
- DNS Resolution
- Secure Communication Initiation
- TCP Connection Establishment
- Journey Through the Network

## Network Tools
### How does traceroute work
### How to read PCAP

## Buffer
## RIB/FIB/TCAM

# TCP/IP

## 3 Way Handshake
As we know TCP is a connection-oriented transport protocol, what does it mean ? It means that before communicating data with a destination host we form a session, sort of a virtual tunnel with certain mechanisms to make sure all packets arrive reliably .Speaking of reliability We often hear that TCP is reliable, we say that because with TCP mechanism we make sure that all packets are delivered with no errors and in the right order. In order to establish the connection TCP goes through a process called the 3-way handshake where each side announces its capabilities and preferences in the form of TCP options, after that our TCP connection is formed, here is the sequence of events : 
- SYN : Client (host initiating the request) initiates TCP connection by sending TCP segment with SYN flag, this is the first step of forming a TCP connection, the client  announces it's initial sequence number as well as other options specific to the host (Maximum Segment Size (MSS) , Window Scale option,  Selective Acknowledgment (SACK) etc.)
- SYN-ACK : Server (host receiving the request) responds with a TCP segmen with the SYN and the ACK flag, the ack is the clients initial sequence number + 1 
- ACK : Client will respond with a TCP segment including the ACK flag adding 1 to the initial sequence number sent during SYN and adding 1 to the first server ACK seq number. 
```
Client                                       Server
  |                                            |
  | ----- SYN (Seq X) -----------------------> |    1. Client sends SYN with sequence number X
  |                                            |
  | <---- SYN-ACK (Seq Y, Ack X+1) ---------- |    2. Server responds with SYN-ACK, sequence number Y, and acknowledgment X+1
  |                                            |
  | ----- ACK (Seq X+1, Ack Y+1) -----------> |    3. Client acknowledges with ACK, sequence number X+1, and acknowledgment Y+1
  |                                            |

```

## Selective Ack
## Flags
## Different implementations of TCP
## Congestion control (CWND, RWND)
## Window scaling
## TCP header (size / fields) , MSS

# OSPF

## LSA types
## Area types
## OSPF interface types
## RID Concept
## DR/BDR election process
## Why is OSPF considered a quiet IGP
## Neighbor states
## DB description packets
## Convergence process
## Route types

# BGP

## BGP attributes
- What are BGP attributes ?

BGP attributes are attributes used to determine the best route to choose between multiple routes, one thing I use to memorize the is the mnemnonic **We Love Oranges As Oranges Mean Pure Refreshment** Which covers the first letter for the most important attributes :

- W - Weight : A Cisco-specific attribute that is local to the router and not advertised to other routers. Higher weights are preferred.
- L - Local Preference : Indicates the preferred path within an AS. the higher the better, and this attribute is shared within the local AS.
- O - Origin : Indicates the origin of the route, it can either be IGP (i - First preferred) meaning the route was originated using an interior gateway protocol such as OSPF or EIGRP, EGP(e - second preferred) meaning the route was learned via an exterior gateway protocol, this one is obsolete, or Incomplet (? - Least preferred) meaning this was injected other than IGP or EGP, for example through redistribution or 
- O - AS Path - The AS Path map, meaning a list of all the AS which the route transited by, this is important to prevent loops in eBGP or influence policy to prefer one route over the other. The lower the better 
- M - MED (Multi-Exit Discriminator) : Suggest remote AS a preferred path into the AS when multiple entry points exist.
- P - Prefer eBGP over IBGP - 
- R - RID - Router ID 

### Details of AS_PATH
### Atomic aggregate and aggregator
#### Aggregator

Aggregator is an BGP attribute that specifies to neighbor in which AS Number aggregation was done as well as the routing ID 

#### Atomic aggregator

Specifies that route has been aggregated and some prefixes may be suppressed 
### 16-bit (2byte) vs 32-bit (4byte) ASN
#### 2 Byte ASN

Due to the large number of entities using BGP and the internet growing larger every year, the 16 bit ASN started to be a constraint for large scale deployment, similarly to IPv4 running out of addressing space and needing to go to IPv6. As a refresher there are two types of BGP Autonomous system numbers: Private and Public. The Public AS numbers range from 1 to 64511 and the Private AS numbers range from 64512 to 65535 leaving only 1023 Autonomous systems for organizations to use. This is not an issue for Regular enterprise networks, even very large ones, but is an issue for hyperscalers which use BGP as IGP in the datacenter for example. 

#### 4-Byte ASN

Because of that 4 byte ASN feature was developed for BGP which give much more address space to use. 4 byte ASN also has a public and a private range, the public 4-byte AS number range is from 1 to 4,199,999,999 while ,the private 4-byte AS number range is from 4,200,000,000 to 4,294,967,294, This provides a total of 94,967,295 private AS numbers available for use in various private networks and scenarios, quite a lot huh ? 

## BGP Load sharing & Multihoming
#### Load Sharing with the Loopback Address as a BGP Neighbor
When we do load sharing between neighbors forming over loopback addresses connected via multiple links (Maximum of 6) We can achieve load sharing by using the **ebgp-multihop** command, you must configure ebgp-multihop whenever the external BGP (eBGP) connections are not on the same network address.

![alt text](https://www.cisco.com/c/dam/en/us/support/docs/ip/border-gateway-protocol-bgp/13762-40-00.png)

#### Load Sharing When Dual-Homed to One Internet Service Provider (ISP) Through a Single Local Router
When we want to achieve load sharing between one ISP through a single router we can use the **maximum-paths** feature, this  specifies the maximum number of paths to install in the routing table for a specific destination

![alt text](https://www.cisco.com/c/dam/en/us/support/docs/ip/border-gateway-protocol-bgp/13762-40-01.png)

#### Load Sharing When Dual-Homed to One ISP Through Multiple Local Routers
To achieve load sharing through multiple routers to the same ISP we can advertise both prefixes through each routers respective link for redundancy do AS_Path prepend towards the neighbors we don't want those prefixes to prefer. Example, For inbound policy, in the figure below, we want ingress traffic for 192.168.11.0 to come in R101 and we want ingress traffic for 192.168.12.0 to come in R102 so we create a route map on each router matching the prefix we want to deprioritize for that router and do an AS_PATH prepend. By doing so, ISP will prefer the route with the lowest AS_PATH for ingress traffic

![alt text](https://www.cisco.com/c/dam/en/us/support/docs/ip/border-gateway-protocol-bgp/13762-40-02.gif)

#### Load Sharing When Multihomed to Two ISPs Through a Single Local Router

In this scenario, load balancing is not an option because BGP only chooses a single best path to a destination among bgp routes learned from different AS, the idea to achieve load sharing here is to split prefixes being advertise into 2 different subnets and prefer prefix over one link and one over the other link. For example if we have 192.168.0.0/23 we would split it into 192.168.0.0/24 and 192.168.1.0/24 then make one go over the link to router B and the other go over the link to router C. 

![alt text](https://www.cisco.com/c/dam/en/us/support/docs/ip/border-gateway-protocol-bgp/13762-40-04.png)

#### Load Sharing When Multihomed to Two ISPs Through Multiple Local Routers

Load balancing is not possible in a multihomed environment with two ISPs. BGP selects only the single best path to a destination among the BGP paths learned from different ASs, which makes load balancing impossible. But, load sharing is possible in such multihomed BGP networks. On the basis of predetermined policies, traffic flow is controlled with different BGP attributes. In the figure below, similarly to the previous example we split the prefixes being advertised, we use local preference to influence outbound traffic over and AS_Path prepend to influence inbound traffic

![alt text](https://www.cisco.com/c/dam/en/us/support/docs/ip/border-gateway-protocol-bgp/13762-40-05.png)



## BGP Traffic engineering (inbound/outbound)
### Hot potato / Cold potato routing 
- Hot Potato Routing: The network tries to offload the traffic as soon as possible, onto another network. It chooses the shortest path to exit its own network, regardless of the total path length or the subsequent path that the traffic will take once it has left the network. Figure below on the left.

- Cold Potato Routing: The network keeps the traffic within its own network for as long as possible, preferring a longer internal path if it results in a shorter path once the traffic exits the network. Figure below on the right.

![alt text](https://miro.medium.com/v2/resize:fit:1400/format:webp/0*zzx4mX6ToQlGpKb2)


#### Traffic Engineering toolset
##### AS Path prepend
AS Path prepend is used to influence inbound traffic, by prepending the routers AS one or multiple times to the AS_PATH attribute of the prefix, we can effectively make the neighbor prefer another path. 
##### Local preference
Local preference is used to influence outbound traffic, it is an attribute that is propagated to the whole AS which can be used to prefer one path over the other when multiples paths exist for a specific outbound destination.
##### Multi-Exit Discriminator (MED)
MED is used to influence ingress traffic, it is important to note that MED is just a recommendation and may or may not be used by the external AS, due to that this is less frequently used. It's worth noting that we only compare MED between different paths in the same AS, if we receive different paths to the same prefix from 2 different AS's it won't compare MED values for them. 
##### More specific routes out of preferred route - Selective Advertisement
This technique is used to influence inbound traffic. How it works is that we advertise a more specific prefix over a link so that the external AS prefers that link for that prefix, for example if we are advertising 192.168.0.0/16 and we advertise 192.168.10.0/24 towards one AS so that it always uses that path for that prefix. because BGP prefers more specific routes over less specific ones. 
##### Communities
BGP communities are pieces of information we can add to prefixes, this information can be used as a signal to take actions on such as traffic engineering or dynamic routing. 4 well-known internet BGP communities are :

- Internet: advertise the prefix to all BGP neighbors.
- No-Advertise: don’t advertise the prefix to any BGP neighbors.
- No-Export: don’t advertise the prefix to any eBGP neighbors.
- Local-AS: don’t advertise the prefix outside of the sub-AS (this one is used for BGP confederations).

Additionally here are some communities used by Verizon :

```
+-------------------+--------------+-----------------+
| BGP Community String | LocalPref Value | Description     |
+-------------------+--------------+-----------------+
| Default              | 100            | Default Value   |
| 70x:80               | 80             | Set localpref 80 |
| 70x:90               | 90             | Set localpref 90 |
| 70x:110              | 110            | Set localpref 110 |
| 70x:120              | 120            | Set localpref 120 |
+-------------------+--------------+-----------------+
```


##### Conditional Advertisement

Conditional advertisement is the process of advertising or not advertising routes based on meeting specific conditions. You have 2 modes of conditional advertisement : 

- Advertise-Map: Advertise routes when conditions are met.
- Non-Exist-Map: Advertise routes when conditions aren't met.

Example : advertise a backup route only if the primary route is unavailable. You would define 2 route maps, 1 for primary route and 1 for backup route and specify which one to use in which condition, the command would look something like this : 
```
router bgp [YOUR_ASN]
    neighbor [NEIGHBOR_IP] advertise-map BackupAdvertiseMap non-exist-map PrimaryRouteMap
```

## BGP messaging system (Open - update - keepalive - notification)
BGP relies on different message types to establish neighborships, verify neighborship health, and exchange updates, here are the message types : 
- Open : This is the first message after the TCP session has been formed. This message contains information such as BGP version number, the autonomous system (AS) number of the sender, the BGP identifier (usually the router's IP address), and optional parameters that may include capabilities like support for 4-byte AS numbers or route refresh capabilities.
- Update  :  Update message are used to exchange routing information, these messages can advertise new routes as well as withdraw routes not relevant, these messages also carry BGP Path attributes such as local preference, AS_PATH, MED , communities etc.
- Keepalive : This is how BGP knows that the neighbor is still there and healthy. The frequency is determined by the hold time negotiated during neighborship process. If BGP does not receive a keepalive for the hold time it will reset the session.
- Notification : These messages are used to notify the neighbor of errors and eventually reset the connection, more info on the specific notification codes here : https://www.inetdaemon.com/tutorials/internet/ip/routing/bgp/operation/messages/notification.shtml

## BGP neighbor states
Here are the states BGP has to go through to establish a neighbor relationship : 
- Idle : This state is the state in which BGP is initially set to after configuring the neighbor command, in this state the neighbor is waiting for a start event. The start event occurs when we configure another router to be BGP neighbor of the first router. It resets the ConnectRetry timer which controls the interval between successive attempts to establish a TCP connection with a peer after a previous attempt fails. in this state we try to initiate a TCP 3-way handshake, if successful it moves to the **Connect** State, if not it stays Idle.
- Connect : In this state the BGP neighbor waits for the 3 way handshake to complete. When successful it will go to **OpenSent** State. If it fails it goes to **Active** State. 
- Active : BGP will try to initiate another TCP 3-Way handshake, if successful it will move to **OpenSent** state. If the ConnectRetry time expires it will go back to the Connect state. 
- OpenSent : BGP will wait for an open message from neighbor and will check if everything is there are no errors (wrong ASN, incorrect version etc), if everything is OK BGP starts sending keepalive messages and goes to **OpenConfirm** State, if TCP session fails it will go back to **Active** .
- OpenConfirm : BGP waits for a keepalive messafe from neighbor, once we recieve the keepalive, we can safely go to **Established** state.
- Established : BGP Neighbor adjacency is formed and routers are exchanging routes.
  
![alt text](https://cdn.networklessons.com/wp-content/uploads/2015/04/bgp-states-neighbor-adjacency.png)

## Route reflector vs confederations
### Route reflectors 
To understand what route reflectors are used for we have to understand why they are used. When we have multiple iBGP neighbors there is a rule called iBGP split horizon, which is that any route received from an internal peer cannot be advertised to another internal peer. Due to that all iBGP nodes have to be fully meshed, this is ok when we have 5 routers but when we have hundreds of thousands like in hyperscalers that can get messy very fast, it's not scalable at all. Thats why route reflectors exist, Route reflectors are a way to designate one router as route "server" and other routers as route "clients".
#### Route reflector scaling

We can achieve very large BGP topology scale using hierarchical route-reflector design 


- What is a hierarchical route-reflector design ? 
A hierarchical route-reflector design is a design where a there are multiple layers of route-reflectors, there are top-level route-reflectors connected to clients that are also route-reflectors in other clusters. 

- Why use this design ? 
Because in some very large networks a single layer of route-reflectors may not be enough, normally when you implement your route-reflectors the number of iBGP full-mesh links should reduce drastically, if the number of remaining links is still too much you can implement a second layer of hierarchy.

How do we prevent loops in this design ? 
The key to preventing loops in a hierarchical route-reflector cluster design is the cluster-list, it is like the AS_PATH Attribute, with every reflection the cluster-list will be longer. If a route comes back to the original reflector, it will discard the packet.
#### Route reflector redundancy
A common practice in enterprise networks is to add another Route reflector to avoid having a single point of failure of the only RR if  the network goes down. This is called a non-redundant cluster. This should be avoided if possible. The concept of clusters is introduced to prevent BGP routing loops when you use multiple RR's in your network.
#### Route reflector cluster 
Clusters are a set of a route-reflector and it's client's that are designed to prevent routing loops in an multi-route-reflector topology. Each cluster has it's own cluster ID which is propagated as an attribute called "cluster list" , the cluster-id is unique to the AS. Route reflectors from different clusters need to be fully meshed with route-reflectors from other clusters except in a hierarchical design where a route-reflector is a client of another route-reflector. Cluster ID is not known by clients. You have the choice of specifically configuring the cluster ID with the bgp cluster-id command, if you don't the cluster-id will be the router-id of the route-reflector. 
### loop prevention in RR architectures
So how do we prevent loops in redundance Route reflector architecture ?
Simple, by using Originator ID and Cluster list :

- Originator-ID : This is the router ID of the route originator, where the route comes from in plain english. If a router sends a route then receives a route with it's own originator-ID there must be a loop somewhere, therefore the route will be discarded. 
- Cluster-list : As discussed above, cluster-list is used a little like the AS_PATH attribute in the sense that each reflector appends it's own cluster ID to the cluster list, if a RR receives a route with it's own cluster-ID in the cluster-list, it knows there must be a loop somewhere so it discards the packets. 
### Confederations 
- What Are BGP Confederations ?

BGP confederations are a way of splitting an AS into multiple sub-AS's, for neighbors peering with that AS, it is transparent as confederations are only visible within the AS. Confederations are used to partition an AS into multiple pieces, this could based on geographical location, need for different eBGP type routing policies within an AS, different organization within the same AS etc. Confederations use eBGP to communicate with each other exactly as if it were two external AS's.

- What is the loop prevention mechanism in BGP confederations ?

The loop prevention mechanism in BGP confederations depends on who we are communicating with. If we are communicating with peers outside the overlying AS then the rulesare the same as for eBGP neighbors, if the router sees it's own ASN in the AS_Path it will drop the prefix. If we are communicating with peers inside the same AS but in different confederations, then BGP adds an attibutes called CONFED_SEQUENCE, which operates like an AS path but uses only the privately assigned sub-AS numbers

# Automation

## Data structures and Algorithms
## Big O notation
