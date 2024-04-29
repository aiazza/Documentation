# OSPF
- What is OSPF ? 
OSPF is a link-state routing protocol, it's used to exchange routes within the same adminstrative domain. The way it works is that it holds a map of the whole network, meaning all routers and links then uses the dijkstra algorithm to build the shortest path to reach a certain destination. Because it holds the map of the whole network, those maps can get very big, thats why OSPF organized in a hierarchical manner, it has a backbone area and then other areas to which all backbone areas have to connect to. 

## Neighbors and adjacencies
To be able to exchange routing information, OSPF first has to form neighborships and adjacencies with neighbor routers. To do so OSPF utilizes hello packets to negotiate the parameters of that neighborship. The hello packets contain the following information. 

- Router ID
- Area ID
- Address Mask of the originating interface
- Authentication type and information
- HelloInterval : Interval between hello messages
- DeadInterval : Interval before a router is declared dead
- Priority
- DR and BDR
- Flag capability bits
- Router ID's of the routers neighbors

All these parameters must be agreed upon prior to forming an adjacency

## OSPF Packet types 
There are 5 packet types that OSPF uses to exchange info between routers : 

- Hello packets : These are used for discovering and maintaining neighbor relationships
- Database descriptor (DBD) packets : This is used to summarize database information and ensure DB are in sync, it holds info such as interface MTU , sequence number and all LSA headers.
- Link state request packets (LSR) : After exchanging databases with a neighbor a router may find that its DB is out of date, when that happens it sends an LSR packet to request the missing pieces of it's DB.
- Link state update : Sent as response to LSR packet to send missing pieces of the neighbors DB.
- LSA : LSA's are OSPF's messaging system, it's a message that informs neighbors of it's local routing topology. It basically tells other routers what it sees from it's perspective.
- LSAck : Used to acknowledge one or multiple LSA's. 

## LSA types
There are 6 well-known LSA types : 
- type 1 - Router LSA : Includes all the links, interfaces, link states and costs associated with the originating router. Does not travel between areas
- Type 2 - Network LSA : Produced by every DR on every broadcast/NBMA network. Does not travel between areas.
- Type 3 - Summary LSA : Now this one is a bit confusing, the "summary" part in "summary LSA" refers to the fact that this type of LSA summarizes routing information about multiple networks into a single advertisement, this is sent by the ABR to tell other routers about its connected area. Travels between areas.
- Type 4 - ASBR summary LSA : ASBR routers job is to connect OSPF domains to external domains and exchanges prefixes between both. To be able to exchanges prefixes towards external domains, every router in the domain has to know where the ASBR is located, so this LSA gives that information.
- Type 5 - External LSA : LSA used to exchanges external prefixes with the OSPF domain.
- Type 7 - NSSA External : in NSSA domain we can't carry type 5 LSA's so these are like Type 5 disguised as type 7, once they leave the NSSA area they are handled as type 5 LSA's

Here is a fun way of seeing it (only 1 to 7 because those are the most important) : 

Type 1 : Router-LSA : "Hello fellow neighbors from Area 0 ! , My name is 1.1.1.1, here is the information about my links and neighbors !"
Type 2 Network-LSA : "Hello I'm the DR in Area 0 for link 192.168.123.0 ! here is the information for all neighbors connected to me! "
Type 3 : Network-summary LSA : "Hello ! I'm Area 1's ABR ! I see that I'm also connected to Area 0, here is a resumé of all networks in area 1 for area 0 to know where to reach us!" 
Type 4 : ASBR-summary-LSA : "Hello ! I'm Area 1's ABR ! I know who the ASBR is ! I'm sending the routes out to you other areas so you know how to get out of this OSPF Domain!"
Type 5 : AS-External-LSA : "Hello ! I am area 1's ASBR, I'm receiving a route from the outside world and I'm forwarding it to you guys! 
Type 7 : NSSA-LSA : "Hello ! I am area 1's ASBR, I see that I'm in a not so stubby area and they don't accept type 5 LSA's, you know what ! I'll send it as a type 7 LSA and my buddy ABR will take care of the translation if it has to go to other zones ! 

## Stub Area types
There are 3 types of stub areas in OSPF : 

- Stub : This is when the only path to external networks is via the ABR through area 0, in that case the stub area doesn't need to receive type 4 (ASBR summary LSA) type 5 LSA (ASBR external LSA) & type 7 (NSSA LSA) because it's only way out of the AS is through ABR, so ABR injects a default route into the area.
- Totally stubby area : This is when the only path to external networks AND other areas are via the ABR through area 0, in that case the totally stubby area doesn't need to receive type 3 (Summary LSA) type 4 (ASBR summary LSA) and type 5 (ASBR external LSA), a single default route is injected from ABR into the area instead. 
- Not so stubby area (NSSA) : This is when there is a stub area where there is redistribution of an external network, that is where the type 7 LSA come in handy (NSSA LSA), once all routers agree that they are in a NSSA, if they receive an external route it will generate a type 7 LSA that will be translated into a type 5 LSA when it passes the ABR to go to another area. it's just a type 5 LSA disguised as a type 7.
- Totally Not so stubby area : same as NSSA but type 4 and 3 are not permitted, just default route to exit area.
## OSPF interface types
## RID Concept
## DR/BDR election process
## Why is OSPF considered a quiet IGP
## Neighbor states
## DB description packets
## Convergence process
## Route types

