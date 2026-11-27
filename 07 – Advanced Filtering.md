# 07 – Advanced Filtering

## 1. Introduction

In the previous modules, we learned how to capture network traffic, apply basic filters, use logical operators, and analyze saved PCAP files.

Now, we will learn **Advanced Packet Filtering** using Tcpdump.

In a real network, thousands of packets can travel between different systems every second. Checking every packet manually can take a lot of time.

Advanced filtering helps us find specific packets based on detailed conditions.

For example:

- Find traffic coming from a particular network.
- Monitor communication between specific systems.
- Identify traffic using a range of port numbers.
- Find TCP SYN packets.
- Identify TCP reset packets.
- Display packets larger or smaller than a specific size.
- Filter traffic using MAC addresses.
- Combine multiple conditions to investigate suspicious communication.

Tcpdump uses Berkeley Packet Filter (BPF) syntax to identify packets that match our requirements.

By the end of this module, you will be able to create more specific filters for network monitoring and security investigations.

## 2. Understanding Advanced Packet Filtering

Before learning advanced commands, let's understand how packet filtering works.

When we run Tcpdump with a filter, it checks each packet against the filter condition.

Only packets that match the condition are displayed or processed.

For example:

```bash
sudo tcpdump -i eth0 host 192.168.1.10
```

This command displays packets involving the IP address `192.168.1.10`.

Now imagine that we want to monitor an entire network instead of one IP address.

We can use a network filter:

```bash
sudo tcpdump -i eth0 net 192.168.1.0/24
```

This displays packets involving the specified network.

The difference is simple:

- `host` filters traffic involving one IP address.
- `net` filters traffic involving a network range.

Let's explore these filters in detail.

## 3. Filter Traffic Using Network Addresses

Sometimes, we need to monitor an entire network instead of a single host.

Tcpdump provides the `net` keyword for this purpose.

### 3.1 Filter an entire network

```bash
sudo tcpdump -i eth0 net 192.168.1.0/24
```

This command displays packets involving the network `192.168.1.0/24`.

The `/24` is CIDR notation. It represents a network with 256 total IPv4 addresses.

### 3.2 Filter traffic coming from a network

```bash
sudo tcpdump -i eth0 src net 192.168.1.0/24
```

This displays packets whose source IP address belongs to the specified network.

### 3.3 Filter traffic going to a network

```bash
sudo tcpdump -i eth0 dst net 192.168.1.0/24
```

This displays packets whose destination IP address belongs to the specified network.

### Command breakdown

| Keyword | Meaning |
|---|---|
| `net` | Matches a network range |
| `src net` | Matches a source network |
| `dst net` | Matches a destination network |
| `192.168.1.0/24` | Network address with CIDR prefix |

### Practical example

Imagine an organization uses the following internal network:

```text
192.168.1.0/24
```

The security team wants to monitor traffic coming from this network.

They can use:

```bash
sudo tcpdump -i eth0 src net 192.168.1.0/24
```

This helps them focus on packets originating from that internal network.

## 4. Filter Traffic Using Port Ranges

In previous modules, we learned how to filter traffic using a single port number.

For example:

```bash
sudo tcpdump -i eth0 port 443
```

But sometimes we need to monitor multiple ports within a range.

Tcpdump provides the `portrange` keyword.

### 4.1 Filter a port range

```bash
sudo tcpdump -i eth0 portrange 20-100
```

This displays packets involving port numbers from 20 through 100, inclusive.

### 4.2 Filter a source port range

```bash
sudo tcpdump -i eth0 src portrange 20-100
```

This displays packets whose source port falls within the specified range.

### 4.3 Filter a destination port range

```bash
sudo tcpdump -i eth0 dst portrange 20-100
```

This displays packets whose destination port falls within the specified range.

### 4.4 Filter TCP traffic within a port range

```bash
sudo tcpdump -i eth0 'tcp and portrange 20-100'
```

This displays TCP packets involving ports from 20 through 100.

### 4.5 Filter UDP traffic within a port range

```bash
sudo tcpdump -i eth0 'udp and portrange 50-150'
```

This displays UDP packets involving ports from 50 through 150.

