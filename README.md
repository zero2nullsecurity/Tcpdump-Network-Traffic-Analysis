# Tcpdump – Network Traffic Analysis

<p align="center">
  <img src="assets/tcpdump-logo.svg" alt="Tcpdump Logo" width="320">
</p>

<p align="center">
  <b>Learn Network Packet Capturing, Filtering & Traffic Analysis</b>
</p>


<p align="center">
  <img src="https://img.shields.io/badge/TOOL-TCPDUMP-blue?style=for-the-badge" alt="Tool">
  <img src="https://img.shields.io/badge/CATEGORY-NETWORK_ANALYSIS-green?style=for-the-badge" alt="Category">
  <img src="https://img.shields.io/badge/LEVEL-BEGINNER-orange?style=for-the-badge" alt="Level">
  <img src="https://img.shields.io/badge/PLATFORM-LINUX-lightgrey?style=for-the-badge" alt="Platform">
</p>


---

## 1. Introduction

Tcpdump is a command-line network packet analyzer used to capture, monitor, and analyze network traffic.

In simple words, Tcpdump allows us to see what is happening inside a network.

Whenever two devices communicate, they exchange network packets. These packets contain information such as:

- Source IP Address
- Destination IP Address
- Source and Destination Ports
- Network Protocol
- TCP Flags
- Packet Size
- Other protocol-related information

Normally, we use applications and websites without seeing these network communications.

Tcpdump helps us observe these communications directly.

**Example:**

When you open a website, your computer communicates with different servers. Tcpdump can help you observe DNS queries, TCP connections, and other network packets generated during this activity.

## 2. Why Do We Use Tcpdump?

Tcpdump is useful for understanding and troubleshooting network communication.

Some common use cases include:

- **Network Monitoring:** Observe packets moving through a network interface.
- **Troubleshooting:** Identify network communication problems.
- **Protocol Analysis:** Understand TCP, UDP, ICMP, DNS, ARP, and other protocols.
- **Security Analysis:** Investigate suspicious network traffic.
- **Packet Filtering:** Capture only the traffic you are interested in.
- **PCAP Analysis:** Save network traffic and analyze it later.
- **Incident Investigation:** Examine network activity during a security investigation.

## 3. How Does Tcpdump Work?

Tcpdump uses the `libpcap` packet-capture library to capture network traffic.

Basic working process:

1. A device sends or receives network packets.
2. The network interface receives or transmits the packets.
3. Tcpdump captures packets through the packet-capture mechanism.
4. Tcpdump displays packet information in the terminal.
5. Captured packets can also be saved into a PCAP file.
6. The saved file can be analyzed later using Tcpdump or Wireshark.

**Important:** Tcpdump captures traffic visible to the selected network interface. It does not automatically capture every packet across the entire network.

## 4. Learning Objectives

After completing this module, you should understand:

- What Tcpdump is and how it works.
- How to identify network interfaces.
- How to capture live network packets.
- How to save captured packets into PCAP files.
- How to read previously captured PCAP files.
- How to filter packets by IP address, port, and protocol.
- How to use logical operators in filters.
- How to analyze TCP flags.
- How to inspect packet contents.
- How to investigate ARP, DNS, ICMP, TCP, and UDP traffic.

## 5. Installation & Local Setup

We will install Tcpdump and perform practical exercises on our own machine.

### Supported environments

| Platform | Setup |
|---|---|
| Kali Linux | Native installation |
| Ubuntu | Native installation |
| Debian | Native installation |
| Windows | Linux VM or Windows packet-capture environment |
| TryHackMe | Browser-based lab machine |

### Linux Installation

Update your package repository:

```bash
sudo apt update
```

Install Tcpdump:

```bash
sudo apt install tcpdump -y
```

Check the installed version:

```bash
tcpdump --version
```

Check available network interfaces:

```bash
ip a
```

**Note:** Packet capturing on network interfaces generally requires root privileges or appropriate capture permissions.

## 6. Practical Learning Modules

We will cover the following topics step by step:

| Module | Topic |
|---|---|
| 01 | Introduction to Tcpdump |
| 02 | Installation and Setup |
| 03 | Basic Packet Capture |
| 04 | Packet Filtering |
| 05 | Logical Operators |
| 06 | Reading and Analyzing PCAP Files |
| 07 | Advanced Filtering |
| 08 | TCP Flags Analysis |
| 09 | Displaying Packet Contents |
| 10 | DNS, ARP and ICMP Analysis |
| 11 | Real-World Network Troubleshooting |
| 12 | Practical Labs and Challenges |

## 7. Practical Exercises

We will perform these exercises locally:

- Capture packets from a network interface.
- Capture a limited number of packets.
- Identify source and destination IP addresses.
- Monitor DNS traffic using port 53.
- Capture HTTP and HTTPS traffic.
- Filter TCP, UDP, and ICMP packets.
- Identify TCP SYN and RST flags.
- Capture ARP requests and replies.
- Save captured traffic into PCAP files.
- Read and analyze existing PCAP files.
- Investigate unusual network communication.

All exercises should be performed on your own machine, authorized labs, or networks where you have permission.

## 8. TryHackMe Practice

**Learning Resource:** [TryHackMe – Tcpdump](https://tryhackme.com/)

We will use the TryHackMe learning material as our primary learning reference and extend it with additional local practical exercises.

The documentation will include:

- Topic explanations.
- Important commands.
- Command output analysis.
- Lab exercises.
- TryHackMe question explanations.
- Additional practice challenges.

## 9. Additional Learning Resources

- [Official Tcpdump Website](https://www.tcpdump.org/)
- [Tcpdump Manual](https://www.tcpdump.org/manpages/tcpdump.1.html)
- [PCAP Filter Manual](https://www.tcpdump.org/manpages/pcap-filter.7.html)
- [Wireshark Official Website](https://www.wireshark.org/)

## 10. Repository Structure

```text
Tcpdump/
│
├── README.md
│
├── 01-Introduction-to-Tcpdump.md
├── 02-Installation-and-Setup.md
├── 03-Basic-Packet-Capture.md
├── 04-Packet-Filtering.md
├── 05-Logical-Operators.md
├── 06-PCAP-File-Analysis.md
├── 07-Advanced-Filtering.md
├── 08-TCP-Flags-Analysis.md
├── 09-Displaying-Packet-Contents.md
├── 10-Real-World-Use-Cases.md
├── 11-Practical-Labs.md
├── 12-Troubleshooting.md
│
├── screenshots/
│
└── pcaps/
```

## 11. Learning Approach

Our learning approach will follow this sequence:

**Understand → Install → Execute → Observe → Analyze → Practice → Document**

We will not just memorize commands. We will understand what each command does, why we use it, and how its output helps us analyze network traffic.

## 12. Conclusion

Tcpdump is an important tool for anyone learning networking, cybersecurity, SOC operations, or penetration testing.

It helps us understand network communication by allowing us to capture and inspect packets directly.

By combining TryHackMe exercises with local practical work, we can develop real-world packet analysis skills.

---

**Author:** Zero2Null Security

**Focus:** Networking | Cybersecurity | Network Traffic Analysis | Practical Learning

**Disclaimer:** This repository is created for educational purposes. Perform packet capture and network analysis only on systems and networks where you have proper authorization.
