# 06 – PCAP File Analysis

## 1. Introduction

In the previous modules, we learned how to capture network traffic using Tcpdump and save captured packets into a file.

Now, we will learn how to analyze those saved packet capture files.

A PCAP file stores network packet information captured from a network interface. Instead of monitoring live network traffic, we can open an existing PCAP file and examine the packets whenever we need them.

This is especially useful in cybersecurity because security analysts often receive packet capture files while investigating suspicious network activity, malware communication, unauthorized connections, and other network-related incidents.

For example:

- A SOC analyst receives a PCAP file from a security monitoring system.
- A network administrator investigates why a connection is failing.
- A security researcher examines suspicious communication between two systems.
- A penetration tester reviews network traffic generated during an authorized security assessment.

Tcpdump allows us to read, filter, and inspect these packets directly from the terminal.

## 2. What Is a PCAP File?

PCAP stands for **Packet Capture**.

A PCAP file contains information about network packets captured from a network interface.

Depending on the capture settings and available packet data, it may contain information such as:

- Source IP address
- Destination IP address
- Source port
- Destination port
- Protocol information
- Packet timestamps
- TCP flags
- Packet length
- Packet payload, if captured

A PCAP file does not automatically contain every detail of a network communication. The available information depends on what was captured, the capture settings, and whether the traffic was encrypted.

### Common PCAP file extensions

| Extension | Description |
|---|---|
| `.pcap` | Common packet capture file format |
| `.pcapng` | PCAP Next Generation format |
| `.cap` | Another extension sometimes used for capture files |

Tcpdump commonly works with traditional PCAP files. Support for PCAPNG depends on the Tcpdump and libpcap versions and their build configuration.

## 3. PCAP vs PCAPNG

Both PCAP and PCAPNG are used to store captured network traffic, but they have different capabilities.

| Feature | PCAP | PCAPNG |
|---|---|---|
| Full name | Packet Capture | PCAP Next Generation |
| Structure | Simpler format | More flexible format |
| Interface information | Limited | Can store information about multiple interfaces |
| Metadata | Basic | Supports additional metadata |
| Compatibility | Widely supported | Supported by many modern tools |

### Why does this matter?

When working in cybersecurity, you may receive capture files in either format.

Before analyzing a file, identify its format and confirm that your tools can read it.

## 4. Prepare a PCAP File for Analysis

Before analyzing a capture file, we need a PCAP file to work with.

You can use a PCAP file that you previously created in the Basic Packet Capture module.

### Step 1: Create a working directory

```bash
mkdir -p ~/tcpdump-lab/pcaps
```

**Command breakdown:**

- `mkdir` – Creates a directory.
- `-p` – Creates parent directories if they do not exist.
- `~/tcpdump-lab/pcaps` – Directory where we will store capture files.

### Step 2: Create a sample PCAP file

If you do not already have a capture file, you can create one using your own system.

Open a terminal and run:

```bash
sudo tcpdump -i any -nn -c 20 -w ~/tcpdump-lab/pcaps/sample.pcap
```

Now, while Tcpdump is capturing, open another terminal and generate some network traffic:

```bash
ping -c 4 1.1.1.1
```

After Tcpdump captures 20 packets, it will stop automatically.

**Command breakdown:**

| Option | Meaning |
|---|---|
| `sudo` | Runs Tcpdump with the required permissions |
| `-i any` | Captures traffic from available interfaces supported by the system's `any` pseudo-interface |
| `-nn` | Prevents hostname and service-name resolution |
| `-c 20` | Stops after capturing 20 packets |
| `-w` | Writes captured packets into a file |
| `sample.pcap` | Name of the output file |

**Note:** The `any` interface is commonly available on Linux. If it is not available on your system, use an interface name displayed by `tcpdump -D`.

The file is now ready for analysis.

### Step 3: Verify the file

```bash
ls -lh ~/tcpdump-lab/pcaps/
```

This command displays the files in the directory along with their sizes.

You can also check the file type:

```bash
file ~/tcpdump-lab/pcaps/sample.pcap
```

The `file` command examines the file and identifies its type based on its contents.

