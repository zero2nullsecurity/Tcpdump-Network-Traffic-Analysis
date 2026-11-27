# 09 – Displaying Packet Contents

## 1. Introduction

In previous modules, we learned how to capture network traffic, filter packets, analyze PCAP files, and understand TCP flags using Tcpdump.

Now, we will learn how to display and inspect the actual contents of network packets.

When we capture network traffic, Tcpdump normally displays information such as:

- Source IP address.
- Destination IP address.
- Source port.
- Destination port.
- Protocol.
- Packet length.
- TCP flags.
- Timestamp.

However, sometimes this information is not enough.

For example, imagine that a computer sends an HTTP request to a web server. We may want to inspect the actual data inside the packet to understand what information is being transmitted.

Tcpdump provides different options to display packet contents in readable formats.

We can use these options to:

- Display packet contents in ASCII.
- Display packet contents in hexadecimal.
- Display hexadecimal and ASCII together.
- Inspect readable application data.
- Examine protocol information.
- Investigate suspicious network communication.

This is useful in network troubleshooting, security monitoring, packet analysis, and digital forensics.

## 2. Understanding Packet Headers and Payloads

Before displaying packet contents, we need to understand two important parts of a network packet:

1. Header
2. Payload

### 2.1 What Is a Packet Header?

A packet header contains information that helps network devices deliver and process the packet.

For example, an IP header contains information such as:

- Source IP address.
- Destination IP address.
- Protocol information.
- Packet length.
- Time To Live (TTL).

A TCP header contains information such as:

- Source port.
- Destination port.
- Sequence number.
- Acknowledgment number.
- TCP flags.
- Window size.

These fields help systems manage network communication.

### 2.2 What Is a Payload?

A payload is the data carried inside a packet.

For example, when you send a message through an application, the message data may be carried inside network packets.

Depending on the protocol, the payload may contain:

- HTTP request information.
- DNS messages.
- Application data.
- File data.
- Other communication information.

If the communication is encrypted, the payload may not be readable as normal text.

### 2.3 Header vs Payload

| Header                                    | Payload                                    |
| ----------------------------------------- | ------------------------------------------ |
| Contains control and delivery information | Contains the data being transported        |
| Helps devices process packets             | Carries application or protocol data       |
| Includes addresses and protocol fields    | May include text, files, or encrypted data |
| Used for packet delivery and handling     | Used to carry communication content        |

### Simple example

Imagine you send a message to another computer.

The packet may contain:

```text
+--------------------------------+
| Ethernet Header                |
+--------------------------------+
| IP Header                      |
+--------------------------------+
| TCP Header                     |
+--------------------------------+
| Application Data (Payload)     |
+--------------------------------+
```

This is a simplified representation of an Ethernet frame carrying an IPv4 packet, a TCP segment, and application data.

Each layer adds its own information to help the data reach the correct destination.

## 3. Why Do We Need to Display Packet Contents?

Default Tcpdump output is useful for understanding basic network communication.

However, sometimes we need to inspect the actual bytes carried inside packets.

For example:

Imagine that a security analyst is investigating suspicious HTTP communication.

The analyst may need to check:

- What HTTP method was used?
- Which URL path was requested?
- Was a suspicious string transmitted?
- Was readable application data present?
- Was the communication encrypted?

Displaying packet contents can help answer some of these questions.

### Common use cases

- Investigating HTTP requests.
- Examining DNS messages.
- Understanding protocol communication.
- Troubleshooting application problems.
- Checking whether sensitive information was transmitted in plaintext.
- Analyzing suspicious network traffic.

**Important:** Packet contents may contain private information. Only capture, inspect, and share network traffic when you are authorized to do so.

## 4. Display Packet Contents Using ASCII – `-A`

Tcpdump provides the `-A` option to display packet contents in ASCII format.

ASCII is a character-encoding standard that represents common letters, numbers, and symbols using numeric values.

For example:

| Character | ASCII Decimal Value |
| --------- | ------------------: |
| A         |                  65 |
| B         |                  66 |
| a         |                  97 |
| b         |                  98 |
| 0         |                  48 |
| 1         |                  49 |

When packet data contains readable text, the `-A` option can help display it in the terminal.

### Basic syntax

```bash
sudo tcpdump -A -i eth0
```

### Example

```bash
sudo tcpdump -A -i eth0 tcp
```

This captures TCP traffic and displays packet contents in ASCII where possible.

