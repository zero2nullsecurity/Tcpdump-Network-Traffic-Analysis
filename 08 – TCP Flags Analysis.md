# 08 – TCP Flags Analysis

## 1. Introduction

In previous modules, we learned how to capture network traffic and filter packets using Tcpdump.

Now, we will learn about **TCP Flags Analysis**.

TCP flags are important fields inside the TCP header. They help devices control and manage TCP connections.

Whenever two systems communicate using TCP, they exchange packets. These packets contain different flags that tell the receiving system what action is expected.

For example:

- SYN is used to start a TCP connection.
- ACK is used to acknowledge received data.
- FIN is normally used to close a TCP connection.
- RST is used to reset a TCP connection.
- PSH indicates that data should be pushed to the receiving application.
- URG indicates that urgent data is present.

By analyzing TCP flags, we can understand how connections are started, maintained, and closed.

This is useful for:

- Network troubleshooting.
- SOC alert investigation.
- Firewall analysis.
- Detecting unusual connection attempts.
- Understanding port scanning activity.
- Investigating unexpected TCP connection resets.

In this module, we will learn how TCP flags work and how to analyze them using Tcpdump.

## 2. What Is TCP?

TCP stands for **Transmission Control Protocol**.

It is a transport-layer protocol used to provide reliable communication between two systems over an IP network.

TCP is connection-oriented. This means that two systems normally establish a connection before exchanging application data.

TCP provides features such as:

- Connection establishment.
- Reliable data delivery.
- Data sequencing.
- Acknowledgment of received data.
- Retransmission of lost data.
- Flow control.
- Connection termination.

### Example

Imagine that you open a website in your browser.

Your computer needs to communicate with the website's server.

Before exchanging application data over a TCP connection, the two systems normally perform a process called the **TCP Three-Way Handshake**.

During this process, TCP flags help both systems establish the connection.

## 3. What Are TCP Flags?

TCP flags are control bits inside the TCP header.

They tell the receiving system how to handle a TCP segment.

A TCP header contains several fields, including:

- Source port.
- Destination port.
- Sequence number.
- Acknowledgment number.
- Header length.
- Flags.
- Window size.
- Checksum.
- Urgent pointer.

TCP flags are part of the control information used during communication.

### Why are TCP flags important?

Imagine that a computer wants to establish a connection with a web server.

The first computer sends a TCP packet with the SYN flag set.

The server responds with SYN and ACK flags.

The first computer then sends an ACK packet.

This process establishes the TCP connection.

Without these control flags, TCP would not be able to manage its connection in the same way.

## 4. Types of TCP Flags

TCP has several control flags.

The commonly discussed TCP flags are:

| Flag | Full Name | Purpose |
|---|---|---|
| SYN | Synchronize | Used to initiate a TCP connection and synchronize sequence numbers |
| ACK | Acknowledgment | Indicates that the acknowledgment field is valid |
| FIN | Finish | Used to indicate that the sender has finished sending data |
| RST | Reset | Used to reset or immediately terminate a TCP connection |
| PSH | Push | Requests that data be pushed to the receiving application |
| URG | Urgent | Indicates that the urgent pointer field is significant |
| ECE | ECN-Echo | Used for Explicit Congestion Notification |
| CWR | Congestion Window Reduced | Indicates that the sender has reduced its congestion window |
| NS | Nonce Sum | An older ECN-related control bit |

The first six flags are especially useful when learning basic TCP connection behavior.

Modern TCP headers also include the ECE and CWR flags. The NS bit is an additional control bit defined in the TCP header.

Let's understand the main flags one by one.

## 5. SYN Flag – Synchronize

The SYN flag is used when establishing a TCP connection.

When one system wants to communicate with another system using TCP, it normally sends a packet with the SYN flag set.

This tells the receiving system that the sender wants to establish a TCP connection.

### Example

Imagine your computer wants to connect to a web server.

Your computer sends:

```text
Source IP:      192.168.1.10
Destination IP: 93.184.216.34
Source Port:    51500
Destination:    443
TCP Flag:       SYN
```

This is an example of a TCP connection request.

The source port is an example of an ephemeral client port. The destination port 443 is commonly used for HTTPS.

### What happens next?

If the server accepts the connection request, it normally responds with a packet containing both SYN and ACK flags.

This is the second step of the TCP three-way handshake.

### Tcpdump command

To capture TCP SYN packets:

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-syn != 0'
```

**Command breakdown:**

- `sudo` – Runs Tcpdump with the required permissions.
- `tcpdump` – Starts the packet capture tool.
- `-i eth0` – Selects the network interface.
- `tcp` – Selects TCP packets.
- `[tcpflags]` – Accesses the TCP flags field.
- `& tcp-syn` – Checks the SYN flag using a bitwise AND operation.
- `!= 0` – Matches packets where the SYN flag is set.

This filter also matches SYN-ACK packets because they contain the SYN flag.

To identify initial SYN packets without ACK, use the more specific filter shown in Section 8.

## 6. ACK Flag – Acknowledgment

ACK stands for Acknowledgment.

It is used to acknowledge received data or control information in TCP communication.

When a system receives TCP data, it can send an acknowledgment to tell the other system how much data has been received successfully.

The acknowledgment number indicates the next sequence number expected by the receiver.

### Example

Imagine System A sends data to System B.

System B receives the data and sends an ACK packet.

This tells System A that the data has been acknowledged.

### Important points

- ACK is used during TCP connection establishment after the initial SYN.
- ACK is normally present in established TCP connections.
- It is used to acknowledge received data.
- The acknowledgment number helps track the data received.

### Tcpdump command

To capture TCP packets where the ACK flag is set:

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-ack != 0'
```

This displays TCP packets containing the ACK flag.

**Important:** ACK does not always mean that an application message has been successfully processed. It acknowledges TCP sequence space, not necessarily application-level success.

## 7. FIN Flag – Finish

The FIN flag is used when a system wants to close its side of a TCP connection.

When an application finishes sending data, TCP can use FIN to indicate that no more data will be sent from that side.

The other system can acknowledge the FIN.

A normal TCP connection termination commonly involves four packets.

### Example

Imagine that you finish using a website and your browser closes the TCP connection.

The systems exchange FIN and ACK packets to close the connection.

### Tcpdump command

To capture TCP FIN packets:

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-fin != 0'
```

This displays TCP packets where the FIN flag is set.

### Important points

- FIN is used for normal TCP connection termination.
- It indicates that the sender has finished sending data.
- The other side can continue sending data until it also closes its side.
- Both directions of a TCP connection are closed independently.

## 8. RST Flag – Reset

RST stands for Reset.

It is used to reset a TCP connection.

Unlike FIN, which is commonly used for an orderly connection shutdown, RST indicates that the connection should be reset.

### Example

Imagine that a computer tries to connect to a TCP port where no application is listening.

The target system may respond with a TCP packet containing the RST flag.

This indicates that the connection cannot be established in the requested way.

RST packets can also occur for other reasons, such as an application closing a connection unexpectedly or a system receiving an invalid TCP segment.

### Tcpdump command

To capture TCP RST packets:

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-rst != 0'
```

This displays packets where the RST flag is set.

### Why is RST analysis useful?

Security analysts and network administrators can use RST packets to investigate:

- Unexpected connection termination.
- Applications refusing connections.
- Firewall-related connection behavior.
- Network troubleshooting problems.
- Unusual TCP communication patterns.

**Important:** A TCP RST packet is not automatically evidence of an attack. It can be part of normal network behavior.

## 9. PSH Flag – Push

PSH stands for Push.

It is used to indicate that the receiving TCP implementation should push received data to the application without waiting for additional buffering.

In simple words, it requests that the data be delivered to the receiving application promptly.

### Example

Imagine that a client is communicating with a server.

The client sends application data, and the TCP segment has the PSH flag set.

The receiving system is requested to push the received data to the application.

### Tcpdump command

To capture TCP packets containing the PSH flag:

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-push != 0'
```

This displays TCP packets where the PSH flag is set.

### Important points

- PSH is a TCP control flag.
- It is associated with the delivery of received data to the application.
- It does not mean that the data is encrypted or unencrypted.
- It does not automatically indicate suspicious activity.

## 10. URG Flag – Urgent

URG stands for Urgent.

It indicates that the urgent pointer field in the TCP header is significant.

The urgent pointer identifies the end of the urgent data in the TCP sequence space.

This feature is part of TCP's older urgent-data mechanism.

### Example

A TCP sender may set the URG flag when it wants the receiver to treat a particular portion of data as urgent according to the TCP urgent mechanism.

### Tcpdump command

To capture TCP packets containing the URG flag:

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-urg != 0'
```

This displays TCP packets where the URG flag is set.

### Important points

- URG indicates that the urgent pointer is significant.
- It is not commonly used by most modern applications.
- Its meaning depends on the TCP urgent-data mechanism.
- Its presence alone does not prove malicious activity.

## 11. Understanding ECE and CWR Flags

Modern TCP also includes flags related to network congestion.

### 11.1 ECE – ECN-Echo

ECE stands for ECN-Echo.

It is used with Explicit Congestion Notification (ECN).

ECN allows network devices to signal congestion without necessarily dropping packets.

During connection setup, ECE can indicate that the sender supports ECN.

During communication, it can also be used to report congestion information.

### 11.2 CWR – Congestion Window Reduced

CWR stands for Congestion Window Reduced.

It is used by a sender to indicate that it has reduced its congestion window after receiving congestion information.

### Why are these flags useful?

ECE and CWR help TCP systems respond to network congestion.

They are useful when investigating:

- Network congestion.
- TCP performance problems.
- Packet loss.
- Communication delays.

They are not normally the first flags beginners need to analyze, but understanding them gives a more complete picture of TCP.

## 12. TCP Three-Way Handshake

The TCP three-way handshake is the process used to establish a TCP connection.

It involves three steps:

1. SYN
2. SYN-ACK
3. ACK

Let's understand each step.

### Step 1: SYN

The client sends a TCP packet to the server with the SYN flag set.

This requests a TCP connection and provides the client's initial sequence number.

```text
Client  ---------------- SYN ---------------->  Server
```

### Step 2: SYN-ACK

The server receives the SYN packet and responds with a packet containing SYN and ACK flags.

This acknowledges the client's SYN and provides the server's initial sequence number.

```text
Client  <------------- SYN + ACK -------------  Server
```

### Step 3: ACK

The client sends an ACK packet to acknowledge the server's SYN.

```text
Client  ---------------- ACK ---------------->  Server
```

After this process, the TCP connection is established.

### Complete three-way handshake

```text
        CLIENT                         SERVER
           |                              |
           |                              |
           | -------- SYN --------------> |
           |                              |
           | <----- SYN + ACK ----------- |
           |                              |
           | -------- ACK --------------> |
           |                              |
           |                              |
           |     CONNECTION ESTABLISHED   |
```

### Why is the handshake important?

The handshake helps both systems:

- Confirm that the other system is reachable.
- Synchronize initial sequence numbers.
- Establish connection state.
- Prepare for reliable data communication.

## 13. Understanding TCP Four-Way Connection Termination

After communication is complete, a TCP connection is normally closed using FIN and ACK packets.

This is commonly explained as a four-way termination process.

### Step 1: First FIN

System A sends a FIN packet to indicate that it has finished sending data.

### Step 2: ACK

System B acknowledges the FIN packet.

### Step 3: Second FIN

When System B has also finished sending data, it sends its own FIN packet.

### Step 4: Final ACK

System A acknowledges the second FIN.

After the required TCP state transitions, the connection is closed.

### Diagram

```text
        SYSTEM A                       SYSTEM B
            |                              |
            |                              |
            | -------- FIN -------------> |
            |                              |
            | <------- ACK -------------- |
            |                              |
            | <------- FIN -------------- |
            |                              |
            | -------- ACK -------------> |
            |                              |
            |       CONNECTION CLOSED     |
```

### Why does TCP use FIN?

TCP supports communication in both directions.

Each direction can be closed independently.

This is why one system may send FIN while the other system still has data to send.

**Note:** The four-packet exchange is the common teaching example. In real traffic, ACK and FIN can sometimes be combined, so the number of observed packets may differ.

## 14. How to Identify TCP Flags in Tcpdump Output

Tcpdump can display TCP flags directly in packet output.

For example:

```bash
sudo tcpdump -i eth0 -nn -vv tcp
```

This displays detailed TCP packet information.

### Example output

The following output is illustrative:

```text
IP 192.168.1.10.51500 > 93.184.216.34.443:
Flags [S], seq 123456789, win 64240, length 0

IP 93.184.216.34.443 > 192.168.1.10.51500:
Flags [S.], seq 987654321, ack 123456790, win 65160, length 0

IP 192.168.1.10.51500 > 93.184.216.34.443:
Flags [.], ack 987654322, win 64240, length 0
```

### Understanding the flag symbols

Tcpdump commonly uses these symbols to represent TCP flags:

| Symbol | Meaning |
|---|---|
| `S` | SYN |
| `.` | ACK |
| `F` | FIN |
| `R` | RST |
| `P` | PSH |
| `U` | URG |
| `E` | ECE |
| `W` | CWR |

### Understanding the example

**First packet:**

```text
Flags [S]
```

This means the SYN flag is set.

The client is requesting a TCP connection.

**Second packet:**

```text
Flags [S.]
```

This means both SYN and ACK are set.

The server is acknowledging the client's request and sending its own SYN.

**Third packet:**

```text
Flags [.]
```

This means the ACK flag is set.

The client acknowledges the server's SYN.

These three packets represent the TCP three-way handshake.

## 15. Filter Specific TCP Flags Using Tcpdump

Now let's learn how to capture individual TCP flags.

### 15.1 Capture SYN packets

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-syn != 0'
```

This captures packets where the SYN flag is set.

### 15.2 Capture initial SYN packets without ACK

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn'
```

This matches packets where SYN is set and ACK is not set.

These commonly represent initial TCP connection requests.

### 15.3 Capture SYN-ACK packets

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & (tcp-syn|tcp-ack) == (tcp-syn|tcp-ack)'
```

This matches packets where both SYN and ACK are set.

### 15.4 Capture ACK packets

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-ack != 0'
```

This captures packets where the ACK flag is set.

### 15.5 Capture FIN packets

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-fin != 0'
```

This captures packets where the FIN flag is set.

### 15.6 Capture RST packets

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-rst != 0'
```

This captures packets where the RST flag is set.

### 15.7 Capture PSH packets

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-push != 0'
```

This captures packets where the PSH flag is set.

### 15.8 Capture URG packets

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-urg != 0'
```

This captures packets where the URG flag is set.

**Important:** These filters check whether a flag is set. They do not necessarily require that it is the only flag present.

## 16. Understanding the TCP SYN Scan

A TCP SYN scan is a port-scanning technique commonly associated with network security testing.

It sends TCP SYN packets to target ports and observes the responses.

This technique is also known as a half-open scan.

### How does it work?

Imagine an authorized security tester wants to check whether a TCP port is open.

The scanner sends a SYN packet to the target.

The response can help identify the port state.

| Target Response | Possible Interpretation |
|---|---|
| SYN-ACK | Port is likely open |
| RST | Port is likely closed |
| No response | Port may be filtered or the packet may have been lost |
| ICMP error | A network device may be blocking or rejecting the traffic |

### Example

An authorized tester runs a SYN scan against a system they are permitted to assess.

They can observe the traffic using Tcpdump:

```bash
sudo tcpdump -i eth0 -nn 'tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn'
```

This displays initial SYN packets without ACK.

### Why is this important for cybersecurity?

A large number of SYN packets sent to different destination ports may indicate port-scanning activity.

However, SYN packets are also normal during everyday network communication.

A security analyst should consider:

- Source IP address.
- Destination IP address.
- Number of destination ports.
- Frequency of connection attempts.
- Responses from the target.
- Whether the activity is expected.

**Important:** Only perform port scanning against systems you own or have explicit permission to test.

## 17. Detecting Unusual TCP Behavior

TCP flag analysis can help identify unusual communication patterns.

Let's understand a few examples.

### 17.1 Repeated SYN packets

If a system repeatedly sends SYN packets but does not receive a response, possible explanations include:

- The target system is unavailable.
- A firewall is dropping packets.
- The destination port is filtered.
- Network connectivity is failing.
- A connection attempt is being repeated.

Tcpdump filter:

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn'
```

### 17.2 Repeated RST packets

A large number of RST packets may indicate:

- Connections to closed ports.
- Applications terminating connections.
- Firewall or security-device behavior.
- Unexpected network activity.

Tcpdump filter:

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-rst != 0'
```

### 17.3 Unexpected FIN packets

FIN packets are normally used to close TCP connections.

However, unexpected or unusual connection termination patterns may require investigation.

Tcpdump filter:

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-fin != 0'
```

### 17.4 Unusual combinations of TCP flags

A TCP packet may contain multiple flags at the same time.

For example, a packet with an unusual combination of control flags may deserve closer inspection.

However, unusual flag combinations are not automatically malicious.

Analysts should examine the full packet, communication context, and endpoint behavior before reaching a conclusion.

## 18. Practical Lab – TCP Flags Analysis

Now let's perform a practical lab using Tcpdump.

We will capture TCP traffic, generate a connection, and observe different TCP flags.

### Lab requirements

- Kali Linux or Ubuntu.
- Tcpdump installed.
- Terminal access.
- Internet connection.
- A browser or `curl` command.

### Step 1: Identify your network interface

```bash
ip a
```

Find the active network interface.

You can also use:

```bash
sudo tcpdump -D
```

### Step 2: Start capturing TCP traffic

Create a directory:

```bash
mkdir -p ~/tcpdump-lab/tcp-flags
cd ~/tcpdump-lab/tcp-flags
```

Start the capture:

```bash
sudo tcpdump -i any -nn -c 100 -w tcp-flags-lab.pcap tcp
```

This captures up to 100 TCP packets and saves them into a PCAP file.

### Step 3: Generate TCP traffic

While Tcpdump is capturing, open another terminal and run:

```bash
curl -I https://example.com
```

This sends an HTTP HEAD request over HTTPS, which normally uses TCP port 443 unless the connection uses a different transport.

The command may reuse an existing connection depending on the environment. A fresh connection is more useful for observing a new handshake.

### Step 4: Read the capture file

After the capture finishes, run:

```bash
sudo tcpdump -nn -vv -r tcp-flags-lab.pcap
```

Look for the `Flags` field in the output.

### Step 5: Find SYN packets

```bash
sudo tcpdump -nn -r tcp-flags-lab.pcap 'tcp[tcpflags] & tcp-syn != 0'
```

Observe the packets where the SYN flag is set.

### Step 6: Find initial SYN packets

```bash
sudo tcpdump -nn -r tcp-flags-lab.pcap 'tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn'
```

This filters SYN packets without ACK.

### Step 7: Find SYN-ACK packets

```bash
sudo tcpdump -nn -r tcp-flags-lab.pcap 'tcp[tcpflags] & (tcp-syn|tcp-ack) == (tcp-syn|tcp-ack)'
```

Observe the response packets.

### Step 8: Find FIN packets

```bash
sudo tcpdump -nn -r tcp-flags-lab.pcap 'tcp[tcpflags] & tcp-fin != 0'
```

Look for packets used during connection termination.

### Step 9: Find RST packets

```bash
sudo tcpdump -nn -r tcp-flags-lab.pcap 'tcp[tcpflags] & tcp-rst != 0'
```

Check whether any connections were reset.

### Step 10: Analyze the complete connection

Review the packets in order.

Try to identify:

1. The initial SYN packet.
2. The SYN-ACK response.
3. The final ACK packet.
4. Data transfer packets.
5. FIN or RST packets, if present.

**Lab result:** You have captured TCP traffic and learned how to identify connection establishment, data communication, and connection termination using TCP flags.

**Note:** Your capture may not contain every flag type. For example, RST or URG packets may not appear during a normal HTTPS request. That is expected.

## 19. Real-World Use Cases

TCP flag analysis is useful in network security and troubleshooting.

### 19.1 SOC Analyst – Investigating Port Scans

A SOC analyst receives an alert about a system sending a large number of TCP connection requests.

The analyst checks SYN packets and investigates:

- Source IP address.
- Destination IP addresses.
- Destination ports.
- Number of connection attempts.
- Responses from target systems.

This can help determine whether the activity is normal or potentially related to port scanning.

### 19.2 Network Administrator – Troubleshooting Connections

A user reports that an application cannot connect to a server.

The administrator examines TCP flags to understand:

- Whether the client is sending SYN packets.
- Whether the server is responding with SYN-ACK.
- Whether the connection is being reset.
- Whether the handshake completes.

This helps identify where the connection process may be failing.

### 19.3 Incident Response – Investigating Connection Resets

During an incident, analysts may notice unexpected connection termination.

They can filter RST packets and examine the systems involved.

This can help investigate application failures, firewall behavior, and unusual network activity.

### 19.4 Penetration Testing – Understanding TCP Scans

During an authorized penetration test, a tester can analyze TCP flags to understand how scanning tools communicate with target systems.

This helps explain why SYN, SYN-ACK, and RST responses are important when identifying port states.

## 20. Common Mistakes During TCP Flags Analysis

| Mistake | Explanation | Solution |
|---|---|---|
| Confusing SYN with SYN-ACK | Both packets contain SYN, but SYN-ACK also contains ACK | Check the complete flag combination |
| Assuming every SYN is malicious | SYN packets are normal during TCP connection establishment | Examine the source, destination, and frequency |
| Confusing FIN with RST | FIN normally closes a connection orderly; RST resets it | Understand their different purposes |
| Assuming ACK means application success | ACK acknowledges TCP sequence space | Check application-level behavior separately |
| Expecting every flag in one capture | Normal traffic may not use every flag | Generate appropriate test traffic |
| Ignoring packet order | TCP communication is stateful | Analyze packets in sequence |
| Using an incorrect filter | The filter may match more packets than intended | Check the TCP flag expression |

## 21. Quick Command Reference

| Command | Purpose |
|---|---|
| `tcpdump 'tcp[tcpflags] & tcp-syn != 0'` | Capture packets with SYN set |
| `tcpdump 'tcp[tcpflags] & tcp-ack != 0'` | Capture packets with ACK set |
| `tcpdump 'tcp[tcpflags] & tcp-fin != 0'` | Capture packets with FIN set |
| `tcpdump 'tcp[tcpflags] & tcp-rst != 0'` | Capture packets with RST set |
| `tcpdump 'tcp[tcpflags] & tcp-push != 0'` | Capture packets with PSH set |
| `tcpdump 'tcp[tcpflags] & tcp-urg != 0'` | Capture packets with URG set |
| `tcpdump 'tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn'` | Capture initial SYN packets without ACK |
| `tcpdump 'tcp[tcpflags] & (tcp-syn|tcp-ack) == (tcp-syn|tcp-ack)'` | Capture SYN-ACK packets |
| `tcpdump -nn -vv tcp` | Display detailed TCP packet information |
| `tcpdump -nn -r sample.pcap tcp` | Read TCP packets from a PCAP file |

*Note: Add `-i eth0` for live capture or `-r sample.pcap` for saved-file analysis.*

## 22. Practice Questions

Try answering these questions after completing the module.

1. What are TCP flags?
2. Why does TCP use flags?
3. What is the purpose of the SYN flag?
4. What is the purpose of the ACK flag?
5. What is the difference between FIN and RST?
6. What does the PSH flag indicate?
7. What is the purpose of the URG flag?
8. What are ECE and CWR?
9. What happens during the TCP three-way handshake?
10. Why is TCP connection termination commonly explained as a four-way process?
11. What does `Flags [S.]` mean in Tcpdump output?
12. How can you identify initial SYN packets using Tcpdump?
13. What is a TCP SYN scan?
14. Why can repeated SYN packets be important during a security investigation?
15. How can TCP flag analysis help a SOC analyst?

## 23. Practical Tasks

Complete these tasks using your own lab capture file.

- [ ] Capture TCP traffic using Tcpdump.
- [ ] Read TCP packets from a PCAP file.
- [ ] Identify SYN packets.
- [ ] Identify SYN packets without ACK.
- [ ] Identify SYN-ACK packets.
- [ ] Identify ACK packets.
- [ ] Identify FIN packets.
- [ ] Identify RST packets.
- [ ] Identify PSH packets.
- [ ] Identify URG packets, if present.
- [ ] Observe the TCP three-way handshake.
- [ ] Observe TCP connection termination, if present.
- [ ] Compare different TCP flag combinations.
- [ ] Explain what the observed packets tell you about the connection.

## 24. Key Takeaways

In this module, we learned how TCP flags control TCP communication and how to analyze them using Tcpdump.

We covered:

- Understanding TCP and TCP headers.
- Learning the purpose of TCP flags.
- Understanding SYN, ACK, FIN, RST, PSH, and URG.
- Learning about ECE and CWR.
- Understanding the TCP three-way handshake.
- Understanding the common four-way connection termination process.
- Reading TCP flags in Tcpdump output.
- Filtering specific TCP flags.
- Understanding TCP SYN scans.
- Investigating unusual TCP connection behavior.
- Applying TCP flag analysis during SOC investigations and network troubleshooting.

**Remember:** TCP flags provide important information about connection behavior. By understanding them, you can identify how a TCP connection starts, how data is acknowledged, and how the connection is closed or reset.

## 25. References and Further Learning

- [Tcpdump Official Website](https://www.tcpdump.org/)
- [Tcpdump Manual Page](https://www.tcpdump.org/manpages/tcpdump.1.html)
- [PCAP Filter Manual](https://www.tcpdump.org/manpages/pcap-filter.7.html)
- [RFC 9293 – Transmission Control Protocol](https://www.rfc-editor.org/rfc/rfc9293)
- [Wireshark Documentation](https://www.wireshark.org/docs/)
- [TryHackMe](https://tryhackme.com/)

---

**Next Module: 09 – Displaying Packet Contents**

In the next module, we will learn how to inspect packet contents using Tcpdump, understand ASCII and hexadecimal output, and examine the data carried inside network packets.

**Documentation by Zero2Null Security**
