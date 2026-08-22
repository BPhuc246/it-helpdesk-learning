# CCNA 200-301 — Switching Fundamentals, VLANs & STP Labs

> Notes from the CCNA 200-301 course.  
> Topics covered: Switch forwarding, Cisco switch CLI, VLANs, trunking, collision/broadcast domains, STP/RSTP, and related labs.

---

# 1. Introduction

## 1.1 Course Timeline

| Time              | Topic                                      |
|-------------------|--------------------------------------------|
| 05:30 – 19:30     | Sample Switch Forwarding                   |
| 21:00 – 43:15     | Lab 1 — Basic Switch Configuration         |
| 43:30 – 59:00     | Q&A #1                                     |
| 59:30 – 1:03:00   | Lab 2 — Switch Interface Settings (Part 1) |
| 1:19:16 – 1:28:00 | Collision Domain & Broadcast Domain        |
| 1:30:15 – 1:59:30 | Lab 2 — Switch Interface Settings (Part 2) |
| 1:42:30 – 1:53:40 | VLANs Concept                              |
| —                 | Lab 4 — VLAN Configuration & Trunking      |
| —                 | STP and RSTP                               |
| —                 | Additional Q&A                             |

---

# 2. Switch Forwarding Behavior

Switches take incoming traffic and flood it out every port **except** the port on which the frame arrived (when the destination MAC is unknown).

```text
Frame arrives on Port 1
          ↓
Switch does not know destination MAC
          ↓
Floods frame out all ports except Port 1
```

Once the switch learns the source MAC address and the associated port, subsequent frames destined for that MAC are forwarded only to the correct port.

---

# 3. Lab 1 — Basic Switch Configuration

## 3.1 Useful Show Commands (Enable Mode)

| Command                          | Purpose                                                                 |
|----------------------------------|-------------------------------------------------------------------------|
| `show interface status`          | High-level summary of all physical interfaces (state, VLAN, speed, duplex, type) |
| `show vlan` / `show vlan brief`  | Displays the VLAN database                                              |
| `show mac address-table dynamic` | Shows all dynamically learned MAC addresses                             |
| `show running-config`            | Displays the full running configuration (including passwords)           |

## 3.2 Global Configuration Mode Commands

```bash
enable password <password>          # Sets a clear-text enable password
enable secret <password>            # Sets a hashed enable password (preferred)
service password-encryption         # Encrypts clear-text passwords in the config
no enable secret                    # Removes the enable secret
no ip domain-lookup                 # Disables DNS lookup on mistyped commands
interface vlan <vlan-id>            # Creates / enters SVI configuration
line vty 0 4                        # Enters VTY line configuration (0–4 sessions)
```

**Notes on passwords**

- `enable secret` always takes precedence over `enable password`.
- `enable secret` stores a cryptographic hash (MD5/Type 5 by default; modern IOS can use Type 8/SHA-256).
- Both passwords may appear in `show running-config` until the old one is removed.

## 3.3 Line Configuration Mode

```bash
password <password>                 # Sets the VTY login password
transport input all                 # Allows both Telnet and SSH
```

## 3.4 Interface / SVI Configuration

```bash
ip address dhcp                     # Obtains IP address via DHCP
no shutdown                         # Enables the interface
```

**When a DHCP-assigned address may change**

- Device is powered off or disconnected longer than the lease time.
- Another device claims the address while the switch is offline.
- No MAC reservation exists on the DHCP server.

## 3.5 Lab Steps Summary

1. Create a new switch.
2. Enter privileged EXEC mode (`enable`).
3. Set an enable password:

   ```bash
   configure terminal
   enable password <password>
   ```

4. (Recommended) Set an encrypted secret:

   ```bash
   enable secret <password>
   ```

5. Create two SVIs and assign IP addresses:

   ```bash
   interface vlan 1
   ip address 192.168.1.1 255.255.255.0
   no shutdown

   interface vlan 2
   ip address 192.168.2.1 255.255.255.0
   no shutdown
   ```

6. Verify with:

   ```bash
   show ip interface brief
   ```

---

# 4. Q&A #1 (43:30 – 59:00)

**Q1. Why is Telnet still available even though it is insecure?**  
Some legacy environments still use it. In modern networks, SSH is the only recommended remote-access protocol.

**Q2. VLAN vs Subnet — when to use each?**