### Read from a PCAP file

If you already have a saved packet capture file, use:

```bash
sudo tcpdump -A -r sample.pcap
```

This reads the packets from `sample.pcap` and displays their contents in ASCII where possible.

### Example output

The following is illustrative output:

```text
GET /index.html HTTP/1.1
Host: example.com
User-Agent: curl/8.0
Accept: */*
```

### Understanding the output

| Output        | Meaning                               |
| ------------- | ------------------------------------- |
| `GET`         | HTTP request method                   |
| `/index.html` | Requested resource path               |
| `HTTP/1.1`    | HTTP protocol version                 |
| `Host`        | Requested website hostname            |
| `User-Agent`  | Information about the client software |
| `Accept`      | Content types the client can accept   |

This example shows readable HTTP request data.

**Important:** Modern websites commonly use HTTPS. Because HTTPS encrypts application data, ordinary packet captures generally do not show the HTTP request in readable plaintext.

## 5. Display Packet Contents Using Hexadecimal – `-x`

Sometimes packet contents contain data that cannot be displayed as readable text.

For this purpose, Tcpdump provides the `-x` option.

The `-x` option displays packet contents in hexadecimal format.

### What Is Hexadecimal?

Hexadecimal is a number system that uses 16 symbols:

```text
0 1 2 3 4 5 6 7 8 9 A B C D E F
```

It is commonly used in computing because one hexadecimal digit represents four bits.

Two hexadecimal digits represent one byte.

For example:

| Hexadecimal | Decimal | ASCII Character |
| ----------- | ------: | --------------- |
| `41`        |      65 | A               |
| `42`        |      66 | B               |
| `61`        |      97 | a               |
| `62`        |      98 | b               |
| `30`        |      48 | 0               |
| `31`        |      49 | 1               |

### Basic syntax

```bash
sudo tcpdump -x -i eth0
```

This captures packets and displays their contents in hexadecimal.

### Read a saved PCAP file

```bash
sudo tcpdump -x -r sample.pcap
```

This reads the saved packets and displays their contents in hexadecimal format.

### Example output

The following is an illustrative representation:

```text
0x0000:  4500 003c 1c46 4000 4006 0000 c0a8 010a
0x0010:  5db8 d822 c8fc 01bb 0000 0001 0000 0000
0x0020:  5002 faf0 0000 0000
```

### Understanding the output

- `0x0000` – Offset showing the position of the bytes.
- `4500` – Hexadecimal representation of packet bytes.
- `003c` – Additional packet bytes.
- `4006` – Contains information from the IP header.

The exact meaning of individual bytes depends on the packet format and protocol.

**Why is hexadecimal useful?**

It helps analysts inspect raw packet bytes, including values that are not printable as normal characters.

## 6. Display Packet Contents in Hexadecimal and ASCII – `-X`

The `-X` option displays packet contents in both hexadecimal and ASCII formats.

This is one of the most useful options when inspecting packet data.

It allows us to examine:

- Raw hexadecimal bytes.
- Readable ASCII characters.
- Text transmitted inside packets.
- Data that is not readable as normal text.

### Basic syntax

```bash
sudo tcpdump -X -i eth0
```

### Read a PCAP file

```bash
sudo tcpdump -X -r sample.pcap
```

This reads the saved packet capture and displays packet contents in hexadecimal and ASCII.

### Example output

The following is an illustrative representation:

```text
0x0000:  4745 5420 2f69 6e64 6578 2e68 746d 6c20
         GET /index.html

0x0010:  4854 5450 2f31 2e31 0d0a
         HTTP/1.1..
```

### Understanding the output

The hexadecimal bytes:

```text
4745 5420
```

Represent the ASCII characters:

```text
GET
```

Similarly:

```text
2f69 6e64 6578
```

Represents part of the text:

```text
/index
```

This makes it easier to compare raw bytes with readable data.

### Comparison of `-A`, `-x`, and `-X`

| Option | Description                                       |
| ------ | ------------------------------------------------- |
| `-A`   | Displays packet contents in ASCII                 |
| `-x`   | Displays packet contents in hexadecimal           |
| `-X`   | Displays packet contents in hexadecimal and ASCII |

### When should we use each option?

- Use `-A` when you want to inspect readable text.
- Use `-x` when you want to inspect raw hexadecimal bytes.
- Use `-X` when you want to examine both formats together.

## 7. Display Complete Packet Details Using Verbosity