### Why is this useful?

Imagine a network administrator wants to monitor communication involving a group of application ports.

Instead of writing separate filters for every port, they can use one port-range filter.

This makes monitoring easier.

## 5. Filter Traffic Using TCP Flags

TCP flags are important fields in the TCP header.

They help identify the state and purpose of TCP communication.

Some common TCP flags are:

| TCP Flag | Meaning |
|---|---|
| SYN | Starts a TCP connection |
| ACK | Acknowledges received data |
| FIN | Normally used to close a TCP connection |
| RST | Resets a TCP connection |
| PSH | Requests that received data be pushed to the application |
| URG | Indicates urgent data |

### Why are TCP flags important?

TCP flags help network and security analysts understand how systems establish, maintain, and terminate TCP connections.

For example:

- SYN packets can indicate connection attempts.
- SYN and ACK packets commonly indicate a response to a connection attempt.
- RST packets can indicate a connection reset.
- FIN packets commonly appear during connection termination.

Tcpdump allows us to filter packets based on TCP flags.

### 5.1 Capture SYN packets

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-syn != 0'
```

This displays TCP packets where the SYN flag is set.

**Command breakdown:**

| Part | Meaning |
|---|---|
| `tcp` | Selects TCP packets |
| `[tcpflags]` | Accesses the TCP flags field |
| `&` | Performs a bitwise AND operation |
| `tcp-syn` | Represents the SYN flag |
| `!= 0` | Checks whether the SYN flag is set |

### 5.2 Capture SYN packets without ACK

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn'
```

This matches TCP packets where SYN is set and ACK is not set.

These packets commonly represent the initial connection request in a TCP three-way handshake.

### 5.3 Capture SYN-ACK packets

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & (tcp-syn|tcp-ack) == (tcp-syn|tcp-ack)'
```

This matches TCP packets where both SYN and ACK flags are set.

These packets commonly represent the second step of the TCP three-way handshake.

### 5.4 Capture RST packets

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-rst != 0'
```

This displays TCP packets where the RST flag is set.

RST packets are useful when investigating unexpected connection resets.

### 5.5 Capture FIN packets

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-fin != 0'
```

This displays TCP packets where the FIN flag is set.

FIN packets commonly appear when a TCP connection is being closed.

### 5.6 Capture ACK packets

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-ack != 0'
```

This displays TCP packets where the ACK flag is set.

**Important:** These filters check whether a particular flag is set. A packet can contain multiple TCP flags at the same time.

## 6. Filter Packets Based on Packet Size

Sometimes, packet size can help us investigate unusual network activity.

Tcpdump provides two useful keywords:

- `greater` – Matches packets larger than a specified size.
- `less` – Matches packets smaller than a specified size.

The size is measured in bytes.

### 6.1 Find packets larger than 1000 bytes

```bash
sudo tcpdump -i eth0 greater 1000
```

This displays packets larger than 1000 bytes.

### 6.2 Find packets smaller than 100 bytes

```bash
sudo tcpdump -i eth0 less 100
```

This displays packets smaller than 100 bytes.

### 6.3 Find packets within a size range

We can combine both conditions.

```bash
sudo tcpdump -i eth0 'greater 100 and less 1000'
```

This displays packets larger than 100 bytes and smaller than 1000 bytes.

### Why is packet-size filtering useful?

Packet-size filtering can help us:

- Identify unusually large packets.
- Investigate small or repeated packets.
- Narrow down network traffic during troubleshooting.
- Examine communication patterns.

**Important:** Packet size alone does not prove malicious activity. Normal applications can also generate large or small packets.

## 7. Filter Traffic Using MAC Addresses

A MAC address is a hardware address associated with a network interface.

It operates at the Data Link Layer (Layer 2) of the OSI model.

A MAC address usually looks like this:

```text
00:1A:2B:3C:4D:5E
```

Tcpdump can filter Ethernet traffic using MAC addresses.

### 7.1 Filter traffic involving a MAC address

```bash
sudo tcpdump -i eth0 ether host 00:1A:2B:3C:4D:5E
```

This displays Ethernet packets where the specified MAC address is either the source or destination.

