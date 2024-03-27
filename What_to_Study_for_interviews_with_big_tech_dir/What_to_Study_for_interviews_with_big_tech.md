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
- Aggregator

Aggregator is an BGP attribute that specifies to neighbor in which AS Number aggregation was done as well as the routing ID 

- Atomic aggregator

Specifies that route has been aggregated and some prefixes may be suppressed 
### 16-bit (2byte) vs 32-bit (4byte) ASN
- 2 Byte ASN

Due to the large number of entities using BGP and the internet growing larger every year, the 16 bit ASN started to be a constraint for large scale deployment, similarly to IPv4 running out of addressing space and needing to go to IPv6. As a refresher there are two types of BGP Autonomous system numbers: Private and Public. The Public AS numbers range from 1 to 64511 and the Private AS numbers range from 64512 to 65535 leaving only 1023 Autonomous systems for organizations to use. This is not an issue for Regular enterprise networks, even very large ones, but is an issue for hyperscalers which use BGP as IGP in the datacenter for example. 

- 4-Byte ASN

Because of that 4 byte ASN feature was developed for BGP which give much more address space to use. 4 byte ASN also has a public and a private range, the public 4-byte AS number range is from 1 to 4,199,999,999 while ,the private 4-byte AS number range is from 4,200,000,000 to 4,294,967,294, This provides a total of 94,967,295 private AS numbers available for use in various private networks and scenarios, quite a lot huh ? 


## BGP Traffic engineering (inbound/outbound)
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
