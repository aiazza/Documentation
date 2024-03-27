# Network Fundamentals

## Life of a Packet
### What happens when I type google.com in my browser
#### DNS Query Initiation
#### Formation and Encapsulation of DNS Query
#### IP Packet Encapsulation
#### Ethernet Frame Encapsulation
#### Transmission
#### Routing through the Network
#### DNS Resolution
#### Secure Communication Initiation
#### TCP Connection Establishment
#### Journey Through the Network

## Network Tools
### How does traceroute work
### How to read PCAP

## Buffer
## RIB/FIB/TCAM

# TCP/IP

## 3 Way Handshake
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

## BGP Load sharing 
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
### Multi-homing
#### Multi-homing with the Same ISP

#### Multi-homing with different ISP 
### AS path prepend
### More specific routes out of preferred route
### Communities
### do not advertise at all
## BGP messaging system (Open - update - keepalive - notification)
## BGP neighbor states
## Route reflector vs confederations
### Route reflector scaling
### Route reflector redundancy
### loop prevention in RR architectures
## iBGP vs eBGP

# Automation

## Data structures and Algorithms
## Big O notation
