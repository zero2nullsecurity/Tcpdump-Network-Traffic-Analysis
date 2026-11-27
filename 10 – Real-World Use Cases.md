# 10 – Real-World Use Cases

## 1. Introduction

In the previous modules, we learned how to use Tcpdump for capturing and analyzing network traffic.

We covered:

- Introduction to Tcpdump.
- Installation and setup.
- Basic packet capture.
- Packet filtering.
- Logical operators.
- PCAP file analysis.
- Advanced filtering.
- TCP flags analysis.
- Displaying packet contents.

Now, we will apply these concepts to real-world situations.

In a real organization, network traffic is continuously generated between computers, servers, applications, and external systems.

Sometimes, this traffic behaves normally. However, sometimes we may find unusual communication, connection failures, unexpected DNS requests, or suspicious connections to external systems.

Tcpdump helps us capture and investigate this network activity.

### Why are real-world use cases important?

Learning commands is only the first step.

A cybersecurity professional must also understand:

- When to use a particular command.
- What information to look for.
- How to identify unusual network behavior.
- How to investigate a network problem.
- How to analyze captured packets.
- How to document investigation findings.

In this module, we will explore practical scenarios that demonstrate how Tcpdump can be used in networking and cybersecurity.

## 2. Use Case 1 – Monitoring Live Network Traffic

### Scenario

Imagine you are working as a network administrator in an organization.

Several computers are connected to the same network, and you want to understand what network communication is happening.

You need to monitor traffic from your Linux system.

Tcpdump allows you to capture live packets from a network interface.

### Step 1: Identify the network interface

First, check the available network interfaces.

```bash
ip a
```

This command displays network interfaces and their IP addresses.

You can also use:

```bash
sudo tcpdump -D
```

This displays the interfaces available to Tcpdump.

### Step 2: Capture live traffic

Run:

```bash
sudo tcpdump -i eth0 -nn
```

**Command breakdown:**

| Option | Meaning |
|---|---|
| `sudo` | Runs Tcpdump with the required permissions |
| `-i eth0` | Selects the network interface |
| `-nn` | Disables hostname and service-name resolution |

This command continuously displays packets captured on the selected interface.

### Step 3: Limit the number of packets

If you only want to capture 20 packets, use:

```bash
sudo tcpdump -i eth0 -nn -c 20
```

The `-c 20` option stops the capture after 20 packets have been captured.

### Step 4: Save the captured traffic

You can save the packets into a PCAP file:

```bash
sudo tcpdump -i eth0 -nn -c 100 -w network-monitoring.pcap
```

This saves up to 100 packets into `network-monitoring.pcap`.

### What can we learn?

By monitoring live network traffic, we can identify:

- Which systems are communicating.
- Which protocols are being used.
- Which ports are involved.
- Whether communication is going to internal or external IP addresses.
- Whether unexpected traffic is present.

### Real-world application

Network administrators use packet capture to understand network activity and investigate unexpected communication.

SOC analysts can also use packet captures as supporting evidence when investigating security alerts.

## 3. Use Case 2 – Investigating Suspicious Outbound Connections

### Scenario

Imagine an employee's computer is communicating with an unfamiliar external IP address.

The security team wants to investigate whether the connection is expected or suspicious.

Unexpected outbound connections can sometimes be related to:

- Malware communication.
- Unauthorized applications.
- Unusual remote connections.
- Data transfer to external systems.
- Misconfigured software.

However, an unfamiliar IP address does not automatically indicate malicious activity.

We need to investigate the communication before making a decision.

### Step 1: Monitor outbound TCP traffic

```bash
sudo tcpdump -i eth0 -nn 'tcp and dst net not 192.168.1.0/24'
```

This example is intended to show TCP packets whose destination is outside the specified internal network.

For a more reliable investigation, first confirm your actual internal network ranges. Organizations may use multiple private networks, VPN ranges, and other internal address spaces.

### Step 2: Investigate a specific destination IP

Imagine that you have identified this destination IP:

```text
203.0.113.50
```

The address belongs to the documentation range reserved for examples. Replace it with the actual destination address from your authorized investigation.

Run:

```bash
sudo tcpdump -i eth0 -nn host 203.0.113.50
```

This displays packets involving the specified IP address.

### Step 3: Check outbound connections to a specific port

For example, to inspect TCP traffic going to destination port 443:

```bash
sudo tcpdump -i eth0 -nn 'tcp and dst port 443'
```