| Aspect          | VLAN (Layer 2)                                      | Subnet (Layer 3)                                      |
|-----------------|-----------------------------------------------------|-------------------------------------------------------|
| Primary purpose | Segment broadcast domains on a switch               | Segment IP addressing and routing                     |
| Use cases       | Limit broadcast storms, logical department separation, trunking multiple networks over one link, port security | Address allocation, route summarization, ACLs/firewalls, WAN/cloud connectivity, QoS |
| Limitation      | Typically keep a single VLAN under ~250–500 hosts   | Can span geographic locations                         |

**Q3. How does a device learn a destination MAC address?**  
Using ARP.

**Q4. Should DHCP be used in this lab?**  
Not recommended for critical infrastructure devices; static addressing is preferred.

**Q5. Do I need a managed switch for a home PC + two laptops?**  
Only if you require isolation, traffic control, or advanced features. A simple unmanaged switch or even a home router is usually sufficient for basic connectivity.

**Q6. Can I connect to my neighbor’s network by just configuring my router?**  
Yes — by ensuring proper IP addressing and routing between the two routers.

**Q13. Do all switches route IP addresses during ARP?**  
No. Standard Layer-2 switches only forward frames based on MAC addresses.

**Q14. What is the maximum number of devices in one VLAN?**  
Theoretically very large, but practical limits (broadcast domain size, performance) usually keep it in the low hundreds.

**Q16. Where does ARP sit in the OSI model?**  
Between Layer 2 and Layer 3 (it maps Layer-3 IP addresses to Layer-2 MAC addresses).

---

# 5. Lab 2 — Interface Speed & Duplex

## 5.1 Common Interface Commands

```bash
interface <interface>
no negotiation auto          # Disables auto-negotiation
duplex full                  # Forces full-duplex
speed 100                    # Forces 100 Mbps
```

**Notes**

- Auto-negotiation is enabled by default on most switches.
- `no negotiation auto` appears in `show running-config` primarily on SFP/fiber interfaces; it may not be visible or available on copper RJ-45 ports.

## 5.2 Connecting Two Switches

1. Create SW1 and SW2.
2. Connect SW1 → SW2 (order matters for observation).
3. Observe interface status with `show ip interface brief`.
4. Match speed and duplex on both sides.

### Interface Status Codes

| Status                  | Protocol | Interface Status | Typical Root Cause                                      |
|-------------------------|----------|------------------|---------------------------------------------------------|
| administratively down   | down     | disabled         | `shutdown` command configured                           |
| down                    | down     | notconnect       | No cable, bad cable, speed mismatch, neighbor powered off |
| up                      | down     | notconnect       | Unusual on LAN switch ports                             |
| down                    | down     | err-disabled     | Port security or error-disable condition                |
| up                      | up       | connected        | Interface is fully operational                          |

---

# 6. Collision Domains & Broadcast Domains

| Term              | Definition                                                                 | Typical Device          |
|-------------------|----------------------------------------------------------------------------|-------------------------|
| Collision Domain  | Physical segment where simultaneous transmissions can collide              | Hub                     |
| Broadcast Domain  | Logical area where a broadcast frame is received by all devices            | Switch (per VLAN) + Router |

Modern switches create a separate collision domain on every port.  
Routers (and Layer-3 switches) separate broadcast domains.

---

# 7. VLANs

A **VLAN (Virtual Local Area Network)** is a Layer-2 construct that logically segments a physical switch into multiple virtual switches, each forming its own broadcast domain.

## 7.1 Key VLAN Commands

```bash
vlan <vlan-id>                          # Creates / enters VLAN configuration (1–4094)
show vlan / show vlan brief             # Displays VLAN database
interface range <start> - <end>         # Selects a range of interfaces
switchport mode access                  # Sets port to access mode
switchport access vlan <vlan-id>        # Assigns the port to a VLAN
switchport mode dynamic desirable       # Actively negotiates trunking (DTP)
```

### Trunking

A **trunk** is a single physical link that carries traffic for multiple VLANs using 802.1Q tagging.

```text
Switch A ──── Trunk (802.1Q) ──── Switch B
              (multiple VLANs)
```

- **Trunk** = the capability to carry multiple VLANs on one link.  
- **VLAN tagging** = the mechanism that keeps the VLANs logically separated on that link.

## 7.2 Lab 4 — VLAN Configuration Steps

1. Create VLANs.
2. Assign access ports:

   ```bash
   interface range g0/1 - 2
   switchport mode access
   switchport access vlan 10
   ```

3. Optionally configure dynamic desirable mode and verify:

   ```bash
   show interface <interface> switchport
   ```

---

# 8. Additional VLAN Q&A

**Q1. Can the same VLAN exist on two switches?**  
Yes — this is the normal way to extend a VLAN across a network (via trunks).