## 5. Read a PCAP File Using Tcpdump

The `-r` option is used to read packets from an existing capture file.

### Basic syntax

```bash
sudo tcpdump -r filename.pcap
```

### Example

```bash
sudo tcpdump -r ~/tcpdump-lab/pcaps/sample.pcap
```

This command reads the packets stored inside `sample.pcap` and displays them in the terminal.

**Command breakdown:**

- `sudo` – Provides the required permissions if needed.
- `tcpdump` – Starts the packet analysis tool.
- `-r` – Reads packets from an existing capture file.
- `sample.pcap` – The file we want to analyze.

### Example output

The following is illustrative output. Your actual output will depend on the packets captured.

```text
reading from file sample.pcap, link-type LINUX_SLL2
12:30:01.123456 IP 192.168.1.10 > 1.1.1.1: ICMP echo request
12:30:01.145678 IP 1.1.1.1 > 192.168.1.10: ICMP echo reply
12:30:02.123456 IP 192.168.1.10 > 1.1.1.1: ICMP echo request
12:30:02.145678 IP 1.1.1.1 > 192.168.1.10: ICMP echo reply
```

### Understanding the output

| Output | Meaning |
|---|---|
| `12:30:01.123456` | Packet timestamp |
| `IP` | Internet Protocol |
| `192.168.1.10` | Source IP address |
| `>` | Direction of packet |
| `1.1.1.1` | Destination IP address |
| `ICMP` | Protocol used |
| `echo request` | ICMP request message |
| `echo reply` | ICMP response message |

This output helps us understand which systems communicated and which protocols were used.

## 6. Read Packets Without DNS and Port Name Resolution

By default, Tcpdump may try to resolve IP addresses into hostnames and port numbers into service names.

This can make output slower or less predictable during analysis.

We can use `-nn` to disable both types of resolution.

### Command

```bash
sudo tcpdump -nn -r ~/tcpdump-lab/pcaps/sample.pcap
```

### What does `-nn` do?

- First `n` – Prevents hostname resolution.
- Second `n` – Prevents port-number-to-service-name conversion.

For example, instead of displaying a service name, Tcpdump will show the actual port number.

**Why is this useful?**

During security investigations, exact IP addresses and port numbers are important. Numeric output makes it easier to identify suspicious connections and compare packet information.

## 7. Display More Packet Details

Sometimes the default output does not provide enough information.

Tcpdump provides verbosity options to display additional packet details.

### 7.1 Basic verbosity: `-v`

```bash
sudo tcpdump -v -nn -r ~/tcpdump-lab/pcaps/sample.pcap
```

The `-v` option displays more information about captured packets.

### 7.2 Higher verbosity: `-vv`

```bash
sudo tcpdump -vv -nn -r ~/tcpdump-lab/pcaps/sample.pcap
```

The `-vv` option displays more detailed packet information than `-v`.

### 7.3 Maximum verbosity: `-vvv`

```bash
sudo tcpdump -vvv -nn -r ~/tcpdump-lab/pcaps/sample.pcap
```

The `-vvv` option requests an even higher level of detail.

### Comparison

| Option | Description |
|---|---|
| `-v` | More packet details |
| `-vv` | Higher level of detail |
| `-vvv` | Maximum verbosity level |

**Important:** More verbosity does not guarantee that every packet field or payload will be displayed. The information available depends on the protocol, packet contents, and capture settings.

## 8. Display Readable Timestamps

Packet timestamps are important when investigating network events.

They help us understand when a packet was transmitted and how events happened over time.

Use the `-tttt` option to display human-readable date and time information.

### Command

```bash
sudo tcpdump -tttt -nn -r ~/tcpdump-lab/pcaps/sample.pcap
```

### Example output

```text
2026-11-27 12:30:01.123456 IP 192.168.1.10 > 1.1.1.1: ICMP echo request
2026-11-27 12:30:01.145678 IP 1.1.1.1 > 192.168.1.10: ICMP echo reply
```

The date and time shown above are examples, not actual capture results.

### Why are timestamps important?

Imagine that a security monitoring system reports suspicious traffic at 12:30 PM.