This displays TCP packets whose destination port is 443.

### Step 4: Save the traffic for investigation

```bash
sudo tcpdump -i eth0 -nn host 203.0.113.50 -w suspicious-connection.pcap
```

This saves matching traffic into a PCAP file.

### Step 5: Analyze the saved file

```bash
sudo tcpdump -nn -tttt -r suspicious-connection.pcap
```

This displays the captured packets with readable timestamps.

### What should an analyst investigate?

- Which internal system initiated the connection?
- What is the destination IP address?
- Which destination port was used?
- When did the connection occur?
- Was the connection repeated?
- Is the destination expected for the application?
- Is there supporting evidence from DNS, endpoint logs, or security alerts?

### Real-world application

SOC analysts investigate outbound connections to identify potentially suspicious communication.

Tcpdump helps them examine the network evidence, but additional information is required before classifying a connection as malicious.

## 4. Use Case 3 – Detecting Possible Port Scanning Activity

### Scenario

Imagine that a system is sending TCP connection requests to many different ports on another system.

This behavior may indicate port-scanning activity.

Port scanning is a technique used to discover which network ports are open, closed, or filtered.

It is commonly used during authorized penetration testing, but attackers may also use it to discover available services.

### What happens during a TCP SYN scan?

A TCP SYN scan sends connection requests using the SYN flag.

The target may respond in different ways:

| Target Response | Possible Meaning |
|---|---|
| SYN-ACK | The port is likely open |
| RST | The port is likely closed |
| No response | The port may be filtered, or the packet may have been lost |
| ICMP error | A network device may be rejecting the traffic |

### Step 1: Capture initial SYN packets

```bash
sudo tcpdump -i eth0 -nn 'tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn'
```

This displays TCP packets where SYN is set and ACK is not set.

These commonly represent initial TCP connection requests.

### Step 2: Monitor traffic from a specific source

Imagine the source system has this example IP address:

```text
192.168.1.50
```

Run:

```bash
sudo tcpdump -i eth0 -nn 'src host 192.168.1.50 and tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn'
```

This displays initial SYN packets coming from that source address.

### Step 3: Check the destination ports

```bash
sudo tcpdump -i eth0 -nn 'src host 192.168.1.50 and tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn'
```

Observe the destination port numbers in the output.

If the same source is attempting connections to many different destination ports in a short period, this may deserve further investigation.

### Step 4: Save the traffic

```bash
sudo tcpdump -i eth0 -nn 'tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn' -w syn-traffic.pcap
```

### Step 5: Analyze the saved traffic

```bash
sudo tcpdump -nn -tttt -r syn-traffic.pcap
```

Review the timestamps, source addresses, destination addresses, and destination ports.

### What should an analyst investigate?

- Is one source contacting many destination ports?
- Are the connection attempts happening very quickly?
- Are the destination ports sequential or unusual?
- Are responses coming from the target?
- Is the source a known vulnerability scanner?
- Was the activity authorized?

### Real-world application

SOC analysts use TCP flag analysis to investigate possible port scans.

Network administrators can also identify unexpected scanning activity inside their networks.

**Important:** A large number of SYN packets does not automatically prove a port scan. Browsers, applications, monitoring systems, and legitimate scanners can also generate multiple connection attempts.

Only scan systems you own or have explicit permission to test.

## 5. Use Case 4 – Troubleshooting TCP Connection Problems

### Scenario

Imagine that an employee cannot access an internal application.

The application server is available, but the connection is failing.

The network administrator needs to understand where the communication is failing.

Tcpdump can help inspect the TCP connection process.

### Step 1: Capture TCP traffic

```bash
sudo tcpdump -i eth0 -nn tcp
```

This displays TCP packets on the selected interface.

### Step 2: Identify the TCP three-way handshake

A normal TCP connection commonly starts with three packets:

1. SYN
2. SYN-ACK
3. ACK

Example:

```text
CLIENT                         SERVER
  |                              |
  | -------- SYN --------------> |
  |                              |
  | <----- SYN + ACK ----------- |
  |                              |
  | -------- ACK --------------> |
  |                              |
  |     CONNECTION ESTABLISHED   |
```

### Step 3: Capture initial SYN packets

```bash
sudo tcpdump -i eth0 -nn 'tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn'
```

This displays initial SYN packets without ACK.

### Step 4: Capture SYN-ACK packets

