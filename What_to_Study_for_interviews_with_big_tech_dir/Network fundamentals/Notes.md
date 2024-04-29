# Network Fundamentals

## Life of a Packet
### What happens when I type google.com in my browser
- DNS resolution 
  - ARP request : My machine will check in it's ARP cache if it knows the local Gateway MAC, if not it will send an ARP request. The ARP request will be sent flooded out of it's interfaces asking who has this IP address ?(DNS IP). If there is a L2 switch in between it will first check if it doesn't have the MAC of the gateway in it's MAC table, if not flood it out all ports except the one it received it on. The router configured as local gateway will respond to the ARP request, the switch will update it's MAC table and forward the ARP reply to the host. Now the host knows it's L3 destination IP (DNS) and it's L2 destination MAC (Gateway) So it will create a UDP packet with destination port 53 (DNS packet) with that info. The DNS will reply with whatever IP it has for Google.com, the PC will then have the correct IP to use to communicate with google.com
- TCP Connection setup : Once My PC has the correct IP of google.com it will create TCP segment with destination port 443 to initiate connection with the host, the first step would be 3 way handshake. that TCP segment would be encapsulated in an IP header which would be used to be routed on the network towards google.com.
- L3 Routing : The Packet would then hit the default gateway which would lookup the route in its RIB to see where to send that packet, and so on and so forth until it egresses towards the internet hits google.com host and replies to the PC.
- HTTPS connection setup
- Secure exchange of Data 

## Network Tools
### How does traceroute work
Traceroute has two different implementations either using ICMP or using UDP, let's see how it works for both.

#### ICMP Traceroute 
When we  use the ICMP traceroute command, the sender will send an ICMP echo-request with a TTL of 1, the traceoute will hit the first router which will decrement the TTL and send back a ICMP "Time Exceeded" Message back to the sender. When the sender receives that message it will record the IP of the host and then increment the TTL by 1 making it 2 so that it records the second hop router IP and so on and so forth. Once it reaches it's destination the destination host will respond with an ICMP Echo Reply so that the initial sender knows that it reached it's destination.

#### UDP Traceroute (Linux)
UDP Traceroute works similarly to the ICMP one in regards to decrementing and incrementing the TTL, but the difference is in the message it initially sends. Instead of sending an ICMP echo request it sends a UDP packet to an unlikely destination port, once that message reaches the destination the receiver will respond with a Port Unreachable message, which tells the sender that it reached it's destination.

## Buffer
## RIB/FIB/TCAM
## ASIC

