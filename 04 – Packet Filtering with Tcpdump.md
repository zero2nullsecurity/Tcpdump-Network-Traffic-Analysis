# 04 – Packet Filtering with Tcpdump

## 1. Introduction to Packet Filtering

In the previous module, we learned how to capture network packets using Tcpdump.

However, when monitoring a network, thousands of packets may be generated within a short period.

Capturing and analyzing every packet can make network investigation difficult.

For example, suppose you want to investigate DNS traffic on your network. You don't need to inspect every TCP, UDP, ICMP, or ARP packet.

You only need to capture packets related to DNS communication.

This is where **Packet Filtering** becomes useful.

Packet filtering allows us to capture or display only the network packets that match specific conditions.

With Tcpdump, we can filter packets based on:

- Host IP address
- Source IP address
- Destination IP address
- Port number
- Source port
- Destination port
- Network protocol

**Simple Example:**

Suppose your computer is communicating with multiple devices, but you only want to observe traffic involving `192.168.1.10`.

You can use:

```bash
sudo tcpdump -i eth0 host 192.168.1.10 -nn
```

This command displays packets involving the specified host.

Instead of viewing unrelated traffic, you can focus on communication involving that IP address.

---

## 2. Understanding Tcpdump Filter Syntax

Tcpdump uses packet filtering expressions to identify which packets should be selected.

Basic syntax:

```bash
sudo tcpdump -i INTERFACE [FILTER]
```

Let's understand the components:

| Component | Description |
|---|---|
| sudo | Runs Tcpdump with elevated privileges |
| tcpdump | Starts the packet analyzer |
| -i | Selects a network interface |
| INTERFACE | Network interface name |
| FILTER | Condition used to select packets |

Example:

```bash
sudo tcpdump -i eth0 host 192.168.1.10
```

Here:

- `eth0` is the selected network interface.
- `host` specifies host-based filtering.
- `192.168.1.10` is the IP address we want to monitor.

**Important:** The filter expression is evaluated by Tcpdump's packet-filtering mechanism. Only packets matching the expression are selected for display or capture.

---

## 3. Host-Based Filtering

Host-based filtering allows us to capture packets associated with a specific host.

A host can be identified using its IP address or a hostname.

### 3.1 Filter Traffic for a Specific Host

Command:

```bash
sudo tcpdump -i eth0 host 192.168.1.10 -nn
```

Explanation:

- `-i eth0` selects the network interface.
- `host` specifies the host filter.
- `192.168.1.10` is the target IP address.
- `-nn` disables hostname and port resolution.

This command captures packets where `192.168.1.10` is either the source or destination.

### Example Output

```text
IP 192.168.1.10.52000 > 93.184.215.14.443: Flags [S]
IP 93.184.215.14.443 > 192.168.1.10.52000: Flags [S.]
```

Understanding the output:

- First packet: `192.168.1.10` sends traffic to `93.184.215.14`.
- Second packet: `93.184.215.14` sends traffic back to `192.168.1.10`.

Both packets match the host filter because the specified IP appears in each packet.

### 3.2 Filter Traffic Using a Hostname

Tcpdump also supports hostname-based filters.

Example:

```bash
sudo tcpdump -i eth0 host example.com
```

This selects packets associated with the IP address or addresses resolved for `example.com`.

**Important:** Hostname resolution may introduce delays or unexpected results if the DNS response changes. For precise analysis, IP-based filtering is often preferable.

---

## 4. Source IP Address Filtering

Before using source filtering, understand what a source IP address means.

The source IP address identifies the device that sends a particular IP packet.

Example:

```text
Source IP:      192.168.1.10
Destination IP: 93.184.215.14
```

Here, `192.168.1.10` is the source IP.

### Command

```bash
sudo tcpdump -i eth0 src host 192.168.1.10 -nn
```

Explanation:

- `src` means source.
- `host` identifies an IP host.
- `192.168.1.10` is the source IP address.