```bash
sudo tcpdump -i eth0 -nn 'tcp[tcpflags] & (tcp-syn|tcp-ack) == (tcp-syn|tcp-ack)'
```

This displays packets containing both SYN and ACK.

### Step 5: Capture RST packets

```bash
sudo tcpdump -i eth0 -nn 'tcp[tcpflags] & tcp-rst != 0'
```

This displays TCP packets where the RST flag is set.

### Understanding possible results

| Observation | Possible Explanation |
|---|---|
| SYN is sent but no SYN-ACK is seen | Server may be unavailable, traffic may be filtered, or the response may take another path |
| SYN-ACK is received | The server is responding to the connection request |
| RST is received | The connection is being reset |
| Three-way handshake completes | TCP connection establishment succeeded |
| Repeated SYN packets | Connection attempts may be retransmitted |

### Step 6: Save the connection traffic

```bash
sudo tcpdump -i eth0 -nn host 192.168.1.20 -w connection-troubleshooting.pcap
```

Replace the example IP with the actual server or client address.

### Real-world application

Network administrators use TCP packet analysis to troubleshoot:

- Applications that cannot connect.
- Services that refuse connections.
- Unexpected connection resets.
- Network filtering problems.
- TCP communication failures.

**Important:** A successful TCP handshake does not prove that the application itself is working correctly. Application logs and server-side checks may also be needed.

## 6. Use Case 5 – Investigating DNS Traffic

### Scenario

Imagine that a SOC analyst receives an alert about unusual DNS requests from an internal computer.

DNS is used to translate domain names into IP addresses.

Unusual DNS activity may be related to:

- Suspicious domain lookups.
- Malware communication.
- Misconfigured applications.
- DNS tunneling.
- Unexpected software behavior.

The analyst wants to inspect DNS traffic.

### Step 1: Capture DNS traffic

```bash
sudo tcpdump -i eth0 -nn port 53
```

This displays traffic involving port 53, commonly used by DNS.

### Step 2: Display DNS packet contents

```bash
sudo tcpdump -i eth0 -X -nn port 53
```

This displays packet bytes in hexadecimal and ASCII.

### Step 3: Generate DNS requests

On your own Linux system, run:

```bash
nslookup example.com
```

This generates a DNS lookup request.

You can also use:

```bash
dig example.com
```

This performs a DNS query and displays the response.

### Step 4: Save DNS traffic

```bash
sudo tcpdump -i eth0 -nn port 53 -w dns-investigation.pcap
```

### Step 5: Read the saved file

```bash
sudo tcpdump -nn -tttt -r dns-investigation.pcap port 53
```

### What should an analyst investigate?

- Which internal system generated the request?
- Which domain was requested?
- How frequently are queries being sent?
- Are there unusual or unexpected domain names?
- Are DNS requests receiving responses?
- Is the system using an expected DNS server?

### Real-world application

SOC analysts use DNS traffic analysis to investigate suspicious domain lookups and unusual DNS behavior.

**Important:** DNS traffic may use encrypted protocols such as DNS over HTTPS or DNS over TLS. In those cases, ordinary packet capture may not reveal the DNS query contents.

## 7. Use Case 6 – Investigating HTTP Traffic

### Scenario

Imagine a security analyst is investigating communication between an internal computer and a web server.

The analyst wants to understand which HTTP requests are being sent.

Traditional HTTP traffic is not encrypted by HTTP itself, so some request and response information may be readable in a packet capture.

### Step 1: Capture HTTP traffic

```bash
sudo tcpdump -i eth0 -nn port 80
```

This displays packets involving port 80.

### Step 2: Display readable packet contents

```bash
sudo tcpdump -i eth0 -A -nn port 80
```

The `-A` option displays packet contents in ASCII where possible.

### Step 3: Generate a test HTTP request

```bash
curl -I http://example.com
```

This sends an HTTP HEAD request to the test website.

### Step 4: Save HTTP traffic

```bash
sudo tcpdump -i eth0 -nn port 80 -w http-investigation.pcap
```

### Step 5: Analyze the saved capture

```bash
sudo tcpdump -A -nn -r http-investigation.pcap
```

### What information may be visible?

An HTTP request may contain:

```text
HEAD / HTTP/1.1
Host: example.com
User-Agent: curl/8.0
Accept: */*
```

This example is illustrative. Your actual output will depend on the captured traffic.

An analyst may inspect:

- HTTP request method.
- Requested resource path.
- Host header.
- User-Agent.
- HTTP response information.

### Real-world application

