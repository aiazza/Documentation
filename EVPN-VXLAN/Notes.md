## VxLAN

### What is VxLAN?
VXLAN stands for Virtual Extensible LAN and is a protocol that allows the tunneling of Layer 2 (L2) frames over Layer 3 (L3) networks. In the context of data centers (DC), VXLAN is analogous to what MPLS is for service providers. Traditionally, to enable communication between hosts in the same VLAN through different devices, VLANs had to be extended using trunks. Modern large-scale networks often remove Layer 2 from the access or leaf layer to avoid Spanning Tree Protocol issues that block links and prevent full bandwidth utilization and traffic load balancing across all paths. However, some applications still require being in the same L2 domain, necessitating a method to transport L2 information over L3. Traditional VLANs are limited to 4094 usable IDs due to only 12 bits being reserved for them. This limitation is insufficient for modern applications that may require hundreds of thousands of VLANs. VXLAN addresses this by reserving 24 bits for the VXLAN Network Identifier (VNI), similar to a VLAN ID, allowing for up to 16,777,215 segments. This capacity is crucial for large-scale application usage, where the solution often involves **encapsulation**.

### How does it work?
VXLAN operates through devices known as VXLAN Tunnel Endpoints (VTEPs), which create virtual L2 segments called VNIs (Virtual Network Identifiers) that carry L2 over L3. VTEPs can be implemented in software (e.g., on ESXi hypervisors, using the network as an underlay) or hardware (utilizing network devices, which can be faster due to ASICs). Each VTEP has two interfaces:
- **VTEP IP Interface**: Connects the VTEP to the underlay network with a unique IP address used for encapsulating and de-encapsulating traffic.
- **VNI Interface**: Similar to an SVI interface, it keeps network traffic separated on the physical interface and functions as the gateway for hosts within that specific L2 segment identified by the VNI.

![VxLAN Diagram](https://cdn.networklessons.com/wp-content/uploads/2020/02/vxlan-nvi-vtep-ip-interfaces.png)

When a host sends traffic to another host in the same L2 segment, the traffic reaches the VTEP. The VTEP checks its mapping table for the corresponding remote VTEP IP address. It then encapsulates the original Ethernet frame into a VXLAN packet, which includes a new Ethernet header, VXLAN header, UDP header, and outer IP header designed for the overlay network.

### How does VXLAN learn which remote MAC is associated with which remote VTEP?
Traditional VLAN operations involve switches receiving an ARP request, flooding it out of all ports except the incoming one, and forwarding the responding host's reply back to the original requester, saving the mapping in its ARP table. VXLAN employs a similar concept with a table mapping each destination MAC to a remote VTEP IP. Learning these mappings can be achieved in several ways, including through the **multicast underlay** approach.

#### Multicast "Flood and Learn" in VXLAN

In VXLAN, multicast can be utilized to facilitate the mapping of remote MAC addresses to VTEPs using a "flood and learn" approach. Here’s how the process works:

- **Joining Multicast Groups**: Each VNI and the associated VTEP devices join a specific multicast group.
- **Handling ARP Requests**: When a VTEP receives an ARP request from one of its locally attached hosts, it encapsulates this ARP request in a VXLAN packet. This packet is then sent to the multicast group associated with the VNI.
- **Receiving and Learning**: All other VTEPs that are part of this multicast group receive the multicast VXLAN packet. Upon decapsulation, they extract the ARP request, learn the MAC address of the requester, and store this information in their mapping table.
- **Handling ARP Replies**: Similarly, when these VTEPs receive an ARP reply, it is also encapsulated and sent to the multicast group. Upon receiving and decapsulating the ARP reply, they update their mapping tables with the responder’s MAC address and corresponding VTEP IP.

This method enables each VTEP to dynamically learn and store the necessary MAC-to-IP mappings for Layer 2 forwarding within the VXLAN network, without requiring pre-configuration of these mappings.