This command selects packets sent from `192.168.1.10`.

### Example

Suppose three devices are communicating:

```text
192.168.1.10  →  93.184.215.14
192.168.1.20  →  1.1.1.1
192.168.1.30  →  8.8.8.8
```

If we execute:

```bash
sudo tcpdump -i eth0 src host 192.168.1.10 -nn
```

Tcpdump selects packets whose source IP is `192.168.1.10`.

**Use Case:** A SOC analyst may use source IP filtering to investigate traffic originating from a particular workstation.

---

## 5. Destination IP Address Filtering

The destination IP address identifies the device to which a packet is being sent.

Example:

```text
Source IP:      192.168.1.10
Destination IP: 93.184.215.14
```

Here, `93.184.215.14` is the destination IP.

### Command

```bash
sudo tcpdump -i eth0 dst host 93.184.215.14 -nn
```

Explanation:

- `dst` means destination.
- `host` identifies an IP host.
- `93.184.215.14` is the destination IP.

This command selects packets sent to the specified IP address.

### Example

Suppose multiple systems communicate with different servers.

You want to observe traffic going to `1.1.1.1`.

Execute:

```bash
sudo tcpdump -i eth0 dst host 1.1.1.1 -nn
```

Tcpdump will display packets whose destination IP is `1.1.1.1`.

**Use Case:** Destination filtering can help investigate which devices are communicating with a particular external server.

### Host vs Source vs Destination Filtering

| Filter | Purpose |
|---|---|
| `host 192.168.1.10` | Matches source or destination |
| `src host 192.168.1.10` | Matches source only |
| `dst host 192.168.1.10` | Matches destination only |

Remember these three expressions because they are frequently used in network traffic analysis.

---

## 6. Port-Based Filtering

Before learning port filtering, understand what a port is.

A port is a logical communication endpoint used by applications and services.

Different network services commonly use different port numbers.

Examples:

| Port | Common Service |
|---|---|
| 20 | FTP Data |
| 21 | FTP Control |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 110 | POP3 |
| 143 | IMAP |
| 443 | HTTPS |
| 445 | SMB |
| 3389 | RDP |

**Important:** These are commonly assigned ports. A service can sometimes operate on a different port.

### 6.1 Filter Traffic on a Specific Port

Tcpdump provides the `port` keyword to filter traffic based on a port number.

Command:

```bash
sudo tcpdump -i eth0 port 53 -nn
```

This command selects traffic where the source or destination port is 53.

Port 53 is commonly used by DNS.

### Example Output

```text
IP 192.168.1.10.52000 > 192.168.1.1.53: 12345+ A? example.com.
IP 192.168.1.1.53 > 192.168.1.10.52000: 12345 1/0/0 A 93.184.215.14
```

Understanding the output:

- `192.168.1.10` is the client.
- `192.168.1.1` is the DNS server.
- `52000` is the client's temporary source port.
- `53` is the DNS server port.
- `A? example.com` represents an IPv4 address DNS query.
- The response contains an IPv4 address.

This example illustrates a DNS request and response.

### 6.2 Filter SSH Traffic

SSH commonly uses TCP port 22.

Command:

```bash
sudo tcpdump -i eth0 port 22 -nn
```

This selects traffic where source or destination port is 22.

It can help observe SSH-related network communication.

### 6.3 Filter HTTP Traffic

HTTP commonly uses TCP port 80.

Command:

```bash
sudo tcpdump -i eth0 port 80 -nn
```

This selects traffic involving port 80.

### 6.4 Filter HTTPS Traffic

HTTPS commonly uses TCP port 443.

Command:

```bash
sudo tcpdump -i eth0 port 443 -nn
```

This selects traffic involving port 443.

Remember that seeing HTTPS packets does not mean we can read their encrypted application contents.

---

## 7. Source Port Filtering