An analyst can inspect the PCAP file and compare packet timestamps with the reported incident time.

This helps identify:

- When suspicious communication started.
- Whether multiple systems communicated at the same time.
- The order in which network events occurred.
- Whether a connection happened before or after another security event.

**Note:** Timestamps depend on the clock and timestamp settings of the system that captured the traffic.

## 9. Filter Packets Inside a PCAP File

One of the most useful features of Tcpdump is the ability to apply filters while reading an existing PCAP file.

We do not need to inspect every packet manually. We can select only the packets that match our investigation requirements.

The filter syntax is the same packet-filtering syntax we learned in previous modules.

### 9.1 Filter by IP address

Display packets involving a specific IP address:

```bash
sudo tcpdump -nn -r sample.pcap host 192.168.1.10
```

This displays packets where `192.168.1.10` is either the source or destination.

### 9.2 Filter by source IP

```bash
sudo tcpdump -nn -r sample.pcap src host 192.168.1.10
```

This displays packets sent from the specified IP address.

### 9.3 Filter by destination IP

```bash
sudo tcpdump -nn -r sample.pcap dst host 192.168.1.10
```

This displays packets sent to the specified IP address.

### 9.4 Filter by port number

Display packets involving port 443:

```bash
sudo tcpdump -nn -r sample.pcap port 443
```

Port 443 is commonly used for HTTPS traffic.

### 9.5 Filter TCP traffic

```bash
sudo tcpdump -nn -r sample.pcap tcp
```

This displays only TCP packets.

### 9.6 Filter UDP traffic

```bash
sudo tcpdump -nn -r sample.pcap udp
```

This displays only UDP packets.

### 9.7 Filter ICMP traffic

```bash
sudo tcpdump -nn -r sample.pcap icmp
```

This displays ICMP packets, such as ping requests and replies.

### 9.8 Combine multiple filters

We can combine filters using logical operators.

For example, display TCP traffic involving port 443:

```bash
sudo tcpdump -nn -r sample.pcap 'tcp and port 443'
```

Display traffic involving either port 80 or port 443:

```bash
sudo tcpdump -nn -r sample.pcap 'port 80 or port 443'
```

Display all packets except ICMP:

```bash
sudo tcpdump -nn -r sample.pcap 'not icmp'
```

**Important:** Use quotation marks around complex filters so that the shell does not interpret operators or parentheses before Tcpdump receives them.

## 10. Read Only a Specific Number of Packets

Sometimes a PCAP file contains thousands or millions of packets.

We may not need to inspect the entire file.

The `-c` option limits how many matching packets Tcpdump displays.

### Example

```bash
sudo tcpdump -nn -c 10 -r sample.pcap
```

This reads and displays up to 10 packets from the file.

### Filter with a packet limit

```bash
sudo tcpdump -nn -c 5 -r sample.pcap icmp
```

This displays up to five packets that match the ICMP filter.

**Remember:** The packet limit applies to matching packets, not necessarily the first five packets in the original file.

## 11. Display Packet Contents

Tcpdump can display packet contents in different formats.

This is useful when we need to inspect captured data rather than only source and destination information.

### 11.1 Display packet contents in ASCII

```bash
sudo tcpdump -A -nn -r sample.pcap
```

The `-A` option displays packet payload data in ASCII where possible.

This can help identify readable text in protocols such as HTTP.

### 11.2 Display packet contents in hexadecimal

```bash
sudo tcpdump -X -nn -r sample.pcap
```

The `-X` option displays packet contents in hexadecimal and ASCII.

### 11.3 Display packet contents in hexadecimal only

```bash
sudo tcpdump -x -nn -r sample.pcap
```

The `-x` option displays packet contents in hexadecimal.

### Comparison

| Option | Output |
|---|---|
| `-A` | ASCII packet contents |
| `-x` | Hexadecimal packet contents |
| `-X` | Hexadecimal and ASCII packet contents |

**Security note:** Packet contents can contain sensitive information, including credentials, session data, or personal information. Only inspect and share packet captures when you are authorized to do so.

Encrypted protocols such as HTTPS generally do not expose their application data as readable plaintext in an ordinary packet capture.

