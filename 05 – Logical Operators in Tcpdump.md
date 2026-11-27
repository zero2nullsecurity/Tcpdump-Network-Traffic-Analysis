# 05 – Logical Operators in Tcpdump

## 1. Introduction to Logical Operators

In the previous module, we learned how to filter network packets using:

- Host IP addresses
- Source IP addresses
- Destination IP addresses
- Port numbers
- Source and destination ports
- Network protocols

However, sometimes a single filter is not enough.

For example, imagine you are monitoring network traffic and want to capture:

- TCP traffic going through port 80.
- UDP traffic going through port 53.
- All traffic except TCP traffic.
- Traffic involving a specific IP address and port.

Running separate commands for every condition can take more time.

Tcpdump provides **Logical Operators** that allow us to combine multiple filtering conditions in one command.

Logical operators help us create more specific packet filters.

### Three Main Logical Operators

Tcpdump supports three commonly used logical operators:

| Operator | Meaning | Purpose |
|---|---|---|
| `and` | Both conditions must be true | Matches packets satisfying both conditions |
| `or` | At least one condition must be true | Matches packets satisfying either condition |
| `not` | Reverses a condition | Excludes packets matching a condition |

Let's understand each operator with practical examples.

---

## 2. Understanding the AND Operator

The `and` operator is used when we want to capture packets that match two or more conditions at the same time.

**In simple words:** Both conditions must be true for a packet to match the filter.

### Basic Syntax

```bash
tcpdump CONDITION1 and CONDITION2
```

### Example 1 – Capture TCP Traffic on Port 22

```bash
sudo tcpdump -i eth0 tcp and port 22 -nn
```

Let's understand this command:

| Component | Explanation |
|---|---|
| `sudo` | Runs the command with elevated privileges |
| `tcpdump` | Starts the packet analyzer |
| `-i eth0` | Selects the network interface |
| `tcp` | Selects TCP packets |
| `and` | Requires both conditions to match |
| `port 22` | Selects packets involving port 22 |
| `-nn` | Disables hostname and port resolution |

**What happens here?**

Tcpdump checks two conditions:

1. Is the packet using TCP?
2. Is the source or destination port 22?

Only packets satisfying both conditions are selected.

Port 22 is commonly used by SSH.

Therefore, this command selects TCP traffic involving SSH port 22.

### Example 2 – Capture Traffic from a Specific Host on Port 443

```bash
sudo tcpdump -i eth0 host 192.168.1.10 and port 443 -nn
```

This command selects packets that:

- Involve IP address `192.168.1.10`.
- Use source or destination port `443`.

Both conditions must match.

**Use Case:** A network analyst can use this filter to monitor traffic involving a specific system and HTTPS-related port 443.

### Example 3 – Capture TCP Traffic from a Specific Source IP

```bash
sudo tcpdump -i eth0 src host 192.168.1.10 and tcp -nn
```

This command selects TCP packets originating from `192.168.1.10`.

The conditions are:

1. Source IP must be `192.168.1.10`.
2. Protocol must be TCP.

### AND Operator – Truth Table

The AND operator returns TRUE only when both conditions are TRUE.

| Condition A | Condition B | Result |
|---|---|---|
| TRUE | TRUE | TRUE |
| TRUE | FALSE | FALSE |
| FALSE | TRUE | FALSE |
| FALSE | FALSE | FALSE |

**Remember:** AND means both conditions must match.

---

## 3. Understanding the OR Operator

The `or` operator is used when we want to capture packets matching either of two conditions.

**In simple words:** At least one condition must be true.

### Basic Syntax

```bash
tcpdump CONDITION1 or CONDITION2
```

### Example 1 – Capture TCP or UDP Traffic

```bash
sudo tcpdump -i eth0 tcp or udp -nn
```

Let's understand this command:

- `tcp` selects TCP packets.
- `or` connects two conditions.
- `udp` selects UDP packets.

Tcpdump selects packets if they match either condition.

This means:

- TCP packets are captured.
- UDP packets are captured.
- Other protocols that do not match either condition are excluded.

### Example 2 – Capture Traffic on Port 80 or Port 443

```bash
sudo tcpdump -i eth0 port 80 or port 443 -nn
```