Security analysts can inspect unencrypted HTTP traffic to investigate suspicious requests, unusual web communication, and possible plaintext data exposure.

**Important:** Never use real credentials or sensitive personal information in unencrypted HTTP testing.

## 8. Use Case 7 – Investigating HTTPS and TLS Traffic

### Scenario

An organization uses HTTPS for most of its web communication.

A SOC analyst wants to investigate connections to an external server, but the application data is encrypted.

Tcpdump can still provide useful network information.

### Step 1: Capture HTTPS traffic

```bash
sudo tcpdump -i eth0 -nn port 443
```

This displays packets involving port 443.

### Step 2: Display packet bytes

```bash
sudo tcpdump -i eth0 -X -nn port 443
```

This displays hexadecimal and ASCII representations of the captured packet bytes.

### Step 3: Generate HTTPS traffic

```bash
curl -I https://example.com
```

This sends an HTTPS HEAD request.

### Step 4: Save the traffic

```bash
sudo tcpdump -i eth0 -nn port 443 -w https-investigation.pcap
```

### Step 5: Analyze the saved capture

```bash
sudo tcpdump -nn -tttt -r https-investigation.pcap
```

### What can we investigate?

Even though application data is encrypted, we may still observe:

- Source IP address.
- Destination IP address.
- Source and destination ports.
- Packet timestamps.
- Packet sizes.
- TCP connection behavior.
- Some TLS handshake information.

### Why is this useful?

A security analyst can investigate connection timing, communication frequency, and destination information without reading the encrypted application content.

**Important:** HTTPS normally protects application data using TLS. Do not assume that hexadecimal output reveals the original HTTP request.

## 9. Use Case 8 – Detecting Repeated TCP Connection Resets

### Scenario

Imagine that users report that an application frequently disconnects.

A network administrator suspects that TCP connections are being reset.

TCP RST packets are used to reset connections.

### Step 1: Capture RST packets

```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-rst != 0'
```

This displays TCP packets containing the RST flag.

### Step 2: Focus on a specific system

```bash
sudo tcpdump -i eth0 -nn 'host 192.168.1.20 and tcp[tcpflags] & tcp-rst != 0'
```

This displays RST packets involving the specified IP address.

### Step 3: Save the packets

```bash
sudo tcpdump -i eth0 -nn 'tcp[tcpflags] & tcp-rst != 0' -w tcp-reset-investigation.pcap
```

### Step 4: Analyze the captured packets

```bash
sudo tcpdump -nn -tttt -vv -r tcp-reset-investigation.pcap
```

Review:

- Source IP.
- Destination IP.
- Source port.
- Destination port.
- Timestamp.
- TCP sequence information.
- Frequency of reset packets.

### Possible reasons for RST packets

- A connection attempt reaches a closed port.
- An application closes a connection unexpectedly.
- A system receives an invalid TCP segment.
- A firewall or network device generates a reset.
- An application or operating system resets a connection.

### Real-world application

Network administrators and SOC analysts use RST packet analysis to investigate connection failures and unexpected network behavior.

**Important:** A RST packet alone does not identify the exact cause of a connection failure. Check endpoint and firewall logs as well.

## 10. Use Case 9 – Monitoring Traffic from a Specific Device

### Scenario

Imagine an organization has many computers connected to its internal network.

The security team wants to investigate traffic involving one particular computer.

They know its IP address and want to monitor its communication.

### Step 1: Capture traffic involving the device

```bash
sudo tcpdump -i eth0 -nn host 192.168.1.50
```

This displays packets where `192.168.1.50` is either the source or destination.

### Step 2: Monitor only outgoing traffic

```bash
sudo tcpdump -i eth0 -nn src host 192.168.1.50
```

This displays packets whose source IP is `192.168.1.50`.

### Step 3: Monitor incoming traffic

```bash
sudo tcpdump -i eth0 -nn dst host 192.168.1.50
```

This displays packets whose destination IP is `192.168.1.50`.

### Step 4: Monitor a specific protocol

For TCP traffic:

```bash
sudo tcpdump -i eth0 -nn 'host 192.168.1.50 and tcp'
```

For UDP traffic:

```bash
sudo tcpdump -i eth0 -nn 'host 192.168.1.50 and udp'
```

### Step 5: Save the traffic

```bash
sudo tcpdump -i eth0 -nn host 192.168.1.50 -w device-investigation.pcap
```

### What should an analyst investigate?

