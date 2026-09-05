# CCNA 200-301 — OSPF Troubleshooting, ABR Design & Multi-Area Lab

> Notes from the CCNA 200-301 course.  
> Topics covered: OSPF troubleshooting, debug, route preference, Area Border Routers (ABR), multi-area design, stub/totally stubby areas, LSA types, redistribution, and a full lab.

---

# 1. Introduction

## 1.1 Course Timeline

```
| Time              | Topic                                      |
| ----------------- | ------------------------------------------ |
| 05:00 – 42:00     | How to Troubleshoot                        |
| 43:00 – 52:00     | Questions 1 (from chat)                    |
| 1:02:30 – 1:10:00 | OSPF Debug                                 |
| 1:11:00 – 1:13:30 | Questions 2 (from chat)                    |
| 1:15:00 – 1:52:00 | OSPF Area Border Router and Its Design     |
| 1:57:00 – 2:50:00 | Lab — Multi-Area OSPF Topology             |
| 3:03:45 – 3:10:00 | OSPF Network Types Concept                 |
```

---

# 2. How to Troubleshoot (05:00 – 42:00)

Troubleshooting OSPF typically involves checking:

- Interface status and IP addressing (`show ip interface brief`)
- OSPF neighbor relationships (`show ip ospf neighbor`)
- OSPF database (`show ip ospf database`)
- Routing table (`show ip route` / `show ip route ospf`)
- Process and area configuration
- MTU mismatches, authentication, and network type mismatches

Common debug commands (use with caution in production):

```bash
debug ip ospf adj
debug ip ospf events
debug ip ospf packet
```

---

# 3. Questions 1 (43:00 – 52:00)

(Questions and answers from the live chat during this segment.)

---

# 4. OSPF Debug (1:02:30 – 1:10:00)

Useful debug commands to observe adjacency formation, LSA flooding, and SPF calculations:

```bash
debug ip ospf adj
debug ip ospf events
debug ip ospf packet
undebug all          # turn off all debugging
```

---

# 5. Questions 2 (1:11:00 – 1:13:30)

**Q3. How does the router choose between an OSPF route and other routes (for example EIGRP)?**  
The router uses **Administrative Distance (AD)** — a trust ranking.  

- Lower AD = more trusted.  
- Example: a static route (`ip route`) has AD 1 and is preferred over EIGRP (AD 90), which is preferred over OSPF (AD 110).

---

# 6. OSPF Area Border Router (ABR) and Its Design (1:15:00 – 1:52:00)

An **Area Border Router (ABR)** is a router that has interfaces in more than one OSPF area (typically Area 0 + one or more non-backbone areas).

Key design points:
- All non-backbone areas must connect to Area 0 (the backbone).
- ABRs generate Type 3 Summary LSAs to advertise inter-area routes.
- ABRs can filter or summarize routes between areas.
- Special area types (Stub, Totally Stubby, NSSA) are configured on the ABR to control what external and inter-area information is injected into an area.

---

# 7. Lab — Multi-Area OSPF Topology (1:57:00 – 2:50:00)

## 7.1 Initial Topology (Triangle – Area 0)

```
R1 -------- R2
 \          /
  \        /
   \      /
    \    /
      R3
```

### Base Configuration

**Router 1 (R1)**
```bash
interface loopback 0
ip address 1.1.1.1 255.255.255.255
no shutdown

interface g0/0
ip address 192.16.0.1 255.255.255.252
no shutdown

interface g0/1
ip address 172.16.0.5 255.255.255.252
no shutdown

router ospf 1
network 1.1.1.1 0.0.0.0 area 0
network 192.16.0.0 0.0.0.3 area 0
network 172.16.0.4 0.0.0.3 area 0

line vty 0 4
no login
password cisco
transport input all
```

