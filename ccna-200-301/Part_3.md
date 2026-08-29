# CCNA 200-301 — IP Addressing, Subnetting, Address Planning & Router Configuration Labs

> Notes from the CCNA 200-301 course.  
> Topics covered: IP addressing efficiency, subnetting concepts, finding next subnets, address planning, static routing, OSPF, EIGRP, and related labs.

---

# 1. Introduction

## 1.1 Course Timeline

```
| Time              | Topic                                      |
| ----------------- | ------------------------------------------ |
| 06:00 – 15:00     | IP Address & Efficient Use                 |
| 15:10 – 34:30     | Subnet Concepts                            |
| 35:00 – 40:30     | Questions 1 (from chat)                    |
| 43:40 – 1:16:00   | Finding the Next Subnet (Math)             |
| 1:16:30 – 1:29:00 | Questions 2 (from chat)                    |
| 1:30:00 – 1:42:00 | Summary Address & Address Planning         |
| 1:42:45 – 1:45:30 | Questions 3 (from chat)                    |
| 2:01:35 – 2:33:00 | How to Configure a Router                  |
| 2:33:30 – 2:43:00 | Learning Debug                             |
```

---

# 2. IP Address Structure & Efficient Use

An IPv4 address is 32 bits long and can be divided as:

```text
Network (16 bit) + Subnet (8 bit) + Host (8 bit)
<---------------------- 32 bit ---------------------->
```

## 2.1 Private IP Address Ranges

```
| Private IP Networks              | Class | Number of Networks |
| -------------------------------- | ----- | ------------------ |
| 10.0.0.0                         | A     | 1                  |
| 172.16.0.0 – 172.31.0.0          | B     | 16                 |
| 192.168.0.0 – 192.168.255.0      | C     | 256                |
```

## 2.2 Format of Unsubnetted Classful Networks

```
| Class | Network Bits (N) | Host Bits (H) | Usable Hosts           |
| ----- | ---------------- | ------------- | ---------------------- |
| A     | 8                | 24            | 2²⁴ – 2 = 16 777 214   |
| B     | 16               | 16            | 2¹⁶ – 2 = 65 534       |
| C     | 24               | 8             | 2⁸ – 2 = 254           |
```

---

# 3. Subnet Concepts

## 3.1 Borrowing Bits for Subnets and Hosts

- Need **X** subnets → \( 2^S \ge X \)
- Need **Y** hosts per subnet → \( 2^H - 2 \ge Y \)

**Example 1**  
- Network bits (N) = 16, Subnet bits (S) = 8, Host bits (H) = 8  
- Required: ≥ 200 subnets and ≥ 200 hosts per subnet  

```
2^S ≥ 200  →  256 ≥ 200  (OK)
2^H – 2 ≥ 200  →  254 ≥ 200  (OK)
→ Resulting mask: /24
```

---

# 4. Questions 1 (35:00 – 40:30)

**Q1. When to use Class B vs Class C address?**  
In reality people rarely care about the old class. Common practice is `/24` for LAN links and `/30` for WAN point-to-point links.

**Q7. How is subnetting the same or different for IPv6?**  
Almost the same conceptually. IPv4 is 32-bit binary; IPv6 is 128-bit hexadecimal.

---

# 5. Finding the Next Subnet (Math)

(Section covers the step-by-step arithmetic for calculating successive subnets — practice problems from the video.)

---

# 6. Questions 2 (1:16:30 – 1:29:00)

**Q3. When referring to CIDR we only mean Private Address Space — is that correct?**  
No. CIDR has nothing to do with public vs private. It simply means we are **not** restricted to the old classful boundaries (`/8`, `/16`, `/24`).

**Q7. When is `/8` ever useful? Aren’t they huge?**  
There is rarely a reason to use such a large block today, but it can be useful if you want a lot of room to grow inside a single contiguous address space.

**Q11. What’s the difference between the cloud and a data center?**  
The cloud is simply **someone else’s data center**.

**Q12. Would you ever create a WAN connection to the cloud?**  
Yes. Example use-case: multiple servers that need access without being hard-wired, plus a backup connection for resilience.

---

# 7. Summary Address & Address Planning

**Example of a supernet**  
`192.168.0.0/23` covers both `192.168.0.0/24` and `192.168.1.0/24`.

## 7.1 Why We Need an Address Plan

### Example 1 – Contiguous Addressing

```
Datacenter          Cloud
10.0.0.0/24         10.0.1.0/24
10.0.2.0/24
10.0.3.0/24
```

**Benefits**
- Avoids overlapping / conflicting IP ranges.  
  If the same `10.0.0.0/24` is accidentally assigned to both sites, hybrid traffic over Site-to-Site VPN or Direct Connect / ExpressRoute fails due to routing collisions.
- Enables route aggregation / summarization.  
  Upstream routers or firewalls can advertise a single route (`10.0.0.0/22`) instead of four separate `/24` routes → smaller routing tables and lower CPU overhead.

### Example 2 – Non-overlapping Blocks