Sometimes we only want to observe packets originating from a specific port.

For this purpose, Tcpdump provides the `src port` expression.

### Command

```bash
sudo tcpdump -i eth0 src port 53 -nn
```

This selects packets whose source port is 53.

### Example

```text
192.168.1.1.53 > 192.168.1.10.52000
```

Here:

- `192.168.1.1` is the source IP.
- `53` is the source port.
- `192.168.1.10` is the destination IP.
- `52000` is the destination port.

This packet may represent a DNS response from a DNS server.

**Use Case:** Source port filtering can help identify responses originating from a particular service port.

---

## 8. Destination Port Filtering

Destination port filtering allows us to select packets sent to a particular port.

### Command

```bash
sudo tcpdump -i eth0 dst port 443 -nn
```

This selects packets whose destination port is 443.

### Example

```text
192.168.1.10.52000 > 93.184.215.14.443
```

Here:

- `192.168.1.10` is the source device.
- `52000` is the source port.
- `93.184.215.14` is the destination server.
- `443` is the destination port.

This packet is going to a service using TCP port 443.

### Port vs Source Port vs Destination Port

| Filter | Purpose |
|---|---|
| `port 53` | Source or destination port 53 |
| `src port 53` | Source port 53 |
| `dst port 53` | Destination port 53 |

---

## 9. Protocol-Based Filtering

A network protocol defines the rules used by devices to communicate.

Tcpdump allows us to filter packets according to their network protocol.

Some commonly used protocol filters are:

- TCP
- UDP
- ICMP
- ARP

### 9.1 TCP Filtering

TCP stands for Transmission Control Protocol.

It is a connection-oriented transport protocol that provides reliable, ordered delivery of data.

Command:

```bash
sudo tcpdump -i eth0 tcp -nn
```

This command selects TCP packets visible on the selected interface.

Example use cases:

- Observe TCP connections.
- Investigate connection attempts.
- Monitor TCP communication.
- Study TCP flags.

### 9.2 UDP Filtering

UDP stands for User Datagram Protocol.

It is a connectionless transport protocol that does not provide TCP-style delivery guarantees.

Command:

```bash
sudo tcpdump -i eth0 udp -nn
```

This command selects UDP packets.

Common UDP-based applications and protocols include:

- DNS
- DHCP
- NTP
- Streaming and real-time communication protocols

### 9.3 ICMP Filtering

ICMP stands for Internet Control Message Protocol.

It is used for network-related control and diagnostic messages.

For example, the `ping` command commonly uses ICMP Echo Request and Echo Reply messages.

Command:

```bash
sudo tcpdump -i eth0 icmp -nn
```

This command selects ICMP packets.

Practical example:

Open another terminal and execute:

```bash
ping -c 4 1.1.1.1
```

Observe the ICMP packets in the Tcpdump terminal.

### 9.4 ARP Filtering

ARP stands for Address Resolution Protocol.

It is used on IPv4 local networks to resolve an IPv4 address into a link-layer address, usually a MAC address on Ethernet.

Command:

```bash
sudo tcpdump -i eth0 arp -nn
```

This command selects ARP packets.

ARP is commonly used when a device needs to discover the MAC address associated with an IPv4 address on its local network.

### Protocol Filtering Summary

| Filter | Purpose |
|---|---|
| `tcp` | Select TCP packets |
| `udp` | Select UDP packets |
| `icmp` | Select ICMP packets |
| `arp` | Select ARP packets |

---

## 10. Filter DNS Traffic

DNS stands for Domain Name System.

DNS translates domain names into IP addresses.

For example, when you visit:

```text
example.com
```

Your device may send a DNS query to resolve the domain name.

DNS commonly uses port 53 over UDP or TCP.

### Command

```bash
sudo tcpdump -i eth0 port 53 -nn
```

This selects packets involving port 53.

### Practical Exercise

Start Tcpdump in one terminal:

```bash
sudo tcpdump -i eth0 port 53 -nn
```

Open another terminal and generate a DNS query:

```bash
nslookup example.com
```

Return to Tcpdump and observe the DNS communication if it is visible on the selected interface.

You may observe:

- Source IP address.
- Destination IP address.
- Source port.
- Destination port.
- DNS query information.
- DNS response information.

**Important:** DNS may use UDP or TCP. DNS over HTTPS (DoH) and DNS over TLS (DoT) use different transport arrangements and will not necessarily appear as ordinary port 53 traffic.

---

## 11. Filter HTTP, HTTPS and SSH Traffic

Let's understand some practical service-based filters.

### HTTP – Port 80

```bash
sudo tcpdump -i eth0 tcp port 80 -nn
```

This selects TCP traffic involving port 80.

### HTTPS – Port 443

```bash
sudo tcpdump -i eth0 tcp port 443 -nn
```

This selects TCP traffic involving port 443.

Note that HTTPS can also use HTTP/3 over QUIC, which normally uses UDP port 443. To include that traffic, use:

```bash
sudo tcpdump -i eth0 port 443 -nn
```

### SSH – Port 22

```bash
sudo tcpdump -i eth0 tcp port 22 -nn
```

This selects TCP traffic involving port 22.

### Why Are These Filters Useful?

A network analyst may use these filters to focus on specific service communications.

For example:

- Port 80 for HTTP traffic.
- Port 443 for HTTPS and other services using that port.
- Port 22 for SSH connections.
- Port 53 for traditional DNS traffic.

Remember that a port number alone does not prove which application is running. Traffic should be interpreted using additional packet information and context.

---

## 12. Practical Lab – Host and Port Filtering

Now let's perform a local practical exercise.

**Lab Objective:** Capture traffic involving a particular host and a particular port.

### Step 1 – Identify Your Interface

```bash
ip a
```

Identify your active network interface.

### Step 2 – Start Host Filtering

Replace the example IP with an IP address you control or are authorized to monitor.

```bash
sudo tcpdump -i eth0 host 192.168.1.10 -nn
```

Observe packets involving that host.

### Step 3 – Start Port Filtering

Stop the previous capture using `CTRL + C`.

Now execute:

```bash
sudo tcpdump -i eth0 port 53 -nn
```

This selects traffic involving port 53.

### Step 4 – Generate DNS Traffic

In another terminal:

```bash
nslookup example.com
```

Observe the DNS communication.

### Step 5 – Capture TCP Traffic

Stop the previous capture and execute:

```bash
sudo tcpdump -i eth0 tcp -nn
```

Observe TCP packets generated by your network activity.

### Step 6 – Capture ICMP Traffic

Execute:

```bash
sudo tcpdump -i eth0 icmp -nn
```

In another terminal:

```bash
ping -c 4 1.1.1.1
```

Observe the ICMP packets.

**Expected Result:** You should be able to select packets based on the host, port and protocol filters.

---

## 13. Practical Lab – Analyze an Existing PCAP File

Tcpdump can also apply filters to previously saved packet capture files.

Suppose you have a file named:

```text
traffic.pcap
```

You can filter its contents without starting a new live capture.

### Example 1 – Filter by Host

```bash
tcpdump -r traffic.pcap host 192.168.1.10 -nn
```

### Example 2 – Filter by Source IP

```bash
tcpdump -r traffic.pcap src host 192.168.1.10 -nn
```

### Example 3 – Filter by Destination IP

```bash
tcpdump -r traffic.pcap dst host 192.168.1.10 -nn
```

### Example 4 – Filter DNS Traffic

```bash
tcpdump -r traffic.pcap port 53 -nn
```

### Example 5 – Filter ICMP Packets

```bash
tcpdump -r traffic.pcap icmp -nn
```

These commands are useful when investigating previously captured network traffic.

---

## 14. Important Commands Cheat Sheet

