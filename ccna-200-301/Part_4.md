# CCNA 200-301 — Routing Concepts, Static Routes, EIGRP & Lab

> Notes from the CCNA 200-301 course.  
> Topics covered: Routing fundamentals, router configuration commands, static routing, EIGRP, routing between switches, and a multi-router lab.

---

# 1. Introduction

## 1.1 Course Timeline

```
| Time              | Topic                                      |
| ----------------- | ------------------------------------------ |
| 06:30 – 12:30     | Routing Concepts                           |
| 30:10 – 43:30     | Router Configuration Commands              |
| 44:30 – 1:05:30   | Questions 1 (from chat)                    |
| 1:05:50 – 1:46:00 | Routing Between Switches                   |
| 1:46:30 – 2:17:00 | Lab — Three Routers Connectivity           |
```

---

# 2. Routing Concepts

Routing is the process of forwarding packets between different networks based on Layer-3 (IP) information.

Key ideas:
- A router makes forwarding decisions using its **routing table**.
- Routes can be learned **statically** (manually configured) or **dynamically** (via a routing protocol such as OSPF or EIGRP).
- Without a route to a destination network, the router drops the packet (or sends it to a default route if one exists).

---

# 3. Router Configuration Commands

Common commands used when configuring routers:

```bash
enable
configure terminal
interface <interface>
ip address <ip> <mask>
no shutdown
ip route <destination-network> <subnet-mask> <next-hop-ip | exit-interface>
router eigrp <AS-number>
network <ip-address> [wildcard-mask]
no auto-summary
show ip interface brief
show ip route
ping <ip>
```

---

# 4. Questions 1 (44:30 – 1:05:30)

**Q2. When should you choose switching with Spanning Tree vs routing?**  
Spanning Tree can introduce complexity and is often harder to troubleshoot. Many designs prefer routing because it is generally faster to converge and easier to avoid loops with proper configuration. Switch loops are avoided by STP; routing loops are avoided by good design and routing protocols.

**Q3. What is the advantage of using a default route?**  
Without a default route you must manually add a route for every destination network. A default route (`0.0.0.0/0`) provides a “last resort” path so the router knows where to send packets when no more specific route exists.

**Q4. Do you configure the routing table yourself or is it automatic?**  
- If you run a dynamic routing protocol (OSPF, EIGRP, etc.) → routes are learned automatically.  
- If you do not → you must configure static routes yourself.

**Q8. When configuring a static route, is the next-hop the default gateway of the LAN?**  
No. The next-hop is the IP address of the **next router** on the link toward the destination (the neighboring router’s interface).

---

# 5. Routing Between Switches

(Section covers how routers are used to route traffic between different Layer-2 domains / VLANs connected via switches.)

---

# 6. Lab — Three Routers Connectivity (1:46:30 – 2:17:00)

## 6.1 Topology Overview

- Create **3 routers** and **1 switch**.
- Connect the devices with cables as required by the lab.

### Initial Interface Configuration

**Router 1 (R1)**
```bash
interface loopback 0
ip address 1.1.1.1 255.255.255.255
no shutdown

interface g0/0
ip address 192.168.1.1 255.255.255.252
no shutdown
```

**Router 2 (R2)**
```bash
interface loopback 0
ip address 2.2.2.2 255.255.255.255
no shutdown

interface g0/0
ip address 192.168.1.2 255.255.255.252
no shutdown
```

**Router 3 (R3)**
```bash
interface loopback 0
ip address 3.3.3.3 255.255.255.255
no shutdown

interface g0/0
ip address 192.168.1.5 255.255.255.252
no shutdown
```

**Mission**: Make every router able to ping every other router’s IP addresses (including loopbacks).

---

## 6.2 Method 1 — Static Routes

### Step 1: Understand the subnets

- R1 ↔ R2 are on subnet `192.168.1.0/30` (hosts .1 and .2)
- R3 is on a different subnet (`192.168.1.4/30` — hosts .5 and .6)

R1 and R2 can reach each other, but R3 cannot reach R2 until R2 has an interface on R3’s subnet.

### Step 2: Add a second interface on R2 toward R3

```bash
# On R2
interface g0/1
ip address 192.168.1.6 255.255.255.252
no shutdown
```

Now:
- R2 can ping `192.168.1.5`
- R3 can ping `192.168.1.6` (and `192.168.1.2`)

R1 and R3 still cannot reach each other because they do not have routes through R2.

### Step 3: Static routes between the transit subnets

**Syntax**
```bash
ip route <DESTINATION-NETWORK> <SUBNET-MASK> <NEXT-HOP-IP | EXIT-INTERFACE>
```

```bash
# On R1 — reach R3’s subnet via R2
ip route 192.168.1.4 255.255.255.252 192.168.1.2

# On R3 — reach R1’s subnet via R2
ip route 192.168.1.0 255.255.255.252 192.168.1.6
```

