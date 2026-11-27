# Module 11 – Practical Labs

## Introduction

In the previous modules, we learned how to use Tcpdump, capture network packets, apply filters, analyze TCP flags, and inspect packet contents.

Now it is time to practice these concepts in real-world scenarios.

In this module, we will perform practical labs using Tcpdump. Each lab focuses on a specific task, such as capturing traffic, filtering packets, analyzing TCP connections, inspecting DNS requests, and saving packet captures for later analysis.

These labs will help you understand how Tcpdump is used in network troubleshooting, security monitoring, and incident response.

> **Important:** Perform these labs only on your own system, virtual lab, or networks where you have permission to capture traffic.

---

## Lab Environment and Prerequisites

Before starting the labs, make sure you have:

- Kali Linux, Ubuntu, or another Linux distribution.
- Tcpdump installed.
- Terminal access.
- An active network interface.
- Internet access for selected labs.
- Basic knowledge of Linux commands.

### Verify Tcpdump Installation

Run:

```bash
tcpdump --version
```

This command displays the installed Tcpdump version.

### Check Available Network Interfaces

Run:

```bash
ip a
```

This command displays the network interfaces available on your system.

You can also use:

```bash
sudo tcpdump -D
```

This command lists the interfaces that Tcpdump can use.

**Important:** Your interface may be named `eth0`, `ens33`, `enp0s3`, or `wlan0`. The examples below use `eth0` only as a placeholder. Replace it with the interface available on your system.

---

# Lab 01 – Capture Live Network Traffic

## Objective

Learn how to capture live network packets using Tcpdump.

### Step 1: Identify Your Network Interface

Run:

```bash
ip a
```

Find the interface connected to your network.

For example:

```text
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>
    inet 192.168.1.10/24
```

In this example, the interface is `eth0`.

### Step 2: Start Packet Capture

Run:

```bash
sudo tcpdump -i eth0
```

This command starts capturing packets from the selected interface.

### Step 3: Generate Network Traffic

Open another terminal and run:

```bash
ping -c 4 8.8.8.8
```

This generates four ICMP echo requests.

### Step 4: Stop Packet Capture

Return to the Tcpdump terminal and press:

```text
CTRL + C
```

Tcpdump will stop capturing and display capture statistics.

### What You Learned

- How to identify a network interface.
- How to start live packet capture.
- How to generate test traffic.
- How to stop a capture safely.

---

# Lab 02 – Capture Traffic from a Specific Host

## Objective

Capture packets related to a specific IP address.

### Step 1: Start Host-Based Capture

Run:

```bash
sudo tcpdump -i eth0 host 8.8.8.8 -nn
```

### Explanation

- `-i eth0` selects the network interface.
- `host 8.8.8.8` captures packets where this IP is either the source or destination.
- `-nn` disables hostname and service-name resolution.

### Step 2: Generate Traffic

Run:

```bash
ping -c 4 8.8.8.8
```

### Step 3: Observe the Packets

You should see ICMP packets related to `8.8.8.8`.

Illustrative output:

```text
IP 192.168.1.10 > 8.8.8.8: ICMP echo request
IP 8.8.8.8 > 192.168.1.10: ICMP echo reply
```

Your actual output may differ depending on your network.

### Step 4: Capture Only Outgoing Traffic

Run:

```bash
sudo tcpdump -i eth0 src host 8.8.8.8 -nn
```

This captures packets whose source IP is `8.8.8.8`.

### Step 5: Capture Only Incoming Traffic

Run:

```bash
sudo tcpdump -i eth0 dst host 8.8.8.8 -nn
```

This captures packets whose destination IP is `8.8.8.8`.

**Note:** These last two filters are intentionally different from the usual client-to-server direction. For outgoing traffic from your machine to `8.8.8.8`, use `dst host 8.8.8.8`. For replies from `8.8.8.8`, use `src host 8.8.8.8`.

### What You Learned

- How to filter packets by IP address.
- How to use `host`, `src host`, and `dst host`.
- How to identify packet direction.

---

# Lab 03 – Capture Traffic on a Specific Port

## Objective

Learn how to capture packets using port-based filters.

### Step 1: Capture DNS Traffic

Run:

```bash
sudo tcpdump -i eth0 port 53 -nn
```