### 7.2 Filter traffic from a MAC address

```bash
sudo tcpdump -i eth0 ether src 00:1A:2B:3C:4D:5E
```

This displays Ethernet frames sent from the specified MAC address.

### 7.3 Filter traffic going to a MAC address

```bash
sudo tcpdump -i eth0 ether dst 00:1A:2B:3C:4D:5E
```

This displays Ethernet frames sent to the specified MAC address.

### Why is this useful?

MAC address filtering can help network administrators:

- Identify traffic from a particular device.
- Investigate unexpected devices on a local network.
- Troubleshoot communication at the Ethernet layer.
- Examine local network traffic.

**Note:** Ethernet MAC filters apply to link-layer traffic. The visible link-layer addresses depend on the network interface and capture point. For example, a packet captured after routing may show the router's MAC address rather than the original remote device's MAC address.

## 8. Filter Broadcast and Multicast Traffic

Broadcast and multicast are two different ways of sending network traffic to multiple devices.

### 8.1 What is Broadcast?

Broadcast traffic is sent to all devices on a particular broadcast domain.

For example, the Ethernet broadcast MAC address is:

```text
ff:ff:ff:ff:ff:ff
```

To capture Ethernet broadcast traffic:

```bash
sudo tcpdump -i eth0 ether broadcast
```

This displays Ethernet broadcast frames.

### 8.2 What is Multicast?

Multicast traffic is sent to a selected group of devices that have joined a multicast group.

To capture Ethernet multicast traffic:

```bash
sudo tcpdump -i eth0 ether multicast
```

This displays Ethernet multicast frames.

### Why is this useful?

Broadcast and multicast traffic are common in networks.

They are used by services and protocols such as:

- ARP
- DHCP
- Network discovery
- Multicast streaming
- Routing protocols

Monitoring this traffic can help administrators understand local network communication and investigate unusual traffic patterns.

## 9. Filter Traffic Using IP Protocol Fields

We can also filter traffic based on IP protocol numbers.

For example:

| Protocol | IP Protocol Number |
|---|---:|
| ICMP | 1 |
| TCP | 6 |
| UDP | 17 |
| IPv6 encapsulation | 41 |
| GRE | 47 |
| ESP | 50 |
| AH | 51 |

### 9.1 Filter TCP using its protocol number

```bash
sudo tcpdump -i eth0 'ip proto 6'
```

This displays IPv4 packets using TCP.

### 9.2 Filter UDP using its protocol number

```bash
sudo tcpdump -i eth0 'ip proto 17'
```

This displays IPv4 packets using UDP.

### 9.3 Filter ICMP using its protocol number

```bash
sudo tcpdump -i eth0 'ip proto 1'
```

This displays IPv4 packets using ICMP.

### Why use protocol numbers?

Protocol names such as TCP, UDP, and ICMP are easier to remember.

However, protocol numbers are useful when working with low-level packet filters or investigating IP headers.

**Note:** The `ip proto` keyword is for IPv4. IPv6 uses a different header structure and protocol identification through its Next Header field.

## 10. Filter IPv6 Traffic

IPv6 is the newer version of the Internet Protocol.

It uses 128-bit addresses instead of the 32-bit addresses used by IPv4.

An example IPv6 address is:

```text
2001:db8::10
```

Tcpdump provides the `ip6` keyword to filter IPv6 traffic.

### 10.1 Display all IPv6 traffic

```bash
sudo tcpdump -i eth0 ip6
```

This displays IPv6 packets.

### 10.2 Filter traffic involving a specific IPv6 address

```bash
sudo tcpdump -i eth0 'ip6 host 2001:db8::10'
```

This displays IPv6 packets involving the specified address.

### 10.3 Filter IPv6 TCP traffic

```bash
sudo tcpdump -i eth0 'ip6 and tcp'
```

This displays IPv6 packets carrying TCP traffic.

### 10.4 Filter IPv6 UDP traffic

```bash
sudo tcpdump -i eth0 'ip6 and udp'
```

This displays IPv6 packets carrying UDP traffic.

### Why is this useful?

Modern networks use both IPv4 and IPv6.