- Which systems communicate with this device?
- Which external addresses does it contact?
- Which ports are being used?
- Is the communication expected?
- Are there unusual connection patterns?

### Real-world application

This technique is useful when investigating a device that has generated a security alert or is experiencing network problems.

## 11. Use Case 10 – Investigating Large or Unusual Packets

### Scenario

Imagine a network monitoring system reports unusual network traffic.

An analyst wants to inspect packets that are larger than normal.

Large packets may be generated by normal applications, file transfers, backups, or other network activities.

However, unexpected packet sizes may also deserve investigation.

### Step 1: Capture packets larger than 1000 bytes

```bash
sudo tcpdump -i eth0 greater 1000
```

This displays packets larger than 1000 bytes.

### Step 2: Capture smaller packets

```bash
sudo tcpdump -i eth0 less 100
```

This displays packets smaller than 100 bytes.

### Step 3: Filter a packet-size range

```bash
sudo tcpdump -i eth0 'greater 500 and less 1500'
```

This displays packets larger than 500 bytes and smaller than 1500 bytes.

### Step 4: Save large packets

```bash
sudo tcpdump -i eth0 greater 1000 -w large-packets.pcap
```

### Step 5: Analyze the capture

```bash
sudo tcpdump -nn -vv -r large-packets.pcap
```

### What should an analyst investigate?

- Which systems are sending large packets?
- Which protocols are involved?
- Is the traffic expected?
- Is it related to a file transfer or backup?
- Is there an unusual pattern?

### Real-world application

Packet-size filtering helps analysts narrow down traffic during performance troubleshooting and network investigations.

**Important:** Large packets are not automatically malicious. Packet size must be considered along with the protocol, communication pattern, and network environment.

## 12. Use Case 11 – Investigating Broadcast and ARP Traffic

### Scenario

Imagine that a network administrator is investigating communication problems between devices on a local network.

They want to inspect ARP requests and replies.

ARP stands for Address Resolution Protocol.

It is used in IPv4 local networks to discover the MAC address associated with an IPv4 address.

### Step 1: Capture ARP traffic

```bash
sudo tcpdump -i eth0 -nn arp
```

This displays ARP packets.

### Step 2: Display additional packet details

```bash
sudo tcpdump -i eth0 -nn -vv arp
```

This requests more detailed ARP packet information.

### Step 3: Capture Ethernet broadcast traffic

```bash
sudo tcpdump -i eth0 -nn ether broadcast
```

This displays Ethernet broadcast frames.

### Step 4: Save ARP traffic

```bash
sudo tcpdump -i eth0 -nn arp -w arp-investigation.pcap
```

### Step 5: Read the saved file

```bash
sudo tcpdump -nn -r arp-investigation.pcap
```

### What should an analyst investigate?

- Which devices are sending ARP requests?
- Which IP addresses are being queried?
- Are multiple devices claiming the same IP address?
- Are unexpected ARP messages present?
- Is there evidence of unusual ARP behavior?

### Real-world application

ARP traffic analysis can help investigate local network problems and suspicious address-resolution behavior.

For example, unusual ARP replies may deserve further investigation for possible ARP spoofing.

**Important:** An unusual ARP packet is not enough to confirm ARP spoofing. Compare the traffic with the network's expected IP-to-MAC mappings and other evidence.

## 13. Use Case 12 – Analyzing a PCAP File During Incident Response

### Scenario

Imagine that a security team detects suspicious activity on a computer.

The team has already captured network traffic and saved it into a PCAP file.

Now, a SOC analyst needs to investigate the saved traffic.

This is a common situation during incident response.

### Step 1: Check the PCAP file

```bash
ls -lh suspicious-traffic.pcap
```

This displays the file size.

### Step 2: Identify the file type

```bash
file suspicious-traffic.pcap
```

This helps identify the file format.

### Step 3: Calculate the file hash

```bash
sha256sum suspicious-traffic.pcap
```

This calculates a SHA-256 hash.

Record the hash so that the file can be checked later for unexpected changes.

### Step 4: Read the captured packets

```bash
sudo tcpdump -nn -r suspicious-traffic.pcap
```

This displays the captured packets.

### Step 5: Display readable timestamps

```bash
sudo tcpdump -tttt -nn -r suspicious-traffic.pcap
```

This helps the analyst understand when communication occurred.

### Step 6: Investigate TCP traffic

```bash
sudo tcpdump -nn -r suspicious-traffic.pcap tcp
```