**Q2. Layer 2 vs Layer 3 security?**  

- Layer 2: port security, QoS, storm control.  
- Layer 3: ACLs, firewalls, intrusion detection.

**Q3. Is 802.1Q trunking the same as VLAN tagging?**  
Related but not identical. Trunking is the link mode; tagging is the method used to keep VLANs separate on that link.

**Q6. What does VTP do?**  
VLAN Trunking Protocol propagates VLAN information between switches so they stay synchronized.

**Q8. How do hosts on different VLANs communicate?**  
They require a Layer-3 device (router or Layer-3 switch) — inter-VLAN routing.

**Q9. Router vs multilayer switch?**  
Depends on the number of devices and required features.

**Q11. Alternatives to trunking in real/cloud environments?**  
No direct equivalent for carrying multiple L2 domains over a single link in the same way; cloud environments use different segmentation models.

**Q12. Can DNS and DHCP run on a domain controller?**  
Yes.

---

# 9. STP and RSTP

**Spanning Tree Protocol (STP)** and **Rapid Spanning Tree Protocol (RSTP)** prevent Layer-2 loops in networks that have redundant Ethernet links.

### The Problem

Ethernet frames do **not** have a TTL field. Redundant links without loop prevention cause:

- Broadcast storms
- MAC address table instability (MAC flapping)

### How STP Works

1. **Elect the Root Bridge** — switch with the lowest Bridge ID (Priority + MAC).
2. **Select Root Ports (RP)** — on every non-root switch, the port with the lowest cost to the Root Bridge.
3. **Select Designated Ports (DP)** — one port per network segment that offers the best path to the Root.
4. **Block remaining ports** — any port that is neither RP nor DP is placed in Blocking state, breaking the loop while keeping the path available as a backup.

```text
          Root Bridge
         /           \
       RP             RP
      /                 \
   Switch A ----------- Switch B
         (one link blocked)
```

---

# 10. STP Q&A

**Q1. Why do we need STP if we already have TTL?**  
TTL is a Layer-3 mechanism. STP operates at Layer 2 where Ethernet frames have no hop-count limit.

---

# 11. Quick Command Reference

| Command                              | Purpose                                      |
|--------------------------------------|----------------------------------------------|
| `show interface status`              | Interface summary                            |
| `show vlan brief`                    | VLAN database                                |
| `show mac address-table dynamic`     | Dynamic MAC table                            |
| `enable password / enable secret`    | Set enable passwords                         |
| `service password-encryption`        | Encrypt clear-text passwords                 |
| `line vty 0 4`                       | Configure remote access lines                |
| `interface vlan <id>`                | Configure SVI                                |
| `no ip domain-lookup`                | Disable DNS lookup                           |
| `switchport mode access`             | Set access mode                              |
| `switchport access vlan <id>`        | Assign VLAN                                  |
| `switchport mode dynamic desirable`  | Negotiate trunking                           |
| `no negotiation auto`                | Disable auto-negotiation                     |
| `duplex full` / `speed 100`          | Force duplex and speed                       |
| `show ip interface brief`            | Interface IP/status summary                  |

---

# 12. Key Concepts to Remember

```text
Switch → primarily Layer 2 (MAC-based forwarding)
VLAN   → Layer 2 broadcast domain segmentation
Subnet → Layer 3 IP addressing segmentation
Trunk  → carries multiple VLANs (802.1Q tagging)
STP    → prevents Layer 2 loops
```

**Addressing reminder**

- Layer 2 → MAC address  
- Layer 3 → IP address  

**Important protocols**

- ARP → resolves IP to MAC  
- STP / RSTP → loop prevention  
- DTP → Dynamic Trunking Protocol  
- VTP → VLAN information propagation  

---

# 13. Lab Checklist

- [ ] Enter Cisco IOS CLI and switch modes  
- [ ] Configure enable password and enable secret  
- [ ] Secure VTY lines  
- [ ] Create and assign VLANs  
- [ ] Configure access ports  
- [ ] Configure and verify trunking  
- [ ] Match speed/duplex on connected switches  
- [ ] Interpret interface status codes  
- [ ] Understand collision vs broadcast domains  
- [ ] Explain basic STP operation  

---

# 14. Key Takeaway

Switches flood unknown unicast traffic and learn MAC addresses dynamically.  
VLANs allow logical segmentation of a single physical infrastructure.  
Trunks carry multiple VLANs between switches.  
STP keeps redundant Layer-2 topologies free of loops.

These concepts form the foundation for the rest of the **CCNA 200-301** switching and inter-VLAN routing material.