Sometimes we need more than just packet payload information.

Tcpdump provides verbosity options to display additional protocol details.

### 7.1 Use `-v`

```bash
sudo tcpdump -v -nn -i eth0
```

The `-v` option displays additional information about packets.

### 7.2 Use `-vv`

```bash
sudo tcpdump -vv -nn -i eth0
```

The `-vv` option requests more detailed output.

### 7.3 Use `-vvv`

```bash
sudo tcpdump -vvv -nn -i eth0
```

The `-vvv` option requests the highest verbosity level.

### Combine verbosity with packet contents

You can also combine verbosity with the packet-content options.

For example:

```bash
sudo tcpdump -vv -X -nn -i eth0
```

This displays more packet details along with hexadecimal and ASCII packet contents.

**Important:** Verbosity and packet-content options provide different types of information. More verbosity does not guarantee that the entire application payload will be displayed.

## 8. Display a Specific Number of Bytes Using Snapshot Length

Tcpdump uses the `-s` option to control the snapshot length.

Snapshot length determines how many bytes of each packet are captured.

### Example

```bash
sudo tcpdump -s 96 -i eth0 -X
```

This captures up to 96 bytes of each packet and displays the captured bytes in hexadecimal and ASCII.

### What happens if the packet is larger?

Imagine that a packet contains 1500 bytes of data.

If the snapshot length is set to 96 bytes, only the first 96 bytes are captured.

The remaining bytes are not available in that capture.

This means that the packet payload may be incomplete.

### Capture the maximum supported packet size

```bash
sudo tcpdump -s 0 -i eth0 -X
```

On modern Tcpdump versions, `-s 0` requests the maximum snapshot length supported by the implementation.

This generally allows the full packet to be captured.

### Why is snapshot length important?

Imagine you are investigating an HTTP request.

If the capture contains only the beginning of the packet, you may see some headers but miss the remaining application data.

For complete packet-content analysis, choose a suitable snapshot length.

**Security note:** Full-packet captures may contain sensitive information. Store them securely and only inspect traffic you are authorized to access.

## 9. Inspect HTTP Traffic Using Tcpdump

HTTP stands for Hypertext Transfer Protocol.

It is an application-layer protocol used for communication between clients and web servers.

Traditional HTTP traffic is not encrypted by default.

This means that some HTTP request and response data may be visible in a packet capture.

### Example HTTP request

```text
GET /index.html HTTP/1.1
Host: example.com
User-Agent: curl/8.0
Accept: */*
```

### Capture HTTP traffic

To monitor traffic involving port 80:

```bash
sudo tcpdump -i eth0 -nn port 80
```

### Display HTTP packet contents

```bash
sudo tcpdump -i eth0 -A -nn port 80
```

This attempts to display readable ASCII content from packets involving port 80.

### Save HTTP traffic into a PCAP file

```bash
sudo tcpdump -i eth0 -nn port 80 -w http-traffic.pcap
```

### Read the saved HTTP capture

```bash
sudo tcpdump -A -nn -r http-traffic.pcap
```

### Generate a simple HTTP request

You can generate a request to a test website using:

```bash
curl -I http://example.com
```

This sends an HTTP HEAD request.

If the request travels over your selected capture interface and is not redirected, the packet capture may contain readable HTTP request information.

### Why is HTTP analysis useful?

Security analysts can use HTTP packet analysis to investigate:

- HTTP request methods.
- Requested resource paths.
- Host headers.
- User-Agent information.
- Server response information.
- Suspicious plaintext communication.

**Important:** Do not send passwords or sensitive information over unencrypted HTTP for testing. Use a harmless test request.

## 10. Understanding HTTPS and Encrypted Packet Contents

HTTPS stands for Hypertext Transfer Protocol Secure.

It uses TLS to protect communication between a client and a server.

Unlike ordinary HTTP, HTTPS encrypts application data.

This means that when we capture HTTPS traffic, we generally cannot read the actual HTTP request or response content directly.

### Example

Capture traffic involving port 443:

```bash
sudo tcpdump -i eth0 -nn port 443
```

Display packet contents:

```bash
sudo tcpdump -i eth0 -X -nn port 443
```

You may see hexadecimal data, but the encrypted application payload will not appear as normal readable HTTP text.

### What information can we still observe?

Even when application data is encrypted, a packet capture can still provide useful information, including:

- Source IP address.
- Destination IP address.
- Source port.
- Destination port.
- Packet size.
- Packet timing.
- TCP flags.
- Some TLS handshake information, depending on the traffic and encryption features.

### HTTP vs HTTPS

| Feature          | HTTP                                       | HTTPS                    |
| ---------------- | ------------------------------------------ | ------------------------ |
| Default port     | 80                                         | 443                      |
| Encryption       | Not provided by HTTP itself                | Uses TLS                 |
| Application data | Often readable in a packet capture         | Normally encrypted       |
| Security         | Does not protect data in transit by itself | Protects data in transit |

**Important:** HTTPS does not hide every network detail. IP addresses, ports, packet sizes, and timing information may still be visible.

## 11. Inspect DNS Packet Contents

DNS stands for Domain Name System.

It translates domain names into IP addresses and provides other naming information.

DNS commonly uses UDP port 53, although TCP port 53 is also used in certain situations.

### Capture DNS traffic

```bash
sudo tcpdump -i eth0 -nn port 53
```

### Display DNS packet contents

```bash
sudo tcpdump -i eth0 -X -nn port 53
```

This displays the packet bytes in hexadecimal and ASCII.

### Save DNS traffic

```bash
sudo tcpdump -i eth0 -nn port 53 -w dns-traffic.pcap
```

### Read DNS packet contents

```bash
sudo tcpdump -X -nn -r dns-traffic.pcap
```

### Generate DNS traffic

Open another terminal and run:

```bash
nslookup example.com
```

This generates DNS queries.

### Why is DNS packet analysis useful?

It can help analysts investigate:

- Which domain names a system is requesting.
- DNS query and response behavior.
- Repeated DNS requests.
- Unexpected domain lookups.
- Possible DNS tunneling indicators.

**Important:** Ordinary DNS traffic may be readable, but encrypted DNS protocols such as DNS over HTTPS and DNS over TLS protect DNS message contents.

## 12. Understand Packet Contents Using a Practical Example

Let's understand how ASCII and hexadecimal output relate to each other.

Suppose a packet contains this text:

```text
HELLO
```

Each character has an ASCII value.

| Character | Decimal | Hexadecimal |
| --------- | ------: | ----------- |
| H         |      72 | `48`        |
| E         |      69 | `45`        |
| L         |      76 | `4c`        |
| L         |      76 | `4c`        |
| O         |      79 | `4f`        |

The hexadecimal representation is:

```text
48 45 4c 4c 4f
```

The ASCII representation is:

```text
HELLO
```

This is the basic idea behind the `-X` option.

It allows us to examine the same packet bytes in two different formats.

**Remember:** Hexadecimal is not encryption. It is simply another way to represent data.

## 13. Save Packet Contents for Later Analysis

Sometimes we need to capture traffic now and analyze it later.

Tcpdump provides the `-w` option to save packets into a PCAP file.

### Step 1: Save network traffic

```bash
sudo tcpdump -i eth0 -nn -c 50 -w packet-content.pcap
```

This captures up to 50 packets and saves them into a file.

### Step 2: Read the saved file in ASCII

```bash
sudo tcpdump -A -nn -r packet-content.pcap
```

### Step 3: Read the saved file in hexadecimal

```bash
sudo tcpdump -x -nn -r packet-content.pcap
```

### Step 4: Read the saved file in both formats

```bash
sudo tcpdump -X -nn -r packet-content.pcap
```

### Step 5: Display additional details

```bash
sudo tcpdump -vv -X -nn -r packet-content.pcap
```

This is useful when you want to compare packet headers and packet contents during an investigation.

## 14. Practical Lab – Displaying Packet Contents

Now let's perform a practical lab using Tcpdump.

We will generate network traffic, capture it, save it into a PCAP file, and inspect the packet contents.

### Lab requirements

- Kali Linux or Ubuntu.
- Tcpdump installed.
- Terminal access.
- Internet connection.
- A network interface with traffic.

### Step 1: Create a lab directory

```bash
mkdir -p ~/tcpdump-lab/packet-contents
cd ~/tcpdump-lab/packet-contents
```

This creates a directory for the lab files.

### Step 2: Start capturing traffic

Run:

```bash
sudo tcpdump -i any -nn -c 100 -w packet-content-lab.pcap
```

This captures up to 100 packets and saves them into `packet-content-lab.pcap`.

### Step 3: Generate HTTP traffic

While Tcpdump is running, open another terminal and run:

```bash
curl -I http://example.com
```

This sends an HTTP HEAD request.

If the destination redirects the request or your network uses a proxy, the actual captured traffic may differ.

### Step 4: Generate DNS traffic

Run:

```bash
nslookup example.com
```

This generates DNS queries.

### Step 5: Wait for the capture to finish

Tcpdump will stop automatically after capturing 100 packets.

### Step 6: Read the saved packets

```bash
sudo tcpdump -nn -r packet-content-lab.pcap
```

Review the source IP, destination IP, protocol, and packet information.

### Step 7: Display packet contents in ASCII

```bash
sudo tcpdump -A -nn -r packet-content-lab.pcap
```

Look for readable text in the captured packets.

### Step 8: Display packet contents in hexadecimal

```bash
sudo tcpdump -x -nn -r packet-content-lab.pcap
```

Observe the hexadecimal representation of the captured packet bytes.

### Step 9: Display hexadecimal and ASCII together

```bash
sudo tcpdump -X -nn -r packet-content-lab.pcap
```

Compare the hexadecimal values with the readable characters.

### Step 10: Filter DNS traffic

```bash
sudo tcpdump -X -nn -r packet-content-lab.pcap port 53
```

This displays the contents of packets involving port 53.

### Step 11: Filter HTTP traffic

```bash
sudo tcpdump -A -nn -r packet-content-lab.pcap port 80
```

This attempts to display readable HTTP data from captured packets involving port 80.

### Step 12: Analyze HTTPS traffic

If HTTPS traffic was captured, inspect it using:

```bash
sudo tcpdump -X -nn -r packet-content-lab.pcap port 443
```

Observe the packet bytes and compare them with readable HTTP data.

Remember that HTTPS application data is encrypted.

**Lab result:** You have learned how to capture packet data and inspect it using ASCII, hexadecimal, and combined output formats.

## 15. Real-World Use Cases

Displaying packet contents is useful for different cybersecurity and networking tasks.

### 15.1 SOC Analyst – Investigating Suspicious HTTP Traffic

A SOC analyst receives an alert about suspicious HTTP communication.

The analyst examines packet contents to identify:

- HTTP request methods.
- Requested paths.
- Host headers.
- User-Agent information.
- Unexpected plaintext data.

This helps the analyst understand what kind of communication occurred.

### 15.2 Network Troubleshooting

A network administrator investigates an application that is not communicating correctly.

They inspect packet contents to understand:

- Whether requests are being sent.
- Whether responses are received.
- Whether protocol messages are malformed.
- Whether the application is sending unexpected data.

### 15.3 DNS Investigation

A security analyst investigates unusual DNS requests.

They inspect DNS packet contents to identify:

- Requested domain names.
- Query types.
- DNS response information.
- Repeated or unusual requests.

### 15.4 Digital Forensics

During an incident investigation, packet contents may help analysts understand what information was transmitted over a network.

For example, if an application sends data without encryption, the packet capture may contain readable application information.

Analysts must preserve the original capture and protect any sensitive information it contains.

### 15.5 Malware Traffic Analysis

A security researcher examines a PCAP file from a suspicious system.

Packet contents may help identify:

- Unexpected application requests.
- Suspicious domain lookups.
- Unusual communication patterns.
- Readable commands or responses in unencrypted protocols.

However, packet contents alone may not be enough to confirm malware activity.

## 16. Common Mistakes During Packet Content Analysis

| Mistake                                  | Explanation                                      | Solution                                            |
| ---------------------------------------- | ------------------------------------------------ | --------------------------------------------------- |
| Expecting all packet data to be readable | Some data is binary or encrypted                 | Use hexadecimal output and understand the protocol  |
| Expecting HTTPS content in plaintext     | HTTPS encrypts application data                  | Analyze visible metadata and TLS information        |
| Using `-A` for all packet types          | Binary data may not display meaningfully as text | Use `-X` for hexadecimal and ASCII                  |
| Capturing too few bytes                  | The packet payload may be incomplete             | Choose a suitable snapshot length                   |
| Confusing hexadecimal with encryption    | Hexadecimal is only a data representation        | Remember that encoding and encryption are different |
| Ignoring packet headers                  | Payload information needs context                | Inspect headers and payload together                |
| Sharing sensitive captures               | PCAP files may contain private information       | Store captures securely and sanitize before sharing |

## 17. Quick Command Reference

