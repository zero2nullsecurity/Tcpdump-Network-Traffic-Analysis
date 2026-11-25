# 03 – Basic Packet Capture with Tcpdump

## 1. Introduction

In the previous module, we installed Tcpdump on our system.

Now it's time to use Tcpdump for actual network traffic analysis.

The first step in network traffic analysis is **Packet Capture**.

Packet capture means collecting network packets that are sent or received through a network interface.

Tcpdump allows us to capture these packets and display their information directly in the terminal.

In this module, we will learn how to:

- Identify available network interfaces.
- Capture live network packets.
- Select a specific network interface.
- Capture a limited number of packets.
- Display IP addresses in numeric format.
- Save captured packets into a PCAP file.
- Read previously saved PCAP files.

**Practical Environment:** Kali Linux / Ubuntu

---

## 2. Understanding Network Interfaces

Before capturing packets, we need to understand network interfaces.

A network interface is a connection point that allows a computer to communicate with a network.

A computer can have multiple network interfaces depending on its hardware and configuration.

Some common examples are:

| Interface | Description |
|---|---|
| eth0 | Ethernet network interface |
| wlan0 | Wireless network interface |
| lo | Loopback interface |
| ens33 | Common Linux Ethernet interface name |
| enp0s3 | Common Linux network interface name |

**Important:** Interface names can be different on different systems. Do not assume that every Linux machine uses `eth0` or `wlan0`.

### 2.1 Identify Available Network Interfaces

Open your terminal and execute:

```bash
ip a
```

This command displays network interfaces and their IP configuration.

Example output:

```text
1: lo: <LOOPBACK,UP,LOWER_UP>
    inet 127.0.0.1/8

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>
    inet 192.168.1.10/24
```

In this example:

- `lo` is the loopback interface.
- `eth0` is a network interface.
- `192.168.1.10` is the IPv4 address assigned to eth0.
- `UP` indicates that the interface is administratively enabled.

Your actual output may look different.

### 2.2 Another Way to List Interfaces

You can also use:

```bash
ip link show
```

This command displays network interface information.

### 2.3 List Interfaces Using Tcpdump

Tcpdump also provides an option to list available capture interfaces:

```bash
sudo tcpdump -D
```

Example output:

```text
1.eth0
2.lo
3.any
```

The actual interfaces depend on your system.

**Remember:** Always identify your correct network interface before starting packet capture.

---

## 3. Basic Tcpdump Command Syntax

Before running different capture commands, understand the basic syntax.

```bash
sudo tcpdump [options] -i INTERFACE
```

Let's understand each part:

| Component | Explanation |
|---|---|
| sudo | Runs Tcpdump with elevated privileges |
| tcpdump | Starts the packet analyzer |
| Options | Additional settings for packet capture |
| -i | Specifies the network interface |
| INTERFACE | Name of the selected network interface |

For example:

```bash
sudo tcpdump -i eth0
```

This command starts capturing packets visible on the `eth0` interface.

**Note:** Replace `eth0` with the actual interface name available on your machine.

---

## 4. Capture Live Network Packets

Now let's perform our first practical exercise.

### 4.1 Start Packet Capture

Execute:

```bash
sudo tcpdump -i eth0
```

This command starts capturing packets from the selected interface.

Example output:

```text
listening on eth0, link-type EN10MB
IP 192.168.1.10.52000 > 93.184.215.14.443:
Flags [S], seq 123456, win 64240, length 0
```

Let's understand the output:

| Output | Meaning |
|---|---|
| IP | IPv4 packet |
| 192.168.1.10 | Source IP address |
| 52000 | Source port |
| 93.184.215.14 | Destination IP address |
| 443 | Destination port |
| Flags [S] | TCP SYN flag |
| seq | TCP sequence number |
| win | TCP window size |
| length | TCP payload length |

**Understanding the example:**

The system at `192.168.1.10` is sending a TCP SYN packet to `93.184.215.14` on destination port `443`.