This displays TCP packets.

### Step 7: Investigate DNS traffic

```bash
sudo tcpdump -nn -r suspicious-traffic.pcap port 53
```

This displays packets involving port 53.

### Step 8: Investigate HTTP traffic

```bash
sudo tcpdump -A -nn -r suspicious-traffic.pcap port 80
```

This attempts to display readable ASCII content from HTTP-related packets.

### Step 9: Investigate TCP SYN packets

```bash
sudo tcpdump -nn -r suspicious-traffic.pcap 'tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn'
```

This displays initial SYN packets without ACK.

### Step 10: Investigate TCP RST packets

```bash
sudo tcpdump -nn -r suspicious-traffic.pcap 'tcp[tcpflags] & tcp-rst != 0'
```

This displays TCP reset packets.

### What should the analyst document?

- Incident or investigation reference.
- PCAP filename.
- File hash.
- Capture time range.
- Important source and destination IP addresses.
- Relevant ports and protocols.
- Suspicious communication patterns.
- Supporting evidence.
- Findings and limitations.

### Real-world application

PCAP analysis helps incident responders understand network activity associated with a security incident.

It can support investigations into suspicious connections, malware communication, unusual DNS activity, and possible data transfers.

**Important:** Preserve the original capture. Perform analysis on a working copy when possible, and follow your organization's evidence-handling procedures.

## 14. Practical Lab – Multiple Real-World Investigations

Now let's combine the commands from the previous modules into one practical lab.

We will generate normal network traffic, capture it, and investigate different types of communication.

### Lab requirements

- Kali Linux or Ubuntu.
- Tcpdump installed.
- Terminal access.
- Internet connection.
- A network interface with traffic.

### Step 1: Create a lab directory

```bash
mkdir -p ~/tcpdump-lab/real-world
cd ~/tcpdump-lab/real-world
```

This creates a directory for the practical lab.

### Step 2: Identify your network interface

```bash
ip a
```

Check which network interface is active.

You can also run:

```bash
sudo tcpdump -D
```

For this lab, we will use the `any` interface if it is available.

### Step 3: Start packet capture

Run:

```bash
sudo tcpdump -i any -nn -c 200 -w real-world-traffic.pcap
```

This captures up to 200 packets and saves them into `real-world-traffic.pcap`.

### Step 4: Generate different types of traffic

Open another terminal and run these commands one by one.

Generate ICMP traffic:

```bash
ping -c 5 1.1.1.1
```

Generate DNS traffic:

```bash
nslookup example.com
```

Generate HTTPS traffic:

```bash
curl -I https://example.com
```

Generate HTTP traffic:

```bash
curl -I http://example.com
```

These commands generate different types of network activity.

Your actual traffic may differ depending on DNS configuration, network routing, proxies, redirects, and the behavior of the remote website.

### Step 5: Wait for the capture to finish

Tcpdump will stop after capturing 200 packets.

If the capture does not reach 200 packets, press `Ctrl+C` to stop it.

### Step 6: Verify the PCAP file

```bash
ls -lh real-world-traffic.pcap
```

### Step 7: Read the captured traffic

```bash
sudo tcpdump -nn -r real-world-traffic.pcap
```

Review the source IP addresses, destination IP addresses, protocols, and ports.

### Step 8: Investigate ICMP traffic

```bash
sudo tcpdump -nn -r real-world-traffic.pcap icmp
```

Observe the ping requests and replies, if they were captured.

### Step 9: Investigate DNS traffic

```bash
sudo tcpdump -nn -r real-world-traffic.pcap port 53
```

Check for DNS requests and responses.

### Step 10: Investigate TCP traffic

```bash
sudo tcpdump -nn -r real-world-traffic.pcap tcp
```

Observe the TCP communication.

### Step 11: Investigate initial SYN packets

```bash
sudo tcpdump -nn -r real-world-traffic.pcap 'tcp[tcpflags] & (tcp-syn|tcp-ack) == tcp-syn'
```

Look for initial TCP connection requests.

### Step 12: Investigate RST packets

```bash
sudo tcpdump -nn -r real-world-traffic.pcap 'tcp[tcpflags] & tcp-rst != 0'
```

Check whether any TCP resets are present.

### Step 13: Inspect HTTP packet contents

```bash
sudo tcpdump -A -nn -r real-world-traffic.pcap port 80
```

Look for readable HTTP request or response data.

### Step 14: Inspect HTTPS traffic