This command selects packets involving either port 80 or port 443.

Port 80 is commonly used by HTTP.

Port 443 is commonly used by HTTPS over TCP and also by HTTP/3 over QUIC using UDP.

**Important:** This filter checks port numbers. It does not guarantee that the traffic is actually HTTP or HTTPS.

### Example 3 – Capture ICMP or ARP Traffic

```bash
sudo tcpdump -i eth0 icmp or arp -nn
```

This command selects:

- ICMP packets.
- ARP packets.

It is useful when studying network communication and address resolution.

### Example 4 – Capture Traffic from Two Different Hosts

```bash
sudo tcpdump -i eth0 host 192.168.1.10 or host 192.168.1.20 -nn
```

This command selects packets involving either of the two specified hosts.

### OR Operator – Truth Table

The OR operator returns TRUE when at least one condition is TRUE.

| Condition A | Condition B | Result |
|---|---|---|
| TRUE | TRUE | TRUE |
| TRUE | FALSE | TRUE |
| FALSE | TRUE | TRUE |
| FALSE | FALSE | FALSE |

**Remember:** OR means either condition can match.

---

## 4. Understanding the NOT Operator

The `not` operator is used when we want to exclude packets matching a particular condition.

**In simple words:** NOT reverses the result of a condition.

### Basic Syntax

```bash
tcpdump not CONDITION
```

You can also use the `!` symbol as an alternative to `not`.

### Example 1 – Capture All Traffic Except TCP

```bash
sudo tcpdump -i eth0 not tcp -nn
```

This command selects packets that do not match the TCP condition.

For example, it may display:

- UDP packets.
- ICMP packets.
- ARP packets.
- Other non-TCP packets visible to the selected capture interface.

TCP packets are excluded.

### Example 2 – Exclude a Specific Host

```bash
sudo tcpdump -i eth0 not host 192.168.1.10 -nn
```

This command selects packets that do not involve `192.168.1.10`.

Packets involving that IP address as either the source or destination are excluded.

### Example 3 – Exclude SSH Traffic

```bash
sudo tcpdump -i eth0 not port 22 -nn
```

This command excludes packets where either the source or destination port is 22.

Other matching traffic can still be displayed.

### Example 4 – Exclude ICMP Traffic

```bash
sudo tcpdump -i eth0 not icmp -nn
```

This command selects packets that do not match the ICMP protocol.

### NOT Operator – Truth Table

The NOT operator reverses a condition.

| Condition A | NOT A |
|---|---|
| TRUE | FALSE |
| FALSE | TRUE |

**Remember:** NOT means exclude or reverse the condition.

---

## 5. Combining Multiple Logical Operators

Now that we understand AND, OR, and NOT individually, we can combine them to create more advanced filters.

This is one of the most useful features of Tcpdump.

### Example 1 – Capture TCP Traffic on Port 80 or 443

```bash
sudo tcpdump -i eth0 tcp and \( port 80 or port 443 \) -nn
```

Let's understand the command:

1. `tcp` selects TCP packets.
2. `and` requires the packet to satisfy the protocol condition.
3. `port 80 or port 443` selects either port.
4. Parentheses group the two port conditions together.
5. `-nn` disables hostname and port resolution.

**What does this command capture?**

It selects TCP packets involving either port 80 or port 443.

Why do we use parentheses?

Because we want the OR condition to be evaluated as a group before combining it with the TCP condition.

### Example 2 – Capture UDP Traffic on Port 53 or 123

```bash
sudo tcpdump -i eth0 udp and \( port 53 or port 123 \) -nn
```

Here:

- `udp` selects UDP packets.
- `port 53` selects packets involving port 53.
- `port 123` selects packets involving port 123.
- `or` allows either port.
- `and` requires UDP.

Port 53 is commonly used by DNS.

Port 123 is commonly used by NTP.

### Example 3 – Capture Traffic from a Host Except SSH

```bash
sudo tcpdump -i eth0 host 192.168.1.10 and not port 22 -nn
```

This command selects packets involving `192.168.1.10`, excluding packets involving port 22.

### Example 4 – Capture TCP or UDP Traffic but Exclude a Host