Security analysts should understand how to monitor both types of traffic because IPv6 communication may be missed if an investigation only considers IPv4.

## 11. Combine Advanced Filters

In real-world investigations, a single filter may not be enough.

We often need to combine multiple conditions to find exactly the traffic we want.

We can use logical operators:

- `and`
- `or`
- `not`

### 11.1 Find TCP traffic involving port 443 from a specific host

```bash
sudo tcpdump -i eth0 'tcp and host 192.168.1.10 and port 443'
```

This displays TCP traffic involving `192.168.1.10` and port 443.

### 11.2 Find traffic from a network using TCP or UDP

```bash
sudo tcpdump -i eth0 'net 192.168.1.0/24 and (tcp or udp)'
```

This displays TCP or UDP traffic involving the specified network.

### 11.3 Find traffic excluding a specific host

```bash
sudo tcpdump -i eth0 'not host 192.168.1.10'
```

This displays packets that do not involve the specified IP address.

### 11.4 Find TCP traffic except port 22

```bash
sudo tcpdump -i eth0 'tcp and not port 22'
```

This displays TCP packets that do not involve port 22.

### 11.5 Find large TCP packets involving port 443

```bash
sudo tcpdump -i eth0 'tcp and port 443 and greater 1000'
```

This displays TCP packets involving port 443 that are larger than 1000 bytes.

### Why are parentheses important?

Parentheses help group conditions and make the filter easier to understand.

For example:

```bash
sudo tcpdump -i eth0 'tcp and (port 80 or port 443)'
```

This displays TCP packets involving either port 80 or port 443.

Without parentheses, the filter may be interpreted differently because logical operators have different precedence.

## 12. Advanced Filtering on Saved PCAP Files

Advanced filters can also be used while reading previously captured packets.

We do not need to capture live traffic every time.

Use the `-r` option to read a PCAP file and then apply the filter.

### 12.1 Find SYN packets in a PCAP file

```bash
sudo tcpdump -nn -r sample.pcap 'tcp[tcpflags] & tcp-syn != 0'
```

This displays packets where the TCP SYN flag is set.

### 12.2 Find packets involving a network

```bash
sudo tcpdump -nn -r sample.pcap net 192.168.1.0/24
```

This displays packets involving the specified network.

### 12.3 Find traffic involving a port range

```bash
sudo tcpdump -nn -r sample.pcap portrange 20-100
```

This displays packets involving ports from 20 through 100.

### 12.4 Find packets larger than 1000 bytes

```bash
sudo tcpdump -nn -r sample.pcap greater 1000
```

This displays packets larger than 1000 bytes.

### 12.5 Find TCP RST packets

```bash
sudo tcpdump -nn -r sample.pcap 'tcp[tcpflags] & tcp-rst != 0'
```

This displays TCP packets where the RST flag is set.

## 13. Practical Lab – Advanced Filtering

Now let's apply the filters we learned using a local Linux system.

We will capture some network traffic and use different advanced filters to analyze it.

### Lab requirements

- Kali Linux or Ubuntu
- Tcpdump installed
- Terminal access
- An active network interface
- Internet connection for the optional traffic-generation commands

### Step 1: Identify your network interface

```bash
ip a
```

This displays available network interfaces and their IP addresses.

You can also use:

```bash
sudo tcpdump -D
```

This displays the interfaces available to Tcpdump.

Choose the correct interface for your system.

### Step 2: Start packet capture

Create a directory:

```bash
mkdir -p ~/tcpdump-lab/advanced
cd ~/tcpdump-lab/advanced
```

Start capturing:

```bash
sudo tcpdump -i any -nn -c 100 -w advanced-lab.pcap
```

This saves up to 100 packets into `advanced-lab.pcap`.

### Step 3: Generate some network traffic

While Tcpdump is capturing, open another terminal.

Generate ICMP traffic:

```bash
ping -c 5 1.1.1.1
```

Generate HTTPS traffic:

```bash
curl -I https://example.com
```

Generate DNS traffic:

```bash
nslookup example.com
```

These commands generate different types of network activity. The actual packets captured will depend on your system, network, and available interfaces.