```bash
sudo tcpdump -nn -r real-world-traffic.pcap port 443
```

Review the connection information and packet behavior.

Remember that the HTTPS application data is encrypted.

### Step 15: Check the PCAP hash

```bash
sha256sum real-world-traffic.pcap
```

Save the hash for file-integrity verification.

### Lab result

You have captured different types of network traffic and investigated them using Tcpdump.

You have also practiced:

- Live traffic capture.
- PCAP file analysis.
- Protocol filtering.
- TCP flag analysis.
- DNS investigation.
- HTTP and HTTPS inspection.
- File-integrity checking.

## 15. How Tcpdump Is Used in Different Cybersecurity Roles

Tcpdump is useful in several technical roles.

Let's understand how different professionals use it.

### 15.1 SOC Analyst – Level 1

A SOC L1 analyst monitors security alerts and performs initial investigations.

Tcpdump can help with:

- Checking suspicious IP addresses.
- Inspecting network protocols.
- Reviewing DNS requests.
- Investigating unusual TCP connections.
- Providing packet-level evidence for escalation.

For example, when a monitoring system generates an alert about suspicious outbound traffic, the analyst can inspect a PCAP file to understand the communication.

### 15.2 SOC Analyst – Level 2

A SOC L2 analyst performs deeper investigations and helps determine the scope and impact of security incidents.

Tcpdump can help with:

- Investigating suspicious network behavior.
- Analyzing connection patterns.
- Reviewing DNS and HTTP traffic.
- Investigating possible scanning activity.
- Supporting incident-response investigations.

### 15.3 Network Administrator

A network administrator manages network connectivity and performance.

Tcpdump can help with:

- Troubleshooting connection failures.
- Identifying unexpected traffic.
- Inspecting DNS problems.
- Monitoring TCP connections.
- Investigating packet loss and retransmissions.

### 15.4 Penetration Tester

A penetration tester evaluates systems with permission to identify security weaknesses.

Tcpdump can help with:

- Observing traffic generated during authorized testing.
- Understanding TCP connection behavior.
- Inspecting protocol communication.
- Analyzing packets generated by scanning tools.
- Reviewing network traffic during an assessment.

### 15.5 Incident Responder

An incident responder investigates security incidents and helps organizations understand what happened.

Tcpdump can help with:

- Reviewing packet captures.
- Identifying suspicious communication.
- Building a network activity timeline.
- Investigating possible malware communication.
- Preserving and analyzing network evidence.

## 16. Common Mistakes During Real-World Packet Analysis

| Mistake | Explanation | Solution |
|---|---|---|
| Capturing from the wrong interface | Relevant traffic may not appear | Check interfaces using `ip a` and `tcpdump -D` |
| Using very broad filters | Too much unrelated traffic is captured | Use specific host, network, protocol, and port filters |
| Ignoring timestamps | The order of events may be misunderstood | Use `-tttt` |
| Assuming every unusual connection is malicious | Legitimate applications also generate unusual-looking traffic | Investigate the context and supporting evidence |
| Expecting HTTPS data in plaintext | TLS encrypts application data | Analyze visible metadata and TLS information |
| Ignoring DNS traffic | DNS can provide useful information about system communication | Investigate DNS queries and responses |
| Saving captures without checking storage | PCAP files can become very large | Use appropriate filters and capture limits |
| Sharing PCAP files carelessly | Captures may contain confidential information | Protect files and follow data-handling rules |
| Treating one packet as complete evidence | A single packet may not explain the full communication | Examine packet sequences and other evidence |

## 17. Quick Command Reference

