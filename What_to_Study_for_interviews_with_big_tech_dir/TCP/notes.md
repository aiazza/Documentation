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
## TCP header (size / fields) , MSS
Let's take a look at the TCP header, it's fields and what everything means : 
- Source Port : This is the field that specifies the port number of sender.
- Destination port : This is the field that specifies the port number of receiver.
- Sequence number : In TCP, each segment is assigned with a sequence number, this is to keep track of all segments and in which order they have been sent, it's also used to know if the receiver has received the segment.
- Acknowledgment number : This field is used to acknowledge a segment and request the next TCP segment, specifically setting the ack number to the next byte it should receive. Example, if a receiver has successfully received a segment with sequence numbers 1 to 1000, the acknowledgment number sent back to the sender will be 1001.
- Data offset (DO) : This is also known as header lenght, this is used to to indicate the lenght of TCP header so we know where the data actually begins.
- RSV : This is the reserved field, it's not used and always set to 0.
- Flags : We will discuss flags in the next section.
- Window : This specifies the maximum amount of data the receiver can receive before needing to send an ack, it's called the receive window (RCWD), this is used so the sender does not send more data than the receivers buffer can handle, avoiding buffer overflow.
- Checksum : Checksum to see if the TCP header is ok or not
- Urgent Pointer : if the urgent flag is set, this pointer specifies where the urgent portion of data ends, so that the receiver treats the data up to this point as urgent.
- Options : Optional field that can be used to extend TCP capabilities beyond the basic header structure.
![alt text](https://cdn.networklessons.com/wp-content/uploads/2015/07/tcp-header.png)

## Flags
In TCP flags are pieces of information turned on or off in the header to indicate a particular state or give information on how to handle a connection, the flags are the following :
- Synchronization (SYN) : As discussed above this is used to initiate a TCP connection. This is used for sender and receiver to synchronize sequence number as well as announce different capabilities specific to the host (Maximum Segment Size (MSS) , Window Scale option,  Selective Acknowledgment (SACK) etc.)
- Ack : Used to acknowledge TCP segments when they are received by the host and communicate next expected sequence number.
- Finish (FIN) : Used to terminate a TCP connection, when there is no more data from the sender for example. This is graceful agreeing to stop the connection.
- Reset (RST) : Used to terminate a TCP connection if something is wrong, like when the sender receives an unexpected message from the receiver. This is ungraceful way of stoping the connection, data is discarded in this scenario. 
- Urgent (URG) : Used to indicate to the receiver that data contained in the packet should be handled with higher priority than other packets.
- Push (PSH) : Used to request immediate delivery to the receiver without waiting for buffering, commonly used in video streaming or audio
- Checksum (CHK) : Used to verify integrity of TCP Segment.
  
## Congestion control (CWND)
After completing the 3-way handshake TCP know how much data it cannot exceed before receiving an ACK to not overload the receivers buffer, but what about the network ? How do we know that the rate at which we are sending data is not exceeding the networks transmission capabilities ? Well TCP has a built-in mechanism for that called Congestion control, let's see how it works. 
- How does TCP Congestion Control work :

TCP tries to detect and mitigate congestion by probing how much the network can handle, it does that by increasing the congestion window (CWND) every round trip until loss is detected. Loss is detected when not receiving ACK or receiving duplicate ACK's etc. Once loss thus congestion is detected it cuts the CWND in half to mitigate the congestion. If we want to visualize it it looks something like this :

![alt text](https://i.ytimg.com/vi/cPLDaypKQkU/maxresdefault.jpg)

There are multiple phases in TCP congestion control, here are the various phases : 

- Slow start : In this phase TCP starts with an initial CWND of 1 MSS and doubles the CWND, increasing it exponantially, for every ACK received. So first the host will send 1 Segment , then 2 then 4 then 8 then 16 etc. This will happen until either loss is detected or the slow start threshhold (**ssthresh**) is met, after which we cut the CWND in half and then pass to congestion avoidance phase.  This phase is called slow start because it starts slowly but ramps up very fast to detect whats the networks limit. Loss can be detected by timeout or duplicate ACKs depending on TCP version.
- Congestion avoidance : In the congestion avoidance mode, the CWND increases linearly, meaning for each ACK it increases the CWND by 1.
- Fast recovery : Fast Recovery serves as a midpoint between the aggressive Slow Start and the conservative Congestion Avoidance phases. It is triggered upon receiving three duplicate ACKs, which indicate packet loss. The primary aim of Fast Recovery is to recover from this loss without significantly reducing the CWND. After detecting packet loss through duplicate ACKs, TCP Fast Recovery moderately reduces the CWND (not as drastically as in Slow Start), retransmits the lost packet, then increases the CWND incrementally for each received duplicate ACK thereafter. The process transitions back to Congestion Avoidance once the lost packet is successfully acknowledged, striving to maintain efficient throughput while minimizing further congestion.

## Window scaling
In TCP header the window field  specifies the maximum amount of data the receiver can receive before needing to send an ACK. This field is only 2 bytes which means the maximum window size is 65535. When TCP was designed this size was ok for the hosts at that time, but now we have much more capable systems and much larger buffers, so how to tell TCP that our window is bigger than 65535 if we only have 2 bytes ? By using window scaling. Windows scaling makes use of the TCP options field to define a scale factor. The scale factore is by how much the scaling window should be scaled.   Example :Let say client sends SYN packet to server and says that its scaling factor = 2 and receives scaling factor of receiver in ‘SYN+ACK’ packet from receiver.Now in next packet sender tells its window size = 200 bytes. Then Server will calculate the real buffer size available at client as: window_size * 2^2 = 800bytes

## Selective Ack
When communicating between hosts what can happen is that we lose a packet in-flight, let's illustrate for visualization purposes. Let's say you have host A communicating with host B, host A send segment 1,2,3 and segment 4 is lost in flight, then host A send segment 5,6,7. Host B will send the ACK for segment 3 with sequence number 4 and keep sending that ACK for each received segment until host A retransmits the segment 4. After retrasmitting segment 4, host A will have to retransmit segment 5,6,7 as well as there is no tracking of what was received and what was not. That's where selective ACK comes into play. SACK allows Host B to acknowledge non-contiguous blocks of data that have been successfully received. So, even if segment 4 is lost, Host B can still inform Host A that it has received segments 5, 6, and 7. When Host A receives the SACK for segments 5, 6, and 7, it knows exactly which segment was lost (segment 4) and only needs to retransmit that specific segment.

- Without SACK 
```
Host A (Sender)                                            Host B (Receiver)
   |                                                         |
   | -- Segment 1 (Seq: 1) ------------------------------->  |
   | -- Segment 2 (Seq: 2) ------------------------------->  | ACK 2 (Expecting Seq: 3)
   | -- Segment 3 (Seq: 3) ------------------------------->  | ACK 3 (Expecting Seq: 4)
   | -- Segment 4 (Seq: 4) ---X (Lost in Transit)            |
   | -- Segment 5 (Seq: 5) ------------------------------->  | ACK 3 (Still expecting Seq: 4)
   | -- Segment 6 (Seq: 6) ------------------------------->  | ACK 3 (Still expecting Seq: 4)
   | -- Segment 7 (Seq: 7) ------------------------------->  | ACK 3 (Still expecting Seq: 4)
   |                                                         |
   | <Notices repeated ACK for 3, retransmits 4>             |
   | -- Segment 4 (Seq: 4) ------------------------------->  | ACK 4 (Expecting Seq: 5)
   | <Need to retransmit 5, 6, 7>                    |
   | -- Segment 5 (Seq: 5) ------------------------------->  | ACK 5 (Expecting Seq: 6)
   | -- Segment 6 (Seq: 6) ------------------------------->  | ACK 6 (Expecting Seq: 7)
   | -- Segment 7 (Seq: 7) ------------------------------->  | ACK 7 (Expecting Seq: 8)
   |                                                         |
```
- With SACK

```
Host A (Sender)                                            Host B (Receiver)
   |                                                         |
   | -- Segment 1 (Seq: 1) ------------------------------->  |
   | -- Segment 2 (Seq: 2) ------------------------------->  | ACK 2 (Expecting Seq: 3)
   | -- Segment 3 (Seq: 3) ------------------------------->  | ACK 3 (Expecting Seq: 4)
   | -- Segment 4 (Seq: 4) ---X (Lost in Transit)            |
   | -- Segment 5 (Seq: 5) ------------------------------->  | ACK 3 (Expecting Seq: 4, SACK 5)
   | -- Segment 6 (Seq: 6) ------------------------------->  | ACK 3 (Expecting Seq: 4, SACK 5-6)
   | -- Segment 7 (Seq: 7) ------------------------------->  | ACK 3 (Expecting Seq: 4, SACK 5-7)
   |                                                         |  
   | <Receives SACK for 5-7, knows 4 is missing>             |
   | -- Segment 4 (Seq: 4) ------------------------------->  | ACK 3 (Expecting Seq: 8, SACK 5-7)
   |                                                         |
   | <No need to retransmit 5, 6, 7 due to SACK>             |
   |                                                         |  

```

## Different implementations of TCP