Wait for Tcpdump to finish capturing.

### Step 4: Read the capture file

```bash
sudo tcpdump -nn -r advanced-lab.pcap
```

Review the captured packets.

### Step 5: Filter TCP packets

```bash
sudo tcpdump -nn -r advanced-lab.pcap tcp
```

Observe the TCP traffic.

### Step 6: Filter UDP packets

```bash
sudo tcpdump -nn -r advanced-lab.pcap udp
```

Observe the UDP traffic.

### Step 7: Filter DNS traffic

```bash
sudo tcpdump -nn -r advanced-lab.pcap port 53
```

This displays packets involving port 53, commonly used for DNS.

### Step 8: Filter TCP SYN packets

```bash
sudo tcpdump -nn -r advanced-lab.pcap 'tcp[tcpflags] & tcp-syn != 0'
```

Observe TCP packets where the SYN flag is set.

### Step 9: Filter large packets

```bash
sudo tcpdump -nn -r advanced-lab.pcap greater 500
```

This displays packets larger than 500 bytes.

### Step 10: Filter packets involving a port range

```bash
sudo tcpdump -nn -r advanced-lab.pcap portrange 1-1024
```

This displays packets involving port numbers from 1 through 1024.

**Lab result:** You have captured network traffic and applied advanced filters to identify specific packets.

## 14. Real-World Use Cases

Advanced filtering is useful in network monitoring, SOC operations, incident response, and troubleshooting.

### 14.1 Detecting Suspicious Connection Attempts

A security analyst wants to investigate TCP connection attempts from systems on a network.

They can filter SYN packets:

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn'
```

This helps identify initial TCP connection requests.

Repeated connection attempts may need further investigation, especially when they involve unusual destinations or many ports.

### 14.2 Investigating Connection Resets

A network administrator receives complaints that connections are unexpectedly terminating.

They can inspect TCP RST packets:

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-rst != 0'
```

This helps identify packets that reset TCP connections.

Further investigation is needed to determine why the resets occurred.

### 14.3 Monitoring Internal Network Traffic

A SOC analyst wants to focus on traffic involving an internal network.

They can use:

```bash
sudo tcpdump -i eth0 net 192.168.1.0/24
```

This reduces unrelated traffic in the output.

### 14.4 Investigating Unusual Packet Sizes

A network analyst wants to inspect packets larger than 1500 bytes:

```bash
sudo tcpdump -i eth0 greater 1500
```

This can help investigate large packets or unusual traffic patterns.

Remember that packet sizes depend on the network and capture point. Large packets are not automatically malicious.

### 14.5 Monitoring a Specific Application Port Range

An administrator wants to investigate traffic involving a group of application ports.

They can use:

```bash
sudo tcpdump -i eth0 portrange 8000-9000
```

This displays packets involving ports in the specified range.

## 15. Common Mistakes During Advanced Filtering

| Mistake | Explanation | Solution |
|---|---|---|
| Incorrect network range | The CIDR notation is incorrect | Verify the network address and prefix |
| Incorrect port range | The starting or ending port is invalid | Check the port numbers |
| Missing quotation marks | The shell may interpret special characters | Quote complex filters |
| Incorrect TCP flag expression | The filter may not match the intended flags | Check the flag expression carefully |
| Wrong interface | Traffic is captured from an unrelated interface | Check interfaces using `ip a` or `tcpdump -D` |
| Mixing IPv4 and IPv6 filters | The filter does not match the intended IP version | Use `ip` or `ip6` appropriately |
| Assuming large packets are malicious | Packet size alone is not proof of an attack | Investigate additional packet information |

## 16. Quick Command Reference