Port 53 is commonly used by DNS.

### Step 2: Generate DNS Traffic

Open another terminal and run:

```bash
nslookup example.com
```

Or:

```bash
dig example.com
```

### Step 3: Observe DNS Packets

You may see DNS requests and responses.

Illustrative output:

```text
IP 192.168.1.10.53000 > 8.8.8.8.53: 1234+ A? example.com.
IP 8.8.8.8.53 > 192.168.1.10.53000: 1234 1/0/0 A 93.184.216.34
```

The IP addresses and DNS response may differ.

### Step 4: Capture SSH Traffic

Run:

```bash
sudo tcpdump -i eth0 port 22 -nn
```

This captures traffic using TCP or UDP port 22. SSH normally uses TCP port 22.

### Step 5: Capture HTTPS Traffic

Run:

```bash
sudo tcpdump -i eth0 port 443 -nn
```

This captures traffic using port 443.

**Note:** Port-based filters do not automatically identify the application. They match packets using the specified port number.

### What You Learned

- How to capture DNS traffic.
- How to filter traffic using port numbers.
- How to observe common network services.

---

# Lab 04 – Use Logical Operators

## Objective

Practice combining multiple packet filters using logical operators.

### Step 1: Use AND

Run:

```bash
sudo tcpdump -i eth0 'tcp and port 443'
```

This captures TCP packets associated with port 443.

### Step 2: Use OR

Run:

```bash
sudo tcpdump -i eth0 'port 80 or port 443'
```

This captures packets matching port 80 or port 443.

### Step 3: Use NOT

Run:

```bash
sudo tcpdump -i eth0 'not port 22'
```

This excludes packets matching port 22.

### Step 4: Combine Multiple Conditions

Run:

```bash
sudo tcpdump -i eth0 'tcp and (port 80 or port 443)'
```

This captures TCP traffic associated with either port 80 or port 443.

### Step 5: Exclude a Specific Host

Run:

```bash
sudo tcpdump -i eth0 'not host 192.168.1.20'
```

Replace the IP address with the host you want to exclude.

### What You Learned

- How to use `and`.
- How to use `or`.
- How to use `not`.
- How to combine multiple conditions using parentheses.

---

# Lab 05 – Save and Read a PCAP File

## Objective

Learn how to save captured packets and analyze them later.

### Step 1: Capture Packets into a File

Run:

```bash
sudo tcpdump -i eth0 -w lab-capture.pcap
```

Tcpdump will save captured packets into `lab-capture.pcap`.

Generate some traffic in another terminal.

For example:

```bash
ping -c 5 8.8.8.8
```

Stop Tcpdump using `CTRL + C`.

### Step 2: Check the File

Run:

```bash
ls -lh lab-capture.pcap
```

This displays the file size.

### Step 3: Read the PCAP File

Run:

```bash
tcpdump -r lab-capture.pcap -nn
```

This reads packets from the saved capture file.

### Step 4: Apply a Filter to the PCAP File

Run:

```bash
tcpdump -r lab-capture.pcap icmp -nn
```

This displays only ICMP packets from the saved file.

### Step 5: Check File Integrity

Run:

```bash
sha256sum lab-capture.pcap
```

This calculates the SHA-256 hash of the PCAP file.

A hash can help verify whether a file has changed between two checks.

### What You Learned

- How to save network traffic.
- How to read saved packet captures.
- How to filter packets inside a PCAP file.
- How to calculate a file hash.

---

# Lab 06 – Analyze TCP Flags

## Objective

Understand TCP connection behavior by observing TCP flags.

### Step 1: Capture TCP Traffic

Run:

```bash
sudo tcpdump -i eth0 'tcp' -nn
```

### Step 2: Generate a TCP Connection

In another terminal, run:

```bash
curl -I https://example.com
```

This attempts to establish an HTTPS connection and request response headers.

### Step 3: Observe the TCP Handshake

A normal TCP connection starts with a three-way handshake:

1. SYN – The client requests a connection.
2. SYN-ACK – The server acknowledges the request.
3. ACK – The client acknowledges the server.

Illustrative packet output:

```text
192.168.1.10.50000 > 93.184.216.34.443: Flags [S]
93.184.216.34.443 > 192.168.1.10.50000: Flags [S.]
192.168.1.10.50000 > 93.184.216.34.443: Flags [.]
```