## 12. Understand Snapshot Length

Snapshot length controls how many bytes of each packet are captured and stored.

Tcpdump uses the `-s` option to set the snapshot length.

### Example

```bash
sudo tcpdump -s 96 -i eth0 -w sample.pcap
```

This captures up to 96 bytes from each packet.

If the original packet is larger than the snapshot length, the remaining bytes are not stored in the capture file.

### Capture the full packet

```bash
sudo tcpdump -s 0 -i eth0 -w sample-full.pcap
```

On modern Tcpdump versions, `-s 0` requests the maximum snapshot length supported by the implementation, generally allowing the full packet to be captured.

### Why does snapshot length matter?

Imagine that you capture only the first 96 bytes of a packet.

You may still see some header information, but the application payload may be incomplete.

This can create problems during forensic analysis because the missing bytes cannot be recovered from that capture file.

**Best practice:** When performing authorized investigations, choose a snapshot length that captures the data required for the investigation. Be aware that full-packet captures can contain sensitive data and require secure storage.

## 13. Analyze a PCAP File Using Other Linux Commands

Tcpdump is not the only command that can help us inspect a capture file.

Linux provides useful commands for checking file size, identifying file types, and verifying file integrity.

### 13.1 Check the file size

```bash
ls -lh sample.pcap
```

This displays the file size in a human-readable format.

### 13.2 Identify the file type

```bash
file sample.pcap
```

This helps identify whether the file contains packet capture data.

### 13.3 Calculate the SHA-256 hash

```bash
sha256sum sample.pcap
```

This generates a SHA-256 hash of the file.

A hash can help verify whether the file has changed between two points in an investigation.

For example, calculate the hash before transferring the file and calculate it again after transfer. If both hashes match, the file contents are consistent with the original.

### 13.4 Count displayed packets

For a basic packet count, you can use:

```bash
tcpdump -nn -r sample.pcap 2>/dev/null | wc -l
```

This sends Tcpdump's packet output to `wc -l`, which counts the output lines.

For normal one-line-per-packet output, this gives a useful packet count.

**Important:** This method counts output lines, not a guaranteed packet total for every possible verbose or multiline output format. Avoid using verbose output with this counting method.

### 13.5 Inspect PCAP metadata with capinfos

`capinfos` is a command-line utility included with Wireshark.

It can display useful information about a capture file, such as:

- File format
- Capture file size
- Packet count
- Capture time range
- Packet data information

Check whether it is installed:

```bash
capinfos --version
```

If the command is unavailable on Kali or Debian-based systems, install Wireshark's command-line utilities:

```bash
sudo apt update
sudo apt install wireshark-common
```

Then inspect the file:

```bash
capinfos sample.pcap
```

The exact metadata shown depends on the capture file and installed version.

## 14. Practical Lab – Analyze a PCAP File

Now let's perform a practical exercise using the sample capture file.

We will create a capture, inspect it, apply filters, and examine packet contents.

### Lab requirements

- Kali Linux or Ubuntu
- Tcpdump installed
- Terminal access
- Internet connection for the optional ping test

### Step 1: Create a lab directory

```bash
mkdir -p ~/tcpdump-lab/pcaps
cd ~/tcpdump-lab/pcaps
```

### Step 2: Capture network traffic

Run:

```bash
sudo tcpdump -i any -nn -c 30 -w lab-traffic.pcap
```

While Tcpdump is running, open another terminal and generate traffic:

```bash
ping -c 5 1.1.1.1
```

Wait for the capture to finish.

### Step 3: Check the capture file

```bash
ls -lh lab-traffic.pcap
```

Check the file type:

```bash
file lab-traffic.pcap
```

### Step 4: Read the captured packets

```bash
sudo tcpdump -nn -r lab-traffic.pcap
```

Observe the source IP, destination IP, protocol, and packet information.

### Step 5: Display readable timestamps

```bash
sudo tcpdump -tttt -nn -r lab-traffic.pcap
```

Observe the date and time of each packet.

### Step 6: Filter ICMP traffic

```bash
sudo tcpdump -nn -r lab-traffic.pcap icmp
```

