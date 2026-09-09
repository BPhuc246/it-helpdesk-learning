# CCNA 200-301 — TCP/IP, UDP, Flow Control & Access Control Lists (ACLs)

> Notes from the CCNA 200-301 course.  
> Topics covered: TCP vs UDP, flow control, Access Control Lists (standard, extended, and named), ACL lab configuration, and related concepts.

---

# 1. Introduction

## 1.1 Course Timeline

```
| Time              | Topic                                      |
| ----------------- | ------------------------------------------ |
| 05:30 – 20:30     | TCP/IP, UDP, and Flow Control              |
| 21:00 – 31:00     | Questions 1 (from chat)                    |
| 32:00 – 50:00     | Access Control Lists                       |
| 53:00 – 1:16:30   | Lab — Basic ACL Configuration              |
| 1:16:45 – 1:26:30 | Questions 2 (from chat)                    |
| 1:29:50 – 1:33:00 | Continue Lab — Applying Standard ACL       |
| 1:36:25 – 2:08:00 | Extended Access Lists                      |
| 2:18:30 – 2:42:00 | DDoS Attack Overview                       |
```

---

# 2. TCP/IP, UDP, and Flow Control (05:30 – 20:30)

**TCP (Transmission Control Protocol)**
- Connection-oriented
- Reliable (acknowledgements, retransmissions)
- Ordered delivery
- Flow control and congestion control
- Used by applications that require reliability (HTTP, HTTPS, SSH, FTP, etc.)

**UDP (User Datagram Protocol)**
- Connectionless
- Unreliable (no acknowledgements)
- No ordered delivery guarantee
- Lower overhead → faster
- Used by applications that prefer speed or can tolerate loss (DNS, DHCP, VoIP, video streaming, etc.)

**Flow Control**
- Mechanism that prevents a fast sender from overwhelming a slower receiver
- TCP uses windowing (sliding window) to manage how much data can be in transit

---

# 3. Questions 1 (21:00 – 31:00)

(Questions and answers from the live chat during this segment.)

---

# 4. Access Control Lists (32:00 – 50:00)

**What is an Access Control List (ACL)?**  
An ACL is a sequential list of permit or deny statements that control which traffic is allowed to enter or leave an interface.  
In simple terms: **it keeps data from going to places you don’t want it to go**.

Key characteristics:
- Processed **top-down** (first match wins)
- Implicit **deny any** at the end of every ACL
- Can be applied **inbound** or **outbound** on an interface
- Standard ACLs filter based on **source IP only**
- Extended ACLs filter based on **source, destination, protocol, and port**

---

# 5. Lab — Basic ACL Configuration (53:00 – 1:16:30)

## 5.1 Base Router Configuration

**Router 1 (R1)**
```bash
enable password cisco

line vty 0 4
no login
password cisco
transport input all

interface loopback 0
ip address 1.1.1.1 255.255.255.255
no shutdown

interface g0/0
ip address 192.168.1.1 255.255.255.252
no shutdown

router ospf 1
network 1.1.1.1 0.0.0.0 area 0
network 192.168.1.0 0.0.0.3 area 0
```

**Router 2 (R2)**
```bash
enable password cisco
service password-encryption

interface loopback 0
ip address 2.2.2.2 255.255.255.255
no shutdown

interface g0/0
ip address 192.168.1.2 255.255.255.252
no shutdown

line vty 0 4
no login
password cisco
transport input all

router ospf 1
network 2.2.2.2 0.0.0.0 area 0
network 192.168.1.0 0.0.0.3 area 0
```

## 5.2 Creating a Standard Access List

```bash
# On R1
access-list 1 permit 1.1.1.1
access-list 1 permit 192.168.1.1
access-list 1 permit 2.2.2.2
```

Verify with:

```bash
show ip access-lists 1
# or
show access-lists 1
```

---

# 6. Questions 2 (1:16:45 – 1:26:30)

**Q1. Is a wildcard mask also configured for loopback addresses?**  
Yes. A wildcard mask can match a single host (`0.0.0.0`), a range of addresses, or all addresses (`255.255.255.255`).

**Q4. Can ACLs be stateful?**  
No. Routers are not true firewalls. Classic ACLs are **stateless** — they evaluate each packet individually without tracking connection state.

**Q5. Do ACLs run on VMs / in the cloud?**  
Yes. The same ACL concepts apply to virtual routers and cloud networking constructs.

---

# 7. Continue Lab — Applying the Standard ACL (1:29:50 – 1:33:00)

```bash
# On R1
no ip access-list standard 1          # remove previous version if needed

access-list 1 remark allows Phuc ospf to reach nonie
access-list 1 permit 1.1.1.1
access-list 1 permit 2.2.2.2
access-list 1 permit 192.168.1.1
access-list 1 permit 192.168.1.2
access-list 1 permit 224.0.0.5        # OSPF multicast (AllSPFRouters)
access-list 1 permit 224.0.0.6        # OSPF multicast (AllDRouters)

interface g0/0
ip access-group 1 in
```

**Notes**
- `remark` is only a comment — it does not affect traffic.
- OSPF uses multicast addresses `224.0.0.5` and `224.0.0.6`; these must usually be permitted for OSPF adjacency to stay up when an ACL is applied inbound.