The actual IP addresses, ports, and output will vary.

### Step 4: Capture Only SYN Packets Without ACK

Run:

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn'
```

This filter matches TCP packets with SYN set and ACK not set.

### Step 5: Capture TCP Reset Packets

Run:

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-rst != 0'
```

This captures TCP packets with the RST flag set.

### What You Learned

- How to observe a TCP handshake.
- How to identify SYN and SYN-ACK packets.
- How to filter TCP reset packets.
- How TCP flags help troubleshoot connections.

**Security note:** A large number of SYN packets may deserve investigation, but SYN packets alone do not prove that a port scan or attack is occurring.

---

# Lab 07 – Display Packet Contents

## Objective

Learn how to inspect packet contents using ASCII and hexadecimal output.

### Step 1: Capture Packet Contents in ASCII

Run:

```bash
sudo tcpdump -i eth0 -A -s 0
```

The `-A` option displays packet data as ASCII where possible.

### Step 2: Capture Packet Contents in Hexadecimal

Run:

```bash
sudo tcpdump -i eth0 -x
```

The `-x` option displays packet data in hexadecimal.

### Step 3: Display Hexadecimal and ASCII Together

Run:

```bash
sudo tcpdump -i eth0 -X
```

The `-X` option displays packet contents in both hexadecimal and ASCII.

### Step 4: Generate Test Traffic

In another terminal, run:

```bash
curl http://example.com
```

This generates an HTTP request. Depending on the server, the request may be redirected.

### Step 5: Compare the Output

Observe the difference between:

- `-A`: ASCII representation.
- `-x`: Hexadecimal representation.
- `-X`: Hexadecimal and ASCII representation.

### Important Note

HTTPS traffic is encrypted. Tcpdump may show connection metadata and encrypted application data, but it normally cannot display the original HTTPS page content as readable plaintext.

### What You Learned

- How to display packet data.
- How to inspect ASCII output.
- How to read hexadecimal output.
- Why encrypted traffic cannot normally be read as plaintext.

---

# Lab 08 – Analyze DNS Traffic

## Objective

Capture and understand DNS requests and responses.

### Step 1: Start DNS Capture

Run:

```bash
sudo tcpdump -i eth0 port 53 -nn
```

### Step 2: Generate DNS Requests

Run:

```bash
nslookup google.com
```

Then try:

```bash
nslookup github.com
```

### Step 3: Observe the Queries

Look for DNS query information, including:

- Source IP address.
- Destination IP address.
- Source port.
- Destination port.
- Requested domain.
- Query type, such as A or AAAA.

### Step 4: Capture DNS Traffic into a File

Run:

```bash
sudo tcpdump -i eth0 port 53 -w dns-lab.pcap
```

Generate a few DNS requests and stop the capture.

### Step 5: Read the DNS Capture

Run:

```bash
tcpdump -r dns-lab.pcap -nn
```

### What You Learned

- How DNS queries appear in packet captures.
- How to identify DNS requests and responses.
- How to save DNS traffic for later investigation.

**Note:** Traditional DNS commonly uses UDP port 53, but DNS can also use TCP port 53. DNS over HTTPS and DNS over TLS use different ports and are not covered by this simple port 53 filter.

---

# Lab 09 – Capture HTTP and HTTPS Traffic

## Objective

Understand the difference between HTTP and HTTPS traffic in packet captures.

### Step 1: Capture HTTP Traffic

Run:

```bash
sudo tcpdump -i eth0 'tcp port 80' -nn
```

### Step 2: Generate HTTP Traffic

Run:

```bash
curl -I http://example.com
```

Observe the packets generated by the request.

### Step 3: Inspect HTTP Packet Contents

Run:

```bash
sudo tcpdump -i eth0 -A 'tcp port 80'
```

Where HTTP data is present in the captured packets, you may see readable request headers.

### Step 4: Capture HTTPS Traffic

Stop the previous capture and run:

```bash
sudo tcpdump -i eth0 'tcp port 443' -nn
```

In another terminal, run:

```bash
curl -I https://example.com
```

### Step 5: Compare Both Captures