Observe the ICMP request and reply packets generated by the ping command.

### Step 7: Display packet details

```bash
sudo tcpdump -vv -nn -r lab-traffic.pcap icmp
```

Compare this output with the previous command.

### Step 8: Display packet contents

```bash
sudo tcpdump -X -nn -r lab-traffic.pcap icmp
```

Observe the hexadecimal and ASCII representation where available.

### Step 9: Calculate the file hash

```bash
sha256sum lab-traffic.pcap
```

Save the hash for future comparison.

### Step 10: Inspect file metadata

If `capinfos` is installed, run:

```bash
capinfos lab-traffic.pcap
```

Review the packet count, file size, and capture time information.

**Lab result:** You have created a PCAP file and analyzed it using Tcpdump and Linux utilities.

## 15. Real-World Use Cases

PCAP file analysis is an important skill in network troubleshooting, security monitoring, and digital forensics.

Here are some examples of how security professionals use it.

### 15.1 SOC Alert Investigation

A SOC analyst receives an alert about suspicious communication from an internal system.

The analyst receives a PCAP file and checks:

- Source and destination IP addresses.
- Communication timestamps.
- Protocols and destination ports.
- Repeated or unusual connections.

This helps the analyst understand the network activity and decide whether further investigation is required.

### 15.2 Malware Traffic Analysis

A security researcher investigates a suspicious system that may be communicating with an external server.

The researcher can examine the PCAP file to identify:

- Unexpected outbound connections.
- Repeated communication attempts.
- Unusual destination ports.
- DNS requests to suspicious domains.
- Communication patterns that may indicate command-and-control activity.

A PCAP file alone does not prove that a system is infected. Additional endpoint and security evidence is needed.

### 15.3 Network Troubleshooting

A network administrator receives a report that an application is not connecting properly.

The administrator can inspect a PCAP file to investigate:

- TCP connection attempts.
- Retransmissions.
- Connection resets.
- DNS request failures.
- Unusual communication delays.

This helps narrow down the possible cause of the issue.

### 15.4 Digital Forensics

During a security incident, investigators may preserve packet captures as evidence.

They can use timestamps, packet information, and file hashes to help maintain the integrity and timeline of their investigation.

## 16. Common Mistakes During PCAP Analysis

| Mistake | Explanation | Solution |
|---|---|---|
| Using the wrong file path | Tcpdump cannot find the capture file | Check the path with `ls` |
| Forgetting `-r` | The file is not opened for reading | Use `tcpdump -r filename.pcap` |
| Using hostname resolution | Output may be slower or less clear | Use `-nn` |
| Capturing too few bytes | Some packet contents may be missing | Choose a suitable snapshot length |
| Applying an incorrect filter | Relevant packets may not appear | Check the filter syntax |
| Ignoring timestamps | The event timeline may be misunderstood | Use `-tttt` |
| Sharing sensitive PCAP files | Captures may contain private data | Protect and sanitize files before sharing |

## 17. Quick Command Reference

| Command | Purpose |
|---|---|
| `tcpdump -r sample.pcap` | Read a PCAP file |
| `tcpdump -nn -r sample.pcap` | Read without name resolution |
| `tcpdump -v -r sample.pcap` | Display more packet details |
| `tcpdump -vv -r sample.pcap` | Display higher verbosity |
| `tcpdump -vvv -r sample.pcap` | Display maximum verbosity |
| `tcpdump -tttt -r sample.pcap` | Display readable timestamps |
| `tcpdump -c 10 -r sample.pcap` | Display up to 10 matching packets |
| `tcpdump -r sample.pcap icmp` | Filter ICMP packets |
| `tcpdump -r sample.pcap tcp` | Filter TCP packets |
| `tcpdump -r sample.pcap udp` | Filter UDP packets |
| `tcpdump -r sample.pcap port 443` | Filter traffic involving port 443 |
| `tcpdump -A -r sample.pcap` | Display ASCII packet contents |
| `tcpdump -x -r sample.pcap` | Display hexadecimal packet contents |
| `tcpdump -X -r sample.pcap` | Display hexadecimal and ASCII contents |
| `tcpdump -s 0 -i eth0 -w full.pcap` | Capture packets with the maximum supported snapshot length |
| `capinfos sample.pcap` | Inspect capture file metadata |
| `sha256sum sample.pcap` | Calculate the file's SHA-256 hash |