R1 and R3 can now ping each other’s transit interfaces.

### Step 4: Static routes for the loopback addresses

```bash
# On R1
ip route 2.2.2.2 255.255.255.255 192.168.1.2
ip route 3.3.3.3 255.255.255.255 192.168.1.2

# On R2
ip route 1.1.1.1 255.255.255.255 192.168.1.1
ip route 3.3.3.3 255.255.255.255 192.168.1.5

# On R3
ip route 1.1.1.1 255.255.255.255 192.168.1.6
ip route 2.2.2.2 255.255.255.255 192.168.1.6
```

### Step 5: Verification (Success Rate should be 5/5)

**From R1**
```bash
ping 2.2.2.2
ping 3.3.3.3
ping 192.168.1.2
ping 192.168.1.5
ping 192.168.1.6
```

**From R2**
```bash
ping 1.1.1.1
ping 3.3.3.3
ping 192.168.1.1
ping 192.168.1.5
```

**From R3**
```bash
ping 1.1.1.1
ping 2.2.2.2
ping 192.168.1.1
ping 192.168.1.2
ping 192.168.1.6
```

---

## 6.3 Method 2 — EIGRP

### EIGRP Network Command Syntax

```bash
network <IP_ADDRESS> [WILDCARD_MASK]
```

- `<IP_ADDRESS>` — base IP or network prefix to match.
- `[WILDCARD_MASK]` — inverted subnet mask that controls matching precision.
  - `0` = must match exactly
  - `255` = ignore / anything goes

**Examples**
```bash
# Match any interface in 192.168.1.0/30 (hosts .0–.3)
network 192.168.1.0 0.0.0.3

# Match the exact host 1.1.1.1/32
network 1.1.1.1 0.0.0.0

# Match any interface in 192.168.1.0/24
network 192.168.1.0 0.0.0.255
```

### EIGRP Configuration

```bash
# Common on all routers
router eigrp <AS-number>   # AS number range 1–65535
no auto-summary
```

**R1**
```bash
router eigrp 1
no auto-summary
network 192.168.1.0 0.0.0.3
network 1.1.1.1 0.0.0.0
```

**R2**
```bash
router eigrp 1
no auto-summary
network 192.168.1.0 0.0.0.3
network 192.168.1.4 0.0.0.3
network 2.2.2.2 0.0.0.0
```

**R3**
```bash
router eigrp 1
no auto-summary
network 192.168.1.4 0.0.0.3
network 3.3.3.3 0.0.0.0
```

After EIGRP neighbors form, all routers learn the necessary routes automatically and can ping one another.

---

# 7. Quick Command Reference

```
| Command                                              | Purpose                                      |
| ---------------------------------------------------- | -------------------------------------------- |
| `ip address <ip> <mask>`                             | Assign IP to an interface                    |
| `no shutdown`                                        | Enable the interface                         |
| `ip route <net> <mask> <next-hop>`                   | Configure a static route                     |
| `router eigrp <AS>`                                  | Start EIGRP process                          |
| `network <ip> [wildcard]`                            | Advertise networks into EIGRP                |
| `no auto-summary`                                    | Disable classful automatic summarization     |
| `show ip interface brief`                            | Interface IP / status summary                |
| `show ip route`                                      | Display the routing table                    |
| `ping <ip>`                                          | Test reachability                            |
```

---

# 8. Key Concepts to Remember

```text
Routing table          → Used by the router to decide where to forward packets
Static route           → Manually configured path to a destination network
Dynamic routing        → Protocol (OSPF, EIGRP, etc.) automatically exchanges routes
Next-hop               → IP of the neighboring router (not the LAN default gateway)
Default route          → 0.0.0.0/0 — path of last resort
EIGRP network command  → Uses wildcard mask to select which interfaces join the process
Loopback               → Virtual interface often used as a stable router ID / test address
```

**Static vs Dynamic**
- Static → simple, full control, does not scale well
- Dynamic → scales better, automatic updates, requires a routing protocol

---

# 9. Lab Checklist

- [ ] Configure loopback and physical interfaces with correct IP addresses
- [ ] Understand the difference between connected subnets
- [ ] Add secondary interfaces when needed to connect different subnets
- [ ] Configure static routes for transit networks and host routes (/32)
- [ ] Verify full connectivity with `ping` (5/5 success rate)
- [ ] Configure EIGRP with appropriate `network` statements and wildcard masks
- [ ] Disable auto-summary
- [ ] Verify EIGRP neighbors and routes in the routing table

---

# 10. Key Takeaway

Without routes, routers can only reach networks that are directly connected.  
Static routes give precise control but become cumbersome as the network grows.  
EIGRP (and other dynamic protocols) automatically share routing information once the correct networks are advertised, making multi-router topologies much easier to maintain.

These concepts form the foundation for the rest of the **CCNA 200-301** routing material.
```