This can represent an attempt to establish a TCP connection.

The example is illustrative; your actual packet output will depend on your network activity.

### 4.2 Generate Network Traffic

While Tcpdump is running, open another terminal and generate some network activity.

For example:

```bash
ping -c 4 1.1.1.1
```

This sends four ICMP Echo Request packets to `1.1.1.1`.

Return to the Tcpdump terminal and observe the captured packets.

You may see ICMP Echo Requests and Echo Replies if the traffic is visible on the selected interface.

### 4.3 Stop Packet Capture

To stop Tcpdump, press:

```text
CTRL + C
```

Tcpdump will stop capturing and display a summary.

Example:

```text
5 packets captured
5 packets received by filter
0 packets dropped by kernel
```

Let's understand these lines:

- **Packets captured:** Number of packets captured by Tcpdump.
- **Packets received by filter:** Number of packets reported as received by the capture mechanism.
- **Packets dropped by kernel:** Number of packets dropped by the capture mechanism because they could not be processed.

The exact numbers depend on your system and traffic.

---

## 5. Capture Packets from a Specific Interface

A system can have multiple network interfaces.

Sometimes we only want to capture traffic from one specific interface.

For this purpose, Tcpdump provides the `-i` option.

### Command

```bash
sudo tcpdump -i eth0
```

Here:

- `-i` selects the interface.
- `eth0` is the selected interface.

### Capture from Wi-Fi Interface

If your wireless interface is named `wlan0`, you can use:

```bash
sudo tcpdump -i wlan0
```

### Capture from All Interfaces

On supported Linux systems, Tcpdump provides the special interface selection `any`.

```bash
sudo tcpdump -i any
```

This allows Tcpdump to capture packets from the interfaces supported by the `any` pseudo-interface.

**Important:** The `any` interface is a Linux-specific capture feature and does not behave exactly like a physical network interface.

---

## 6. Limit the Number of Captured Packets

By default, Tcpdump continues capturing packets until you manually stop it.

Sometimes we only want to capture a specific number of packets.

For this purpose, we use the `-c` option.

### Command

```bash
sudo tcpdump -i eth0 -c 5
```

Explanation:

- `-i eth0` selects the network interface.
- `-c 5` limits the capture to five packets.

Once five packets have been captured, Tcpdump automatically stops.

### More Examples

Capture 10 packets:

```bash
sudo tcpdump -i eth0 -c 10
```

Capture 20 packets:

```bash
sudo tcpdump -i eth0 -c 20
```

Capture 50 packets:

```bash
sudo tcpdump -i eth0 -c 50
```

**Practical Tip:** Use `-c` when learning Tcpdump because it prevents the terminal from continuously displaying packets.

---

## 7. Display IP Addresses in Numeric Format

When capturing network packets, Tcpdump may try to resolve IP addresses into hostnames.

For example, instead of displaying an IP address, it may display a hostname.

This process is called name resolution.

Sometimes name resolution makes packet analysis slower and less convenient.

To avoid this, we use the `-n` option.

### Command

```bash
sudo tcpdump -i eth0 -n
```

This prevents Tcpdump from resolving IP addresses into hostnames.

### Understanding -n and -nn

Tcpdump provides two useful options:

| Option | Purpose |
|---|---|
| -n | Disables host address resolution |
| -nn | Disables host and port resolution |

Example:

```bash
sudo tcpdump -i eth0 -nn
```

With `-nn`, IP addresses and port numbers are displayed numerically.

### Practical Example

```bash
sudo tcpdump -i eth0 -c 5 -nn
```

This command:

1. Selects the `eth0` interface.
2. Captures five packets.
3. Displays IP addresses and port numbers numerically.
4. Stops automatically after five packets.

**Remember:** `-nn` is especially useful when analyzing network traffic because it avoids unnecessary name resolution.

---

## 8. Save Captured Packets into a PCAP File