| Command | Description |
|---|---|
| `tcpdump host 192.168.1.10` | Filter traffic involving a host |
| `tcpdump src host 192.168.1.10` | Filter source IP |
| `tcpdump dst host 192.168.1.10` | Filter destination IP |
| `tcpdump port 53` | Filter source or destination port 53 |
| `tcpdump src port 53` | Filter source port 53 |
| `tcpdump dst port 443` | Filter destination port 443 |
| `tcpdump tcp` | Filter TCP packets |
| `tcpdump udp` | Filter UDP packets |
| `tcpdump icmp` | Filter ICMP packets |
| `tcpdump arp` | Filter ARP packets |
| `tcpdump -r traffic.pcap port 53` | Filter DNS-related packets in a PCAP file |

**Note:** Add `sudo -i INTERFACE` as appropriate for live capture. For example:

```bash
sudo tcpdump -i eth0 port 53 -nn
```

---

## 15. Practice Exercises

Try solving these exercises independently.

### Beginner Level

1. Capture traffic involving the IP address `192.168.1.10`.
2. Capture packets sent from `192.168.1.10`.
3. Capture packets sent to `192.168.1.10`.
4. Capture traffic involving port 53.
5. Capture TCP packets.
6. Capture UDP packets.
7. Capture ICMP packets.
8. Capture ARP packets.

### Intermediate Level

9. Capture traffic involving TCP port 22.
10. Capture traffic involving TCP port 80.
11. Capture traffic involving TCP port 443.
12. Capture packets originating from source port 53.
13. Capture packets going to destination port 443.
14. Apply host filtering to an existing PCAP file.
15. Apply DNS filtering to an existing PCAP file.

### Challenge

You have a PCAP file named:

```text
traffic.pcap
```

Your task is to:

1. Read the PCAP file.
2. Identify packets involving `192.168.1.10`.
3. Identify packets using port 53.
4. Identify TCP packets.
5. Identify ICMP packets.

Try solving each task using the correct Tcpdump filter expression.

---

## 16. Important Notes

1. `host` matches packets where the specified IP is either the source or destination.
2. `src host` selects packets originating from the specified IP.
3. `dst host` selects packets going to the specified IP.
4. `port` matches either the source or destination port.
5. `src port` selects a specific source port.
6. `dst port` selects a specific destination port.
7. `tcp`, `udp`, `icmp`, and `arp` select packets based on protocol.
8. Use `-nn` to avoid hostname and port resolution.
9. Filters can be applied to live packet capture and saved PCAP files.
10. A port number does not guarantee which application generated the traffic.
11. Capture only traffic on systems and networks where you have authorization.

---

## 17. What We Will Learn Next

In this module, we learned how to filter packets based on:

- Host IP addresses.
- Source and destination IP addresses.
- Port numbers.
- Source and destination ports.
- Network protocols.
- Common services such as DNS, HTTP, HTTPS and SSH.

However, sometimes we need to apply multiple filtering conditions in a single command.

For example, we may want to capture:

- TCP traffic AND port 443.
- UDP traffic OR ICMP traffic.
- All traffic EXCEPT TCP traffic.

For this purpose, Tcpdump supports logical operators.

We will study them in the next module.

**Next Module:** 05 – Logical Operators

---

## References

- [Official Tcpdump Website](https://www.tcpdump.org/)
- [Tcpdump Manual](https://www.tcpdump.org/manpages/tcpdump.1.html)
- [PCAP Filter Manual](https://www.tcpdump.org/manpages/pcap-filter.7.html)
- [Wireshark Official Website](https://www.wireshark.org/)

---

**Author:** Zero2Null Security

**Category:** Network Traffic Analysis

**Focus:** Tcpdump | Packet Filtering | Networking | Cybersecurity

**Disclaimer:** This documentation is created for educational purposes. Perform packet capture and network analysis only on systems and networks where you have proper authorization.