**Router 2 (R2)**
```bash
interface loopback 0
ip address 2.2.2.2 255.255.255.255
no shutdown

interface g0/0
ip address 192.16.0.2 255.255.255.252
no shutdown

interface g0/1
ip address 172.16.0.9 255.255.255.252
no shutdown

router ospf 1
network 2.2.2.2 0.0.0.0 area 0
network 192.16.0.0 0.0.0.3 area 0
network 172.16.0.8 0.0.0.3 area 0

line vty 0 4
no login
password cisco
transport input all
```

**Router 3 (R3)**
```bash
interface loopback 0
ip address 3.3.3.3 255.255.255.255
no shutdown

interface g0/0
ip address 172.16.0.6 255.255.255.252
no shutdown

interface g0/1
ip address 172.16.0.10 255.255.255.252
no shutdown

router ospf 1
network 3.3.3.3 0.0.0.0 area 0
network 172.16.0.4 0.0.0.3 area 0
network 172.16.0.8 0.0.0.3 area 0

line vty 0 4
no login
password cisco
transport input all
```

### Subnets in the Triangle (Area 0)

```
| Subnet              | Link          | Usable IPs                          |
| ------------------- | ------------- | ----------------------------------- |
| 192.16.0.0/30       | R1 ↔ R2       | 192.16.0.1 (R1), 192.16.0.2 (R2)    |
| 172.16.0.4/30       | R1 ↔ R3       | 172.16.0.5 (R1), 172.16.0.6 (R3)    |
| 172.16.0.8/30       | R2 ↔ R3       | 172.16.0.9 (R2), 172.16.0.10 (R3)   |
```

---

## 7.2 Comparison: Static Route vs EIGRP vs OSPF

```
| Feature / Protocol       | Static Routing (`ip route`) | EIGRP (`router eigrp`)      | OSPF (`router ospf`)              |
| ------------------------ | --------------------------- | --------------------------- | --------------------------------- |
| Protocol Type            | Manual / Static             | Distance-Vector (Hybrid)    | Link-State                        |
| How Routes Are Discovered| Hardcoded by admin          | Neighbors exchange updates  | Routers flood LSAs → full topology map |
| Best Path Calculation    | Fixed next-hop              | DUAL (Bandwidth & Delay)    | Dijkstra SPF (Interface Cost)     |
| Administrative Distance  | 1 (highest priority)        | 90                          | 110                               |
| Failover / Redundancy    | Manual update required      | Fast (Feasible Successors)  | Fast (recalculates SPF tree)      |
| Vendor Compatibility     | Universal                   | Cisco proprietary           | Open standard                     |
```

---

## 7.3 Expanding the Topology — Additional Areas

### Adding R4 (Area 1) connected to R2

**On R2**
```bash
interface g0/2
ip address 192.168.1.1 255.255.255.252
no shutdown

router ospf 1
network 192.168.1.0 0.0.0.3 area 1
```

**Router 4 (R4)**
```bash
enable password cisco

interface loopback 0
ip address 4.4.4.4 255.255.255.255
no shutdown

interface g0/0
ip address 192.168.1.2 255.255.255.252
no shutdown

router ospf 1
network 4.4.4.4 0.0.0.0 area 1
network 192.168.1.0 0.0.0.3 area 1

line vty 0 4
no login
password cisco
transport input all
```

### Adding R5 (Area 3) connected to R1

**On R1**
```bash
interface g0/2
ip address 172.16.0.13 255.255.255.252
no shutdown

router ospf 1
network 172.16.0.12 0.0.0.3 area 3
```

**Router 5 (R5)**
```bash
interface loopback 0
ip address 5.5.5.5 255.255.255.255
no shutdown

interface g0/0
ip address 172.16.0.14 255.255.255.252
no shutdown

router ospf 1
network 172.16.0.12 0.0.0.3 area 3
network 5.5.5.5 0.0.0.0 area 3

line vty 0 4
no login
password cisco
transport input all
```

### Tip — Controlling Router-ID

```bash
router ospf <process-id>
router-id <IP_ADDRESS>
clear ip ospf process
```