Sometimes we don't want to analyze packets immediately.

Instead, we may want to save captured network traffic and investigate it later.

Tcpdump provides the `-w` option to write captured packets into a file.

### Command

```bash
sudo tcpdump -i eth0 -w capture.pcap
```

Explanation:

| Component | Meaning |
|---|---|
| sudo | Elevated permissions |
| tcpdump | Packet analyzer |
| -i eth0 | Selects network interface |
| -w | Writes captured packets to a file |
| capture.pcap | Output file name |

After executing the command:

1. Tcpdump starts capturing packets.
2. Captured packets are written to `capture.pcap`.
3. Capture continues until you press `CTRL + C`.
4. Tcpdump closes the capture file.

### Save a Limited Number of Packets

You can combine `-w` with `-c`.

```bash
sudo tcpdump -i eth0 -c 20 -w capture.pcap
```

This captures 20 packets and saves them into `capture.pcap`.

### Why Do We Save PCAP Files?

PCAP files are useful for:

- Investigating network communication later.
- Sharing authorized packet captures with security analysts.
- Studying network protocols.
- Analyzing suspicious network activity.
- Opening packet captures in Wireshark.

**Important:** Packet captures may contain sensitive information. Store them securely and avoid sharing them publicly.

---

## 9. Read Previously Captured PCAP Files

After saving a PCAP file, we can read it without starting a new live capture.

Tcpdump provides the `-r` option for reading previously captured files.

### Command

```bash
tcpdump -r capture.pcap
```

Explanation:

- `tcpdump` starts the packet analyzer.
- `-r` tells Tcpdump to read an existing capture file.
- `capture.pcap` is the file we want to analyze.

### Read Only Five Packets

```bash
tcpdump -r capture.pcap -c 5
```

This displays the first five packets from the saved capture.

### Read Packets in Numeric Format

```bash
tcpdump -r capture.pcap -nn
```

This displays packet addresses and port numbers numerically.

### Read a PCAP File Without Starting a New Capture

Remember the difference:

| Option | Purpose |
|---|---|
| -w | Save captured packets into a file |
| -r | Read packets from an existing file |

This distinction is important when working with previously recorded network traffic.

---

## 10. Combine Multiple Options

Tcpdump allows us to combine multiple options in a single command.

Let's understand some useful examples.

### Example 1 – Capture Five Packets

```bash
sudo tcpdump -i eth0 -c 5
```

### Example 2 – Capture Ten Packets Without Name Resolution

```bash
sudo tcpdump -i eth0 -c 10 -nn
```

### Example 3 – Capture and Save Twenty Packets

```bash
sudo tcpdump -i eth0 -c 20 -w network.pcap
```

### Example 4 – Read Five Packets from a PCAP File

```bash
tcpdump -r network.pcap -c 5 -nn
```

### Example 5 – Capture Traffic from All Supported Linux Interfaces

```bash
sudo tcpdump -i any -nn
```

These examples combine basic Tcpdump options to make packet capture easier.

---

## 11. Complete Local Practical Lab

Now let's combine what we have learned into one practical exercise.

**Lab Objective:** Capture network packets from your local machine and save them into a PCAP file.

### Step 1 – Identify Network Interfaces

```bash
ip a
```

Find the interface connected to your network.

### Step 2 – Start Packet Capture

```bash
sudo tcpdump -i eth0 -c 20 -nn -w my-capture.pcap
```

Replace `eth0` with your actual interface.

### Step 3 – Generate Network Traffic

While Tcpdump is capturing, open another terminal and execute:

```bash
ping -c 4 1.1.1.1
```

You can also open a website to generate additional network activity.

### Step 4 – Wait for Capture to Complete

Tcpdump will stop automatically after capturing 20 packets.

### Step 5 – Check the PCAP File

```bash
ls -lh my-capture.pcap
```

This displays the file size.

### Step 6 – Read the Captured Packets