| Feature | HTTP | HTTPS |
|---|---|---|
| Common port | 80 | 443 |
| Encryption | No transport encryption by default | TLS encryption |
| Readable application data | Often visible | Encrypted |
| Connection metadata | Visible | Visible |

### What You Learned

- How to capture HTTP traffic.
- How to capture HTTPS traffic.
- Why HTTP content may be readable.
- Why HTTPS application content is encrypted.

---

# Lab 10 – Investigate Suspicious Network Traffic

## Objective

Use Tcpdump filters to investigate unusual network activity in an authorized lab environment.

### Scenario

Imagine that a workstation is communicating with an unfamiliar external IP address.

As a SOC analyst, your task is to inspect the traffic and collect useful information.

### Step 1: Monitor Traffic from a Workstation

Replace the example IP address with the IP address of your test machine.

```bash
sudo tcpdump -i eth0 host 192.168.1.20 -nn
```

### Step 2: Identify Outgoing Connections

Run:

```bash
sudo tcpdump -i eth0 'src host 192.168.1.20 and tcp' -nn
```

This displays TCP packets sent from the selected workstation.

### Step 3: Identify Unusual Destination Ports

Run:

```bash
sudo tcpdump -i eth0 'src host 192.168.1.20 and tcp and not (port 80 or port 443 or port 22)' -nn
```

This excludes TCP packets using ports 80, 443, and 22.

Remember that unusual ports are not automatically malicious. Some legitimate applications use non-standard ports.

### Step 4: Save the Traffic

Run:

```bash
sudo tcpdump -i eth0 host 192.168.1.20 -w suspicious-traffic.pcap
```

Generate or observe authorized test traffic, then stop the capture.

### Step 5: Review the Captured Traffic

Run:

```bash
tcpdump -r suspicious-traffic.pcap -nn
```

Look for:

- Source and destination IP addresses.
- Destination ports.
- Repeated connection attempts.
- TCP SYN and RST flags.
- DNS requests.
- Unexpected communication patterns.

### Step 6: Record Your Findings

Create a simple investigation note:

| Field | Observation |
|---|---|
| Source IP | Test workstation IP |
| Destination IP | Observed destination |
| Protocol | TCP, UDP, or ICMP |
| Destination Port | Observed port |
| Activity | Connection attempt or response |
| Evidence | PCAP file |
| Conclusion | Requires further investigation |

### What You Learned

- How to monitor a specific workstation.
- How to identify unusual connections.
- How to preserve packet capture evidence.
- How to record findings during an investigation.

---

# Lab 11 – Create a Combined Network Capture

## Objective

Combine multiple Tcpdump concepts into one practical exercise.

### Step 1: Start a Capture

Run:

```bash
sudo tcpdump -i eth0 -nn -w final-lab.pcap
```

### Step 2: Generate ICMP Traffic

In another terminal:

```bash
ping -c 4 8.8.8.8
```

### Step 3: Generate DNS Traffic

Run:

```bash
nslookup example.com
```

### Step 4: Generate HTTP Traffic

Run:

```bash
curl -I http://example.com
```

### Step 5: Generate HTTPS Traffic

Run:

```bash
curl -I https://example.com
```

### Step 6: Stop the Capture

Return to the Tcpdump terminal and press `CTRL + C`.

### Step 7: Analyze the Complete Capture

Read the saved file:

```bash
tcpdump -r final-lab.pcap -nn
```

### Step 8: Filter ICMP Packets

```bash
tcpdump -r final-lab.pcap icmp -nn
```

### Step 9: Filter DNS Packets

```bash
tcpdump -r final-lab.pcap port 53 -nn
```

### Step 10: Filter TCP Packets

```bash
tcpdump -r final-lab.pcap tcp -nn
```

### Step 11: Display Packet Contents

```bash
tcpdump -r final-lab.pcap -X
```

### Final Lab Questions

After completing the capture, answer these questions:

1. Which source IP address generated the ICMP requests?
2. Which destination IP address received the ICMP requests?
3. Which port was used for DNS?
4. What is the difference between HTTP and HTTPS traffic?
5. Can you identify the TCP three-way handshake?
6. Which TCP flags were visible?
7. Can you identify DNS queries and responses?
8. What information can you read from the HTTPS capture?
9. Why is saving a PCAP file useful?
10. Which command displays packet contents in hexadecimal and ASCII?

---