```bash
sudo tcpdump -i eth0 \( tcp or udp \) and not host 192.168.1.20 -nn
```

This command:

1. Selects TCP or UDP packets.
2. Excludes packets involving `192.168.1.20`.

### Example 5 – Capture Traffic from Two Hosts but Exclude Port 22

```bash
sudo tcpdump -i eth0 \( host 192.168.1.10 or host 192.168.1.20 \) and not port 22 -nn
```

This command selects packets involving either specified host, excluding packets where the source or destination port is 22.

---

## 6. Understanding Parentheses in Tcpdump

Parentheses are important when combining multiple logical operators.

They allow us to group conditions and control how the filter expression is evaluated.

Without proper grouping, the filter may not select the packets we intended.

### Example Without Parentheses

```bash
sudo tcpdump -i eth0 tcp and port 80 or port 443 -nn
```

This expression is interpreted according to filter-operator precedence as:

```text
(tcp and port 80) or port 443
```

This means:

- TCP traffic involving port 80.
- OR any traffic involving port 443.

The second condition is not restricted to TCP.

### Correct Command with Parentheses

```bash
sudo tcpdump -i eth0 tcp and \( port 80 or port 443 \) -nn
```

This is interpreted as:

```text
tcp and (port 80 or port 443)
```

Now both port conditions are restricted to TCP traffic.

### Why Do We Use Backslashes?

In Linux shells such as Bash, parentheses have special meanings.

If we write parentheses directly, the shell may interpret them before Tcpdump receives the filter expression.

Therefore, we escape them using a backslash:

```bash
\(
\)
```

Another option is to place the complete filter expression inside single quotes:

```bash
sudo tcpdump -i eth0 'tcp and (port 80 or port 443)' -nn
```

Both approaches allow Tcpdump to receive the grouped expression correctly.

**Important:** The filter expression follows the precedence rules of the packet-filter language. Use parentheses whenever you want to make grouping clear.

---

## 7. Understanding Operator Precedence

When multiple logical operators are used together, their evaluation order matters.

In Tcpdump's packet-filter expressions, the general precedence from higher to lower is:

1. NOT
2. AND
3. OR

This means NOT is evaluated before AND, and AND is evaluated before OR.

### Example

```bash
tcp and port 80 or port 443
```

This is interpreted as:

```text
(tcp and port 80) or port 443
```

It does not mean:

```text
tcp and (port 80 or port 443)
```

If you want the second meaning, use parentheses.

### Correct Version

```bash
sudo tcpdump -i eth0 'tcp and (port 80 or port 443)' -nn
```

**Learning Tip:** Whenever you combine multiple conditions, use parentheses to make your intended logic clear.

---

## 8. Using Logical Operators with PCAP Files

Logical operators are not limited to live packet capture.

We can also use them to filter packets from previously saved PCAP files.

Suppose we have a file named:

```text
traffic.pcap
```

### Example 1 – Read TCP or UDP Packets

```bash
tcpdump -r traffic.pcap 'tcp or udp' -nn
```

This displays TCP and UDP packets from the saved file.

### Example 2 – Read DNS or ICMP Packets

```bash
tcpdump -r traffic.pcap 'port 53 or icmp' -nn
```

This selects packets matching either condition.

### Example 3 – Read All Packets Except TCP

```bash
tcpdump -r traffic.pcap 'not tcp' -nn
```

This excludes TCP packets.

### Example 4 – Read Traffic from a Specific Host on Port 80 or 443

```bash
tcpdump -r traffic.pcap 'host 192.168.1.10 and (port 80 or port 443)' -nn
```

This selects packets involving the specified host and either port 80 or port 443.

### Example 5 – Exclude a Specific Host and Capture ICMP

```bash
tcpdump -r traffic.pcap 'icmp and not host 192.168.1.20' -nn
```

This selects ICMP packets that do not involve the specified host.

---

## 9. Practical Lab – Logical Operators

Now let's perform practical exercises on our local Kali Linux or Ubuntu machine.

**Lab Objective:** Learn how to combine multiple packet-filtering conditions using AND, OR, and NOT.

### Requirements

- Kali Linux or Ubuntu.
- Tcpdump installed.
- A working network interface.
- Internet connection for generating example traffic.