---

# 8. Extended Access Lists (1:36:25 – 2:08:00)

Extended ACLs (numbers 100–199 or 2000–2699) can match on protocol, source, destination, and optionally ports.

## 8.1 Numbered Extended ACL Example

```bash
# On R1
access-list 101 permit ospf any any
access-list 101 permit icmp 1.1.1.1 0.0.0.0 any
access-list 101 permit icmp 192.168.1.1 0.0.0.0 any
access-list 101 permit icmp 192.168.1.2 0.0.0.0 any
access-list 101 permit ip any any

interface g0/0
ip access-group 101 in
```

Verify:

```bash
show access-lists
# or
show ip access-lists 101
```

You should now be able to ping `2.2.2.2`.

### Adding Remarks and Specific Denies

```bash
access-list 101 remark allowretreat
access-list 101 permit ospf any any
access-list 101 deny tcp 172.16.3.11 0.0.0.255 eq www 2.2.2.2 0.0.0.0

show access-lists

# To remove the entire ACL
no ip access-list extended 101
```

## 8.2 Named Extended ACL (Preferred Modern Style)

```bash
ip access-list extended iamthehuman
 permit ospf any any
 permit ip 1.1.1.1 0.0.0.0 2.2.2.2 0.0.0.0    # allow traffic from 1.1.1.1 to 2.2.2.2
 deny ip 172.16.1.0 0.0.0.255 any
 deny ip 172.16.4.0 0.0.0.255 any
 deny ip 172.16.8.0 0.0.0.255 any
 deny ip 172.16.12.0 0.0.0.255 any
 permit ip any any                                # final permit (otherwise implicit deny)

interface g0/0
ip access-group iamthehuman in
```

**Key points**
- Named ACLs are easier to edit and document.
- Entries are processed in order; the first match is used.
- Always remember the **implicit deny any** at the end.
- Place more specific statements before general ones.

---

# 9. DDoS Attack Overview (2:18:30 – 2:42:00)

A **Distributed Denial of Service (DDoS)** attack attempts to overwhelm a target with traffic from many sources so that legitimate users cannot reach the service.

ACLs can provide basic filtering (blocking known bad sources or rate-limiting certain traffic), but they are **not** a complete DDoS mitigation solution. Dedicated DDoS protection services, firewalls, and traffic scrubbing are normally required for large-scale attacks.

---

# 10. Quick Command Reference

```
| Command                                              | Purpose                                      |
| ---------------------------------------------------- | -------------------------------------------- |
| `access-list <1-99> permit|deny <source> [wildcard]` | Create standard ACL entry                    |
| `access-list <100-199> permit|deny <protocol> ...`   | Create extended ACL entry                    |
| `ip access-list standard|extended <name>`            | Create/enter named ACL                       |
| `access-list <num> remark <text>`                    | Add a comment (does not affect traffic)      |
| `ip access-group <num|name> in|out`                  | Apply ACL to an interface                    |
| `show ip access-lists [num|name]`                    | Display ACL configuration and hit counts     |
| `show access-lists`                                  | Same as above                                |
| `no ip access-list standard|extended <num|name>`     | Delete an entire ACL                         |
| `no access-list <num>`                               | Alternative way to remove numbered ACL       |
```

---

# 11. Key Concepts to Remember

```text
Standard ACL          → Matches source IP only (numbers 1–99, 1300–1999)
Extended ACL          → Matches protocol, source, destination, ports (100–199, 2000–2699)
Named ACL             → Same capability as numbered, but easier to manage
Implicit deny         → Every ACL ends with an invisible “deny any”
First-match wins      → Order of statements is critical
Inbound vs Outbound   → Direction relative to the router interface
Wildcard mask         → 0 = must match, 255 = ignore
OSPF multicast        → 224.0.0.5 and 224.0.0.6 often need to be permitted
Stateful vs Stateless → Classic ACLs are stateless
```

**Best practices**
- Place extended ACLs as close to the **source** as possible.
- Place standard ACLs as close to the **destination** as possible.
- Always document ACLs with `remark` statements.
- Test connectivity after applying an ACL (especially routing protocol traffic).

---

# 12. Lab Checklist

- [ ] Configure basic OSPF connectivity between R1 and R2
- [ ] Create and verify a standard ACL
- [ ] Apply the ACL inbound on an interface
- [ ] Permit OSPF multicast addresses so adjacency stays up
- [ ] Create an extended numbered ACL
- [ ] Create a named extended ACL
- [ ] Use `remark` for documentation
- [ ] Verify with `show access-lists` / `show ip access-lists`
- [ ] Understand the implicit deny and first-match behavior
- [ ] Remove or modify ACLs cleanly

---

# 13. Key Takeaway

TCP provides reliable, ordered, flow-controlled delivery; UDP is lightweight and best-effort.  
Access Control Lists are the fundamental tool for filtering traffic on Cisco routers.  

Standard ACLs are simple (source only); extended and named ACLs give precise control over protocol, source, destination, and ports.  
Always remember the implicit deny, the importance of statement order, and the need to permit control-plane traffic (such as OSPF multicasts) when an ACL is applied.

These concepts form the foundation for security and traffic control topics in the **CCNA 200-301** curriculum.
```