| Command | Purpose |
|---|---|
| `tcpdump net 192.168.1.0/24` | Filter traffic involving a network |
| `tcpdump src net 192.168.1.0/24` | Filter traffic from a network |
| `tcpdump dst net 192.168.1.0/24` | Filter traffic going to a network |
| `tcpdump portrange 20-100` | Filter a port range |
| `tcpdump src portrange 20-100` | Filter source ports in a range |
| `tcpdump dst portrange 20-100` | Filter destination ports in a range |
| `tcpdump 'tcp[tcpflags] & tcp-syn != 0'` | Filter TCP SYN packets |
| `tcpdump 'tcp[tcpflags] & tcp-rst != 0'` | Filter TCP RST packets |
| `tcpdump 'tcp[tcpflags] & tcp-fin != 0'` | Filter TCP FIN packets |
| `tcpdump greater 1000` | Filter packets larger than 1000 bytes |
| `tcpdump less 100` | Filter packets smaller than 100 bytes |
| `tcpdump ether host 00:1A:2B:3C:4D:5E` | Filter Ethernet traffic involving a MAC address |
| `tcpdump ether broadcast` | Filter Ethernet broadcast frames |
| `tcpdump ether multicast` | Filter Ethernet multicast frames |
| `tcpdump ip6` | Filter IPv6 traffic |
| `tcpdump 'ip proto 6'` | Filter IPv4 TCP traffic |
| `tcpdump 'ip proto 17'` | Filter IPv4 UDP traffic |
| `tcpdump 'ip proto 1'` | Filter IPv4 ICMP traffic |

*Note: These command examples show the filter expressions. Add `-i eth0` for live capture or `-r sample.pcap` for saved-file analysis.*

## 17. Practice Questions

Try answering these questions after completing the module.

1. What is advanced packet filtering?
2. What is the difference between `host` and `net`?
3. Which keyword is used to filter a range of port numbers?
4. What are TCP flags?
5. What is the purpose of the SYN flag?
6. What is the difference between SYN and SYN-ACK packets?
7. Which filter is used to identify TCP RST packets?
8. How can you filter packets larger than 1000 bytes?
9. What is the purpose of the `ether host` filter?
10. What is the difference between broadcast and multicast?
11. Which keyword is used to filter IPv6 traffic?
12. Why are advanced filters useful during SOC investigations?

## 18. Practical Tasks

Complete these tasks using your own lab capture file.

- [ ] Identify the available network interfaces.
- [ ] Capture network traffic into a PCAP file.
- [ ] Filter traffic involving a specific network.
- [ ] Filter traffic using a port range.
- [ ] Identify TCP SYN packets.
- [ ] Identify TCP RST packets.
- [ ] Identify TCP FIN packets.
- [ ] Find packets larger than 1000 bytes.
- [ ] Filter traffic involving a specific MAC address, if Ethernet headers are available.
- [ ] Filter broadcast traffic.
- [ ] Filter multicast traffic.
- [ ] Identify IPv6 packets.
- [ ] Combine multiple filter conditions.
- [ ] Apply advanced filters to a saved PCAP file.

## 19. Key Takeaways

In this module, we learned how to use advanced filtering techniques in Tcpdump.

We covered:

- Filtering traffic using network addresses.
- Filtering source and destination networks.
- Filtering traffic using port ranges.
- Understanding and filtering TCP flags.
- Identifying SYN, SYN-ACK, RST, and FIN packets.
- Filtering packets based on their size.
- Filtering traffic using MAC addresses.
- Identifying broadcast and multicast traffic.
- Filtering IPv4 and IPv6 packets.
- Combining multiple advanced filtering conditions.
- Applying advanced filters to saved PCAP files.
- Using advanced filtering during network monitoring and security investigations.

**Remember:** Advanced filtering helps you focus on the packets that matter. Instead of reading thousands of unrelated packets, you can create specific filters to investigate network activity more efficiently.

## 20. References and Further Learning

- [Tcpdump Official Website](https://www.tcpdump.org/)
- [Tcpdump Manual Page](https://www.tcpdump.org/manpages/tcpdump.1.html)
- [Wireshark Documentation](https://www.wireshark.org/docs/)
- [PCAP Filter Manual](https://www.tcpdump.org/manpages/pcap-filter.7.html)
- [TryHackMe](https://tryhackme.com/)

---

**Next Module: 08 – TCP Flags Analysis**

In the next module, we will study TCP flags in more detail, understand the TCP three-way handshake, and learn how to analyze TCP connection behavior using Tcpdump.

**Documentation by Zero2Null Security**