### Step 1 – Identify Your Network Interface

```bash
ip a
```

Identify your active interface.

Replace `eth0` in the following examples with your actual interface name.

### Step 2 – Capture TCP and UDP Traffic

```bash
sudo tcpdump -i eth0 'tcp or udp' -nn
```

Observe both TCP and UDP packets.

### Step 3 – Capture DNS or ICMP Traffic

```bash
sudo tcpdump -i eth0 'port 53 or icmp' -nn
```

Open another terminal and generate traffic:

```bash
nslookup example.com
```

Then:

```bash
ping -c 4 1.1.1.1
```

Observe the matching packets.

### Step 4 – Capture TCP Traffic on Port 80 or 443

```bash
sudo tcpdump -i eth0 'tcp and (port 80 or port 443)' -nn
```

Open a website in your browser to generate network traffic.

Observe the matching TCP packets.

### Step 5 – Exclude a Specific Host

Replace the example IP with an IP address available in your own network.

```bash
sudo tcpdump -i eth0 'not host 192.168.1.20' -nn
```

Observe packets that do not involve the specified host.

### Step 6 – Capture UDP Traffic Except Port 53

```bash
sudo tcpdump -i eth0 'udp and not port 53' -nn
```

This selects UDP packets that do not involve port 53.

### Step 7 – Stop Packet Capture

Press:

```text
CTRL + C
```

Tcpdump will stop capturing and display the capture summary.

**Expected Result:** You should understand how multiple filter conditions work together.

---

## 10. Practical Lab – Save and Analyze Filtered Traffic

In this exercise, we will save network traffic and apply logical operators to the saved PCAP file.

### Step 1 – Capture Network Traffic

```bash
sudo tcpdump -i eth0 -c 100 -w logical-lab.pcap
```

This saves up to 100 captured packets into `logical-lab.pcap`.

Generate some network activity while the capture is running.

### Step 2 – Read TCP or UDP Packets

```bash
tcpdump -r logical-lab.pcap 'tcp or udp' -nn
```

### Step 3 – Exclude ICMP Packets

```bash
tcpdump -r logical-lab.pcap 'not icmp' -nn
```

### Step 4 – Read DNS or SSH-Related Traffic

```bash
tcpdump -r logical-lab.pcap 'port 53 or port 22' -nn
```

### Step 5 – Read TCP Traffic Except Port 22

```bash
tcpdump -r logical-lab.pcap 'tcp and not port 22' -nn
```

**Expected Result:** You should be able to apply logical operators to an existing PCAP file without performing a new live capture.

---

## 11. Important Commands Cheat Sheet

| Command | Purpose |
|---|---|
| `tcp and port 22` | TCP traffic involving port 22 |
| `tcp or udp` | TCP or UDP packets |
| `not tcp` | Exclude TCP packets |
| `port 80 or port 443` | Traffic involving port 80 or 443 |
| `icmp or arp` | ICMP or ARP packets |
| `host 192.168.1.10 and tcp` | TCP traffic involving a host |
| `src host 192.168.1.10 and tcp` | TCP packets from a specific source |
| `dst host 192.168.1.10 and tcp` | TCP packets to a specific destination |
| `tcp and (port 80 or port 443)` | TCP traffic on either port |
| `udp and not port 53` | UDP packets excluding port 53 |
| `(tcp or udp) and not host 192.168.1.20` | TCP or UDP traffic excluding a host |

**Note:** Add the appropriate interface option for live capture, or `-r filename.pcap` for saved captures.

---

## 12. Common Mistakes and Troubleshooting

### Mistake 1 – Forgetting Parentheses

Incorrect for TCP-only traffic on ports 80 and 443:

```bash
tcp and port 80 or port 443
```

Correct:

```bash
'tcp and (port 80 or port 443)'
```

Without parentheses, the port 443 condition is not restricted to TCP.

### Mistake 2 – Forgetting Shell Escaping

Incorrect in a shell:

```bash
tcpdump -i eth0 tcp and (port 80 or port 443)
```

Correct:

```bash
tcpdump -i eth0 'tcp and (port 80 or port 443)'
```

### Mistake 3 – Using the Wrong Interface