# Troubleshooting Common Lab Issues

## 1. Permission Denied

**Problem:**

```text
You don't have permission to capture on that device
```

**Solution:**

Run Tcpdump using `sudo`:

```bash
sudo tcpdump -i eth0
```

## 2. Interface Does Not Exist

**Problem:**

```text
tcpdump: eth0: No such device exists
```

**Solution:**

Check available interfaces:

```bash
ip a
```

Use the correct interface name.

## 3. No Packets Are Captured

Possible reasons:

- The selected interface is incorrect.
- No traffic is being generated.
- The filter is too restrictive.
- The machine is not connected to the network.

**Solution:** Check the interface and temporarily remove the filter.

```bash
sudo tcpdump -i eth0 -nn
```

## 4. DNS Traffic Is Not Visible

Possible reasons:

- DNS information is already cached.
- The system uses a local DNS resolver.
- DNS traffic is encrypted using DoH or DoT.
- The capture is running on the wrong interface.

**Solution:** Generate a new DNS request and check the interface and resolver configuration.

## 5. HTTP Content Is Not Visible

Possible reasons:

- The website redirected the request to HTTPS.
- The capture started after the relevant packets.
- The HTTP data was split across multiple packets.
- The filter does not match the actual traffic.

**Solution:** Check the captured packets and verify whether the connection actually used HTTP.

---

# Practical Challenge Tasks

Complete these tasks without looking at the solutions in previous sections.

1. Capture all traffic from your network interface.
2. Capture packets from a specific IP address.
3. Capture only incoming packets from a specific host.
4. Capture only outgoing TCP packets.
5. Capture DNS traffic.
6. Capture HTTP and HTTPS traffic.
7. Exclude SSH traffic from a capture.
8. Capture packets using both AND and OR operators.
9. Save packets into a PCAP file.
10. Read a saved PCAP file.
11. Identify SYN and SYN-ACK packets.
12. Capture TCP RST packets.
13. Display packet contents in ASCII.
14. Display packet contents in hexadecimal.
15. Investigate an unfamiliar connection in an authorized lab.

---

# Quick Command Reference

| Task | Command |
|---|---|
| List interfaces | `sudo tcpdump -D` |
| Capture live traffic | `sudo tcpdump -i eth0` |
| Capture without name resolution | `sudo tcpdump -i eth0 -nn` |
| Capture a specific host | `sudo tcpdump -i eth0 host 192.168.1.10` |
| Capture a specific port | `sudo tcpdump -i eth0 port 53` |
| Capture TCP traffic | `sudo tcpdump -i eth0 tcp` |
| Capture UDP traffic | `sudo tcpdump -i eth0 udp` |
| Capture ICMP traffic | `sudo tcpdump -i eth0 icmp` |
| Capture and save packets | `sudo tcpdump -i eth0 -w capture.pcap` |
| Read a PCAP file | `tcpdump -r capture.pcap` |
| Display ASCII | `sudo tcpdump -i eth0 -A` |
| Display hexadecimal | `sudo tcpdump -i eth0 -x` |
| Display hex and ASCII | `sudo tcpdump -i eth0 -X` |
| Capture SYN without ACK | `sudo tcpdump -i eth0 'tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn'` |
| Capture TCP RST | `sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-rst != 0'` |

Replace `eth0` with your actual interface name.

---

# Key Takeaways

After completing these practical labs, you should be able to:

- Identify network interfaces.
- Capture live network traffic.
- Filter packets using IP addresses and port numbers.
- Use logical operators to create advanced filters.
- Save and analyze PCAP files.
- Understand TCP flags and connection behavior.
- Inspect packet contents using ASCII and hexadecimal output.
- Monitor DNS, HTTP, and HTTPS traffic.
- Investigate unusual network connections.
- Collect packet evidence for troubleshooting and security analysis.

## Conclusion

Practical experience is important when learning network analysis. Reading commands is not enough; you must run them, generate network traffic, observe packet output, and understand what each packet represents.

Tcpdump is a useful tool for network troubleshooting, SOC monitoring, penetration testing, and incident response.

Continue practicing these labs with your own virtual machines and authorized test networks. Try changing filters, combining conditions, and analyzing saved PCAP files to improve your packet analysis skills.

**Next Module:** 12 – Troubleshooting