```bash
tcpdump -r my-capture.pcap -nn
```

You should now see the packet information stored in your PCAP file.

### Step 7 – Count Captured Packets

```bash
tcpdump -r my-capture.pcap -nn | wc -l
```

This counts the lines displayed by Tcpdump, including its file-reading header. For an exact packet count, use:

```bash
tcpdump -r my-capture.pcap -nn -c 20
```

Or use the capture summary reported when the original capture completes.

**Expected Result:** You should have a PCAP file containing captured network packets that can be read and analyzed later.

---

## 12. Important Commands Cheat Sheet

| Command | Purpose |
|---|---|
| `ip a` | Identify network interfaces |
| `ip link show` | Display network interfaces |
| `sudo tcpdump -D` | List capture interfaces |
| `sudo tcpdump -i eth0` | Capture live packets |
| `sudo tcpdump -i any` | Capture on supported Linux interfaces |
| `sudo tcpdump -i eth0 -c 5` | Capture five packets |
| `sudo tcpdump -i eth0 -n` | Disable IP address resolution |
| `sudo tcpdump -i eth0 -nn` | Disable IP and port resolution |
| `sudo tcpdump -i eth0 -w capture.pcap` | Save packets |
| `tcpdump -r capture.pcap` | Read saved packets |
| `tcpdump -r capture.pcap -c 5` | Read five packets |

---

## 13. Practice Exercises

Try completing these exercises on your own Kali Linux or Ubuntu machine.

**Exercise 1:** Identify all available network interfaces.

**Exercise 2:** Start Tcpdump on your active network interface.

**Exercise 3:** Capture exactly 10 packets.

**Exercise 4:** Capture packets without hostname and port resolution.

**Exercise 5:** Save 20 packets into a file named `practice.pcap`.

**Exercise 6:** Read the first five packets from `practice.pcap`.

**Exercise 7:** Check the size of your saved PCAP file.

**Exercise 8:** Generate ICMP traffic using the ping command and observe the packets.

### Challenge

Complete the following task using a single command:

Capture 15 packets from your active network interface, display IP addresses and ports numerically, and save the captured packets into `challenge.pcap`.

<details>
<summary>Click to reveal the solution</summary>

```bash
sudo tcpdump -i eth0 -c 15 -nn -w challenge.pcap
```

Replace `eth0` with your actual network interface.

</details>

---

## 14. Important Notes

1. Tcpdump continues capturing until stopped manually or until the packet count limit is reached.
2. Use `-c` to limit the number of captured packets.
3. Use `-nn` to avoid unnecessary hostname and port resolution.
4. Use `-w` to save packets into a PCAP file.
5. Use `-r` to read an existing PCAP file.
6. Use `CTRL + C` to stop a running capture.
7. Live capture generally requires root privileges or suitable capture permissions.
8. The `any` interface is available on supported Linux systems.
9. Your actual packet output depends on your network activity.
10. Always capture traffic only on authorized systems and networks.

---

## 15. What We Will Learn Next

In this module, we learned the basic packet capture functionality of Tcpdump.

We now know how to:

- Select network interfaces.
- Capture live network packets.
- Limit packet captures.
- Display numeric addresses.
- Save captured traffic.
- Read saved PCAP files.

In the next module, we will learn how to capture only the packets we are interested in.

**Next Module:** 04 – Packet Filtering

---

## References

- [Official Tcpdump Website](https://www.tcpdump.org/)
- [Tcpdump Manual](https://www.tcpdump.org/manpages/tcpdump.1.html)
- [PCAP Filter Manual](https://www.tcpdump.org/manpages/pcap-filter.7.html)

---

**Author:** Zero2Null Security

**Category:** Network Traffic Analysis

**Focus:** Tcpdump | Packet Capture | Networking | Cybersecurity

**Disclaimer:** This documentation is created for educational purposes. Perform packet capture only on systems and networks where you have proper authorization.