| Command | Purpose |
|---|---|
| `tcpdump -i eth0 -nn` | Monitor live network traffic |
| `tcpdump -i eth0 -nn -c 20` | Capture 20 packets |
| `tcpdump -i eth0 -nn host 192.168.1.50` | Monitor traffic involving a specific host |
| `tcpdump -i eth0 -nn port 53` | Monitor DNS-related traffic |
| `tcpdump -i eth0 -nn port 80` | Monitor traffic involving HTTP's default port |
| `tcpdump -i eth0 -nn port 443` | Monitor traffic involving HTTPS's default port |
| `tcpdump -i eth0 -nn tcp` | Monitor TCP traffic |
| `tcpdump -i eth0 -nn udp` | Monitor UDP traffic |
| `tcpdump -i eth0 -nn icmp` | Monitor ICMP traffic |
| `tcpdump -i eth0 'tcp[tcpflags] & tcp-rst != 0'` | Monitor TCP RST packets |
| `tcpdump -i eth0 'tcp[tcpflags] & (tcp-syn\|tcp-ack) == tcp-syn'` | Monitor initial TCP SYN packets |
| `tcpdump -i eth0 greater 1000` | Monitor packets larger than 1000 bytes |
| `tcpdump -i eth0 -nn arp` | Monitor ARP traffic |
| `tcpdump -nn -r sample.pcap` | Read a saved PCAP file |
| `tcpdump -tttt -nn -r sample.pcap` | Read packets with readable timestamps |
| `tcpdump -A -nn -r sample.pcap` | Display ASCII packet contents |
| `tcpdump -X -nn -r sample.pcap` | Display hexadecimal and ASCII packet contents |
| `sha256sum sample.pcap` | Calculate the PCAP file's SHA-256 hash |

*Note: Replace `eth0` with your actual network interface. For saved-file analysis, use `-r filename.pcap` instead of a live-capture interface.*

## 18. Practice Questions

Try answering these questions after completing the module.

1. How is Tcpdump used in real-world network monitoring?
2. How can a SOC analyst investigate suspicious outbound connections?
3. What is port scanning?
4. How can TCP SYN packets help identify possible scanning activity?
5. How can Tcpdump help troubleshoot TCP connection problems?
6. Why is DNS traffic useful during security investigations?
7. What information can be identified from HTTP packet contents?
8. Why is HTTPS application data normally encrypted in packet captures?
9. What is the purpose of analyzing TCP RST packets?
10. How can Tcpdump help investigate ARP traffic?
11. Why should a PCAP file's SHA-256 hash be recorded?
12. What information should an analyst document during a PCAP investigation?
13. How is Tcpdump useful for a SOC L1 analyst?
14. What is the difference between network troubleshooting and security investigation?
15. Why should analysts avoid classifying traffic as malicious based on one packet alone?

## 19. Practical Tasks

Complete these tasks using your own Linux system and authorized lab traffic.

- [ ] Monitor live network traffic.
- [ ] Capture traffic and save it into a PCAP file.
- [ ] Investigate communication involving a specific IP address.
- [ ] Identify initial TCP SYN packets.
- [ ] Investigate TCP RST packets.
- [ ] Capture and analyze DNS traffic.
- [ ] Inspect HTTP traffic where available.
- [ ] Inspect HTTPS connection information.
- [ ] Analyze ARP traffic.
- [ ] Filter packets based on packet size.
- [ ] Investigate a saved PCAP file.
- [ ] Calculate the SHA-256 hash of the capture file.
- [ ] Write a short investigation summary describing the traffic you observed.

## 20. Key Takeaways

In this module, we applied Tcpdump to practical networking and cybersecurity situations.

We covered:

- Monitoring live network traffic.
- Investigating suspicious outbound connections.
- Identifying possible port-scanning activity.
- Troubleshooting TCP connection problems.
- Investigating DNS traffic.
- Inspecting HTTP and HTTPS communication.
- Investigating TCP connection resets.
- Monitoring traffic involving a specific device.
- Analyzing large or unusual packets.
- Investigating broadcast and ARP traffic.
- Analyzing PCAP files during incident response.
- Understanding how different cybersecurity professionals use Tcpdump.

**Remember:** Tcpdump is not just a packet-capture tool. It is also a useful investigation tool that helps security professionals understand network communication, troubleshoot problems, and collect evidence.

The most important skill is not simply remembering commands. It is knowing which command to use, what information to look for, and how to interpret the results correctly.

## 21. References and Further Learning

- [Tcpdump Official Website](https://www.tcpdump.org/)
- [Tcpdump Manual Page](https://www.tcpdump.org/manpages/tcpdump.1.html)
- [PCAP Filter Manual](https://www.tcpdump.org/manpages/pcap-filter.7.html)
- [Wireshark Documentation](https://www.wireshark.org/docs/)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [NIST Computer Security Incident Handling Guide](https://csrc.nist.gov/pubs/sp/800/61/r2/final)
- [TryHackMe](https://tryhackme.com/)

---

**Next Module: 11 – Practical Labs**

In the next module, we will bring together the concepts from the previous modules through hands-on exercises. We will practice capturing traffic, applying filters, analyzing PCAP files, identifying TCP flags, and investigating network activity.

**Documentation by Zero2Null Security**