Remember: the first IP that appears (or the highest loopback) becomes the Router-ID unless you manually set it.

---

## 7.4 Totally Stubby Area (Area 3)

**Command**
```bash
area 3 stub no-summary
```

Configured on the **ABR** (R1) to convert Area 3 into a **Totally Stubby Area**.

**Effect**
- Blocks individual Type 3 Summary LSAs (inter-area routes such as 2.2.2.2, 3.3.3.3, 4.4.4.4) from entering Area 3.
- Automatically injects a default route (`0.0.0.0/0`) into Area 3.
- Downstream routers (R5) use the default route to reach everything outside Area 3 via the ABR.

**If configured on both R1 and R5**
```bash
router ospf 1
area 3 stub no-summary
```

- R1 stops forwarding specific Type 3 LSAs into Area 3.
- R1 injects `0.0.0.0/0` via its interface toward R5 (172.16.0.13).
- R5 forwards any unknown destination to R1, which then routes it correctly.

**To remove the totally stubby configuration**
```bash
no area 3 stub no-summary
```

---

## 7.5 Interpreting `show ip ospf database` (Area 3)

### Router Link States (Type 1 LSAs)

```
Link ID         ADV Router      Age    Seq#       Checksum Link count
172.16.0.14     172.16.0.14     2833   0x80000013 0x00b43e 2
5.5.5.5         5.5.5.5         469    0x80000017 0x002834 2
1.1.1.1         1.1.1.1         469    0x80000012 0x00297d 1
```

- Type 1 LSAs describe each router’s directly connected links inside the area.
- `1.1.1.1` = R1’s LSA for its connection into Area 3.
- `5.5.5.5` = R5’s current Router-ID LSA.
- `172.16.0.14` = stale LSA (will age out at 3600 seconds).

### Net Link States (Type 2 LSAs)

```
Link ID         ADV Router      Age    Seq#       Checksum
172.16.0.14     5.5.5.5         308    0x80000008 0x00a5b8
```

- Represents the multi-access network segment.
- ADV Router = Designated Router (DR) elected on that link (R5 in this example).

### Summary Net Link States (Type 3 LSAs)

```
Link ID         ADV Router      Age    Seq#       Checksum
0.0.0.0         1.1.1.1         464    0x80000029 0x00250d
```

- Because of `no-summary`, individual inter-area routes are filtered.
- Only a default route (`0.0.0.0`) is injected by the ABR (R1).

---

## 7.6 Redistribution of Connected Routes (on R4)

```bash
interface loopback 10
ip address 64.64.64.64 255.255.255.255
no shutdown

router ospf 1
redistribute connected metric 5 metric-type 1 subnets
```

**Syntax breakdown**
```bash
redistribute connected metric <number> metric-type <1|2> subnets
```

| Keyword              | Meaning |
|----------------------|---------|
| `redistribute connected` | Import all connected networks that are **not** already covered by a `network` statement |
| `subnets`            | Allow VLSM / non-classful prefixes (critical on Cisco IOS) |
| `metric <cost>`      | Seed metric (default is usually 20 if omitted) |
| `metric-type 1` (E1) | Cost = seed metric + internal path cost (accumulates) |
| `metric-type 2` (E2) | Cost stays fixed at the seed metric (default behavior) |

### Resulting LSAs after redistribution

**Summary ASB Link States (Type 4)**
```
Link ID         ADV Router      Age    Seq#       Checksum
4.4.4.4         1.1.1.1         87     0x8000000a 0x009ca3
```
- Type 4 LSA tells other areas how to reach the ASBR (R4).
- Generated by the ABR (R1) into Area 3.

**Type-5 AS External Link States**
```
Link ID         ADV Router      Age    Seq#       Checksum Tag
64.64.64.64     4.4.4.4         92     0x80000001 0x00ab93 0
```
- Type 5 LSA advertises the external prefix (64.64.64.64) originated by the ASBR (R4).

---

## 7.7 Adding a Switch / LAN on R5