## 18. Practice Questions

Try answering these questions after completing the module.

1. What is a PCAP file?
2. What is the difference between PCAP and PCAPNG?
3. Which Tcpdump option is used to read an existing PCAP file?
4. Why do we use the `-nn` option?
5. What is the difference between `-v`, `-vv`, and `-vvv`?
6. Which option displays readable date and time information?
7. How can you filter ICMP packets from a saved PCAP file?
8. How can you display packets involving port 443?
9. What is the purpose of the `-A` option?
10. What is snapshot length?
11. Why is SHA-256 useful when handling capture files?
12. How can PCAP analysis help a SOC analyst investigate suspicious network activity?

## 19. Practical Tasks

Complete these tasks using your own lab capture file.

- [ ] Create a PCAP file using Tcpdump.
- [ ] Check its file size using `ls -lh`.
- [ ] Identify the file type using `file`.
- [ ] Read the PCAP file using `-r`.
- [ ] Display packets using `-nn`.
- [ ] Display additional details using `-vv`.
- [ ] Display readable timestamps using `-tttt`.
- [ ] Filter ICMP packets.
- [ ] Filter packets involving a specific IP address.
- [ ] Display packet contents using `-X`.
- [ ] Calculate the SHA-256 hash.
- [ ] Inspect capture metadata using `capinfos`, if available.

## 20. Screenshots to Add to GitHub

Capture your own terminal screenshots while performing the practical lab.

Recommended screenshots:

1. **PCAP File Creation** – Tcpdump capturing traffic and saving it into a file.
2. **File Verification** – `ls -lh` and `file` commands.
3. **Reading PCAP File** – Output of `tcpdump -r`.
4. **Packet Details** – Output of `tcpdump -vv`.
5. **Timestamp Analysis** – Output of `tcpdump -tttt`.
6. **Packet Filtering** – ICMP or IP-based filtering.
7. **Packet Contents** – Output of `tcpdump -X`.
8. **Hash Verification** – Output of `sha256sum`.
9. **PCAP Metadata** – Output of `capinfos`, if available.

Save the screenshots in:

```text
screenshots/
```

Use clear filenames, for example:

```text
06-pcap-file-creation.png
06-pcap-file-reading.png
06-pcap-timestamp-analysis.png
06-pcap-packet-filtering.png
06-pcap-packet-contents.png
06-pcap-hash-verification.png
```

## 21. Key Takeaways

In this module, we learned how to analyze previously captured network traffic using Tcpdump.

We covered:

- Understanding PCAP and PCAPNG files.
- Creating and verifying a PCAP file.
- Reading saved packet captures using `-r`.
- Displaying numeric IP addresses and port numbers using `-nn`.
- Using verbosity options to inspect packet details.
- Displaying readable timestamps.
- Applying packet filters to saved capture files.
- Inspecting packet contents in ASCII and hexadecimal.
- Understanding snapshot length.
- Checking file metadata and calculating SHA-256 hashes.
- Applying PCAP analysis techniques in SOC investigations and network troubleshooting.

**Remember:** Capturing network traffic is only the first step. The real value comes from analyzing the captured packets, understanding communication patterns, and identifying unusual activity.

## 22. References and Further Learning

- [Tcpdump Official Website](https://www.tcpdump.org/)
- [Tcpdump Manual Page](https://www.tcpdump.org/manpages/tcpdump.1.html)
- [Wireshark Documentation](https://www.wireshark.org/docs/)
- [Wireshark Capinfos Documentation](https://www.wireshark.org/docs/man-pages/capinfos.html)
- [TryHackMe](https://tryhackme.com/)

---

**Next Module: 07 – Advanced Filtering**

In the next module, we will explore advanced packet filtering techniques to identify specific network traffic and investigate suspicious communication more effectively.

**Documentation by Zero2Null Security**