```
Datacenter                Cloud
10.0.0.0/22  ─────────────►
                    ◄───── 172.16.0.0/22

10.0.0.0/24               172.16.0.0/24
10.0.1.0/24               172.16.1.0/24
10.0.2.0/24               172.16.2.0/24
10.0.3.0/24               172.16.3.0/24
```

---

# 8. Questions 3 (1:42:45 – 1:45:30)

**Q1. Difference between Access, Distribution, and Core switches?**  
- **Core** – fastest, high-capacity routers/switches that form the backbone.  
- **Distribution** – aggregates traffic from access layer into the core.  
- **Access** – where end-user devices plug in.

**Q2. Do we use supernetting for our WAN connections to the cloud?**  
Often yes — cloud providers commonly limit the number of routes they will accept (example given: “cloud only takes 100 routes”).

---

# 9. How to Configure a Router (2:01:35 – 2:33:00)

## 9.1 Basic Setup

1. Create two routers and connect them with a straight-through cable.  
2. Assign IP addresses on the connected interfaces (example `/30`):

```bash
# On Router 2
enable
configure terminal
interface g0/0
ip address 192.168.0.2 255.255.255.252
no shutdown
exit
```

Verify with:

```bash
show ip interface brief
```

## 9.2 Common Problem After Assigning IPs

Only the local router can ping; the other cannot.

**Analogy**: You want to go to the USA but have neither an airplane nor a boat — there is simply **no route**.

### Solution Method 1 — Static Routes

```bash
# On Router 2
ip route 1.0.0.0 255.0.0.0 192.168.0.1
# 1.0.0.0          = destination network
# 255.0.0.0        = mask for that network
# 192.168.0.1      = next-hop (Router 1’s interface)

# On Router 1
ip route 2.0.0.0 255.0.0.0 192.168.0.2
```

Remove a static route with:

```bash
no ip route <network> <mask> <next-hop>
```

### Solution Method 2 — Dynamic Routing (OSPF)

```bash
router ospf 1
network 0.0.0.0 255.255.255.255 area 0
```

Verify neighbor relationship:

```bash
show ip ospf neighbor
```

### Solution Method 3 — Dynamic Routing (EIGRP)

```bash
# On Router 1
router eigrp 1
network 2.2.2.2
network 192.168.0.0
no auto-summary

# On Router 2
router eigrp 1
network 1.1.1.1
network 192.168.0.0
no auto-summary
```

(`router eigrp <AS-number>` starts the EIGRP process; AS number range is 1–65535.)

---

# 10. Learning Debug (2:33:30 – 2:43:00)

```bash
debug ip packet
```

Shows detailed packet processing logs on the console (useful for troubleshooting forwarding and routing decisions).

---

# 11. Quick Command Reference

```
| Command                                      | Purpose                                      |
| -------------------------------------------- | -------------------------------------------- |
| `show ip interface brief`                    | Interface IP / status summary                |
| `ip address <ip> <mask>`                     | Assign IP to an interface                    |
| `no shutdown`                                | Enable the interface                         |
| `ip route <network> <mask> <next-hop>`       | Configure a static route                     |
| `no ip route ...`                            | Remove a static route                        |
| `router ospf <process-id>`                   | Start OSPF process                           |
| `network <net> <wildcard> area <id>`         | Advertise network into OSPF                  |
| `show ip ospf neighbor`                      | Verify OSPF adjacencies                      |
| `router eigrp <AS>`                          | Start EIGRP process                          |
| `network <network>`                          | Advertise network into EIGRP                 |
| `no auto-summary`                            | Disable automatic classful summarization     |
| `debug ip packet`                            | Enable IP packet debugging                   |
```

---

# 12. Key Concepts to Remember

```text
IPv4 address        → 32-bit (Network + Subnet + Host)
Classful vs CIDR    → Classful uses fixed /8 /16 /24; CIDR is flexible
Private ranges      → 10/8, 172.16/12, 192.168/16
Subnet math         → 2^S ≥ required subnets; 2^H – 2 ≥ required hosts
Address planning    → Avoid overlap, enable summarization
Static route        → Manually define path to a network
OSPF / EIGRP        → Dynamic routing protocols that automatically share routes
```

**Addressing reminder**
- Layer 3 → IP address
- Routing decisions are based on the destination network and the longest matching prefix.

---

# 13. Lab Checklist

- [ ] Understand private address ranges and classful boundaries
- [ ] Calculate required subnet and host bits
- [ ] Find successive subnets
- [ ] Design non-overlapping address plans for multi-site / hybrid environments
- [ ] Configure interface IP addresses and bring them up
- [ ] Configure and verify static routes
- [ ] Configure and verify OSPF
- [ ] Configure and verify EIGRP
- [ ] Use `debug ip packet` for troubleshooting

---

# 14. Key Takeaway

Efficient IP addressing and proper subnetting prevent waste and enable route summarization.  
A solid address plan avoids overlaps between data centers, cloud environments, and WAN links.  
Static routes work for simple topologies; OSPF and EIGRP scale better by automatically exchanging routing information.

These concepts form the foundation for the rest of the **CCNA 200-301** routing and inter-VLAN material.
```