If your system uses `wlan0` instead of `eth0`, replace the interface name.

Check available interfaces:

```bash
ip a
```

### Mistake 4 – No Packets Appearing

Possible reasons:

- No matching traffic is being generated.
- The wrong interface is selected.
- The filter is too restrictive.
- The destination service is not being used.
- The traffic is not visible to the selected interface.

Try generating traffic and checking your interface again.

### Mistake 5 – Confusing AND and OR

Remember:

- AND requires both conditions.
- OR requires at least one condition.
- NOT excludes matching conditions.

---

## 13. Practice Exercises

Try solving these exercises without looking at the answers.

### Beginner Level

1. Capture TCP or UDP packets.
2. Capture ICMP or ARP packets.
3. Exclude TCP packets.
4. Capture traffic involving port 80 or port 443.
5. Exclude traffic involving port 22.

### Intermediate Level

6. Capture TCP traffic involving port 80 or port 443.
7. Capture UDP traffic involving port 53 or port 123.
8. Capture traffic from `192.168.1.10` except SSH traffic.
9. Capture TCP or UDP traffic but exclude `192.168.1.20`.
10. Capture ICMP traffic except packets involving `192.168.1.20`.

### Advanced Challenge

You have a saved packet capture file:

```text
traffic.pcap
```

Your task is to write commands for the following requirements:

1. Display TCP and UDP packets.
2. Display all packets except ICMP.
3. Display traffic involving port 53 or port 443.
4. Display TCP traffic involving port 80 or port 443.
5. Display packets involving either `192.168.1.10` or `192.168.1.20`, but exclude port 22.
6. Display UDP traffic except packets involving port 53.

### Challenge Answers

<details>
<summary>Click to reveal solutions</summary>

**1. TCP and UDP:**

```bash
tcpdump -r traffic.pcap 'tcp or udp' -nn
```

**2. Exclude ICMP:**

```bash
tcpdump -r traffic.pcap 'not icmp' -nn
```

**3. Port 53 or 443:**

```bash
tcpdump -r traffic.pcap 'port 53 or port 443' -nn
```

**4. TCP on port 80 or 443:**

```bash
tcpdump -r traffic.pcap 'tcp and (port 80 or port 443)' -nn
```

**5. Two hosts, excluding port 22:**

```bash
tcpdump -r traffic.pcap '(host 192.168.1.10 or host 192.168.1.20) and not port 22' -nn
```

**6. UDP except port 53:**

```bash
tcpdump -r traffic.pcap 'udp and not port 53' -nn
```

</details>

---

## 14. Key Takeaways

After completing this module, you should understand:

- What logical operators are.
- How the AND operator works.
- How the OR operator works.
- How the NOT operator works.
- How to combine multiple filtering conditions.
- Why parentheses are important.
- How operator precedence affects filters.
- How to apply logical operators to saved PCAP files.
- How to create more specific packet filters.

**Most Important Rule:**

- AND = Both conditions must match.
- OR = At least one condition must match.
- NOT = Exclude matching conditions.

---

## 15. What We Will Learn Next

In this module, we learned how to combine multiple filtering conditions using logical operators.

However, sometimes we need to investigate previously captured network traffic in more detail.

For example:

- How many packets were captured?
- Which IP addresses communicated with each other?
- Which protocols were used?
- How can we read and investigate saved packet capture files?

For these tasks, we need to understand PCAP file analysis.

In the next module, we will learn how to read, inspect, count, and analyze previously captured network packets.

**Next Module:** 06 – PCAP File Analysis

---

## References

- [Official Tcpdump Website](https://www.tcpdump.org/)
- [Tcpdump Manual](https://www.tcpdump.org/manpages/tcpdump.1.html)
- [PCAP Filter Manual](https://www.tcpdump.org/manpages/pcap-filter.7.html)
- [Wireshark Official Website](https://www.wireshark.org/)

---

**Author:** Zero2Null Security

**Category:** Network Traffic Analysis

**Focus:** Tcpdump | Logical Operators | Packet Filtering | Networking | Cybersecurity

**Disclaimer:** This documentation is created for educational purposes. Perform packet capture and network analysis only on systems and networks where you have proper authorization.