```bash
# On R5
interface g0/1
ip address 192.168.10.1 255.255.255.0
no shutdown

router ospf 1
network 192.168.10.0 0.0.0.255 area 3
```

---

# 8. OSPF Network Types Concept (3:03:45 – 3:10:00)

OSPF supports several network types that affect how Hellos are sent, whether a DR/BDR is elected, and how adjacencies form:

- **Broadcast** (default on Ethernet) — elects DR/BDR
- **Point-to-Point** — no DR/BDR, faster adjacency
- **Point-to-Multipoint**
- **Non-Broadcast** (NBMA)
- **Loopback**

Matching network types on both ends of a link is important for successful adjacency.

---

# 9. Quick Command Reference

```
| Command                                          | Purpose                                      |
| ------------------------------------------------ | -------------------------------------------- |
| `show ip ospf neighbor`                          | Verify OSPF adjacencies                      |
| `show ip ospf database`                          | View the full Link-State Database            |
| `show ip route ospf`                             | Display OSPF-learned routes                  |
| `router ospf <id>`                               | Enter OSPF process configuration             |
| `network <ip> <wildcard> area <id>`              | Enable OSPF on matching interfaces           |
| `router-id <ip>`                                 | Manually set Router-ID                       |
| `clear ip ospf process`                          | Restart OSPF process (applies new Router-ID) |
| `area <id> stub no-summary`                      | Make area Totally Stubby                     |
| `no area <id> stub no-summary`                   | Remove Totally Stubby configuration          |
| `redistribute connected metric <n> metric-type <1\|2> subnets` | Inject connected routes as external |
| `debug ip ospf adj / events / packet`            | Troubleshoot adjacency and LSA activity      |
```

---

# 10. Key Concepts to Remember

```text
Administrative Distance   → Lower = more preferred (Static 1 < EIGRP 90 < OSPF 110)
ABR                       → Router with interfaces in multiple areas (usually Area 0 + others)
Totally Stubby Area       → Blocks Type 3 + Type 5; injects default route only
Type 1 LSA                → Router LSA (describes a router’s links)
Type 2 LSA                → Network LSA (generated by DR on multi-access segments)
Type 3 LSA                → Summary LSA (inter-area routes, generated by ABR)
Type 4 LSA                → ASBR Summary (tells areas how to reach an ASBR)
Type 5 LSA                → External LSA (routes redistributed into OSPF)
Metric-Type 1 (E1)        → Accumulates internal cost + seed metric
Metric-Type 2 (E2)        → Fixed seed metric (default)
Router-ID                 → First / highest loopback, or manually configured
```

---

# 11. Lab Checklist

- [ ] Build the Area 0 triangle (R1–R2–R3) with correct /30 links
- [ ] Verify full OSPF adjacency and reachability of loopbacks
- [ ] Add R4 in Area 1 and R5 in Area 3
- [ ] Confirm inter-area routes appear via Type 3 LSAs
- [ ] Configure Area 3 as Totally Stubby (`area 3 stub no-summary`)
- [ ] Verify only a default route is present in Area 3
- [ ] Redistribute a connected network on R4 and observe Type 4 + Type 5 LSAs
- [ ] Manually set Router-IDs and clear the OSPF process when needed
- [ ] Interpret `show ip ospf database` sections correctly
- [ ] Understand the difference between metric-type 1 and metric-type 2

---

# 12. Key Takeaway

OSPF is a link-state protocol that builds a complete topology map using LSAs.  
Area design (especially the use of ABRs and special area types such as Totally Stubby) controls how much routing information is flooded into each area, saving memory and CPU on internal routers.  

Static routes have the highest preference (lowest AD), followed by EIGRP, then OSPF.  
Redistribution allows external or connected networks to be injected into OSPF as Type 5 LSAs, with the ABR generating Type 4 LSAs so other areas know how to reach the ASBR.

These concepts form the foundation for advanced OSPF design and troubleshooting in the **CCNA 200-301** curriculum.
```