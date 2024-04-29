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
Traceroute has two different implementations either using ICMP or using UDP, let's see how it works for both.

#### ICMP Traceroute 
When we  use the ICMP traceroute command, the sender will send an ICMP echo-request with a TTL of 1, the traceoute will hit the first router which will decrement the TTL and send back a ICMP "Time Exceeded" Message back to the sender. When the sender receives that message it will record the IP of the host and then increment the TTL by 1 making it 2 so that it records the second hop router IP and so on and so forth. Once it reaches it's destination the destination host will respond with an ICMP Echo Reply so that the initial sender knows that it reached it's destination.

#### UDP Traceroute (Linux)
UDP Traceroute works similarly to the ICMP one in regards to decrementing and incrementing the TTL, but the difference is in the message it initially sends. Instead of sending an ICMP echo request it sends a UDP packet to an unlikely destination port, once that message reaches the destination the receiver will respond with a Port Unreachable message, which tells the sender that it reached it's destination.

## Buffer
## RIB/FIB/TCAM
## ASIC

