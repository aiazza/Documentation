# Network Fundamentals

## Life of a Packet
### What happens when I type google.com in my browser
- DNS Resolution
When my machine needs to resolve a domain name like "google.com" to an IP address, it first checks its ARP cache to see if it already knows the MAC address of the local gateway. If not found, it sends an ARP request, which is broadcasted across all interfaces, asking who has the MAC address associated with the IP of the DNS server. If there's a Layer 2 switch in between, it checks its MAC table; otherwise, it floods the ARP request to all ports except the one it was received on. Upon receiving the request, the router acting as the local gateway responds with its MAC address. The switch updates its MAC table and forwards the ARP reply to my machine. Now armed with both the L3 destination IP (the DNS server) and its L2 destination MAC (the gateway), my machine crafts a UDP packet with destination port 53 (for DNS) and sends it to the DNS server. Upon receiving this request, the DNS server replies with the IP address associated with "google.com," equipping my PC with the correct IP to communicate with.

- TCP Connection Setup
With the correct IP address of "google.com" in hand, my PC proceeds to establish a TCP connection. It creates a TCP segment with the destination port set to 443 (for HTTPS) to initiate communication with the server. The first step is the three-way handshake, essential for establishing a reliable connection. This TCP segment is then encapsulated within an IP header for routing on the network towards "google.com."

- L3 Routing
The prepared packet now reaches the default gateway, which consults its Forwarding Information Base (FIB) which is populated by the control plane table (RIB) to determine where to forward the packet. This process repeats across multiple routers until the packet reaches its destination on the internet, eventually reaching Google's servers. The response from Google's server follows the reverse path until it reaches my PC.

- HTTPS Connection Setup
Once the TCP connection is established, the HTTPS connection setup begins. This involves initiating a TLS handshake for secure communication. During the handshake, encryption keys are exchanged, and the server's identity is verified through its certificate.

- Secure Exchange of Data
With the TLS handshake completed, my PC and the server can securely exchange data. This includes HTTP requests and responses, all encrypted to ensure privacy and security during transmission.







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