| Command                       | Purpose                                                              |
| ----------------------------- | -------------------------------------------------------------------- |
| `tcpdump -A -i eth0`          | Display packet contents in ASCII                                     |
| `tcpdump -x -i eth0`          | Display packet contents in hexadecimal                               |
| `tcpdump -X -i eth0`          | Display packet contents in hexadecimal and ASCII                     |
| `tcpdump -A -r sample.pcap`   | Read packet contents in ASCII                                        |
| `tcpdump -x -r sample.pcap`   | Read packet contents in hexadecimal                                  |
| `tcpdump -X -r sample.pcap`   | Read packet contents in hexadecimal and ASCII                        |
| `tcpdump -vv -X -i eth0`      | Display detailed packet information and contents                     |
| `tcpdump -s 96 -i eth0 -X`    | Capture up to 96 bytes per packet and display them                   |
| `tcpdump -s 0 -i eth0 -X`     | Capture using the maximum supported snapshot length                  |
| `tcpdump -A -i eth0 port 80`  | Display ASCII contents of traffic involving port 80                  |
| `tcpdump -X -i eth0 port 443` | Display hexadecimal and ASCII contents of traffic involving port 443 |
| `tcpdump -X -i eth0 port 53`  | Display DNS-related packet contents involving port 53                |

*Note: Add `-i eth0` for live capture or `-r sample.pcap` for saved-file analysis.*

## 18. Practice Questions

Try answering these questions after completing the module.

1. What is packet content analysis?
2. What is the difference between a packet header and a payload?
3. Why do we need to display packet contents?
4. What does the `-A` option do?
5. What does the `-x` option do?
6. What is the difference between `-x` and `-X`?
7. What is hexadecimal?
8. How many bits are represented by one hexadecimal digit?
9. Why is HTTP traffic often readable in packet captures?
10. Why is HTTPS application data normally not readable in plaintext?
11. What is snapshot length?
12. How can you inspect DNS packet contents using Tcpdump?
13. Why is `-X` useful during packet analysis?
14. How can packet contents help a SOC analyst investigate suspicious communication?
15. Why should PCAP files be handled carefully?

## 19. Practical Tasks

Complete these tasks using your own lab capture file.

- [ ] Create a PCAP file using Tcpdump.
- [ ] Read the PCAP file using `-r`.
- [ ] Display packet contents using `-A`.
- [ ] Display packet contents using `-x`.
- [ ] Display packet contents using `-X`.
- [ ] Compare ASCII and hexadecimal output.
- [ ] Capture and inspect HTTP traffic.
- [ ] Capture and inspect DNS traffic.
- [ ] Inspect HTTPS packet contents and identify what remains encrypted.
- [ ] Experiment with different snapshot lengths.
- [ ] Explain the difference between packet headers and payloads.
- [ ] Identify readable application data in an authorized capture.

## 20. Key Takeaways

In this module, we learned how to display and inspect packet contents using Tcpdump.

We covered:

- Understanding packet headers and payloads.
- Learning why packet-content analysis is important.
- Displaying packet contents using ASCII.
- Displaying packet contents using hexadecimal.
- Displaying hexadecimal and ASCII together.
- Understanding the difference between encoding and encryption.
- Using verbosity options to inspect packet details.
- Understanding snapshot length.
- Inspecting HTTP, HTTPS, and DNS traffic.
- Saving packet captures for later analysis.
- Applying packet-content analysis in SOC investigations and network troubleshooting.

**Remember:** Default Tcpdump output tells us about the communication, while packet-content analysis can help us understand the data being carried. However, encrypted traffic and incomplete captures may limit what we can see.

## 21. References and Further Learning

- [Tcpdump Official Website](https://www.tcpdump.org/)
- [Tcpdump Manual Page](https://www.tcpdump.org/manpages/tcpdump.1.html)
- [PCAP Filter Manual](https://www.tcpdump.org/manpages/pcap-filter.7.html)
- [Wireshark Documentation](https://www.wireshark.org/docs/)
- [RFC 9110 – HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110)
- [RFC 8446 – TLS 1.3](https://www.rfc-editor.org/rfc/rfc8446)
- [TryHackMe](https://tryhackme.com/)

---

**Next Module: 10 – Real-World Use Cases**

In the next module, we will combine the Tcpdump concepts we have learned and apply them to practical network monitoring, troubleshooting, and cybersecurity investigation scenarios.

**Documentation by Zero2Null Security**
