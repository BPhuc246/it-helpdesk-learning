# CCNA 200-301 — Networking Fundamentals & Routing Labs

> Notes from the CCNA 200-301 course.
> Topics covered: OSI/TCP-IP models, encapsulation, LAN, Ethernet, TCP/UDP, ARP, Cisco IOS, interfaces, OSPF, routing tables, and Cisco device memory.

---

# 1. Introduction

## 1.1 Course Timeline

| Time              | Topic                             |
| ----------------- | --------------------------------- |
| 09:30 – 16:48     | TCP Transport Layer & OSI Model   |
| 17:00 – 21:00     | Routing Concept Example           |
| 21:10 – 23:30     | Data Encapsulation                |
| 24:00 – 27:00     | OSI Model                         |
| 27:30 – 37:00     | Q&A #1                            |
| 38:40 – 42:50     | Local Area Network (LAN)          |
| 43:00 – 54:00     | Freezing Problem — Skip           |
| 54:10 – 1:05:00   | Q&A #2                            |
| 1:05:10 – 1:21:30 | Cable Types & Networking Concepts |
| 1:25:30 – 1:38:50 | Basic Cisco CLI & First Lab       |
| 1:39:30 – 1:44:50 | Q&A #3                            |
| 1:44:00 – 1:45:00 | Why Configuration Mistakes Matter |
| 1:45:15 – 1:48:00 | Internet Access at Work           |
| 1:50:30 – 2:03:00 | Router & Encapsulation            |
| 2:03:15 – 2:13:30 | Q&A #4                            |
| 2:14:00 – 2:17:00 | Terminal Settings                 |
| 2:18:00 – 2:24:00 | Basic Cisco Commands              |
| 2:27:00 – 2:51:00 | Connecting Two Routers & OSPF     |
| 2:51:10 – 2:53:30 | Cisco Memory Types                |
| 2:54:00 – 3:33:00 | Q&A #5                            |

---

# 2. Data Encapsulation

Data encapsulation is the process of adding protocol information to data as it moves down through the networking layers.

## 2.1 Five Steps of Data Encapsulation

| OSI Layer | Layer       | Data Added            | PDU     |
| --------- | ----------- | --------------------- | ------- |
| Layer 5   | Application | Data                  | Data    |
| Layer 4   | Transport   | TCP header            | Segment |
| Layer 3   | Network     | IP header             | Packet  |
| Layer 2   | Data Link   | Link Header + Trailer | Frame   |
| Layer 1   | Physical    | Bits                  | Bits    |

Conceptually:

```text
Layer 5: Application
        Data

Layer 4: Transport
        TCP | Data
        ↓
        Segment

Layer 3: Network
        IP | TCP | Data
        ↓
        Packet

Layer 2: Data Link
        LH | IP | TCP | Data | LT
        ↓
        Frame

Layer 1: Physical
        101010101010...
        ↓
        Bits
```

Where:

* **LH** = Link Header
* **LT** = Link Trailer

### Important terminology

```text
Application Layer → Data
Transport Layer   → Segment
Network Layer     → Packet
Data Link Layer   → Frame
Physical Layer    → Bits
```

---

# 3. OSI Model

The OSI model contains seven layers.

| Layer | Name         | Main Purpose                                   |
| ----- | ------------ | ---------------------------------------------- |
| 7     | Application  | Network services used by applications          |
| 6     | Presentation | Data representation, encoding, encryption      |
| 5     | Session      | Establishes and manages communication sessions |
| 4     | Transport    | End-to-end delivery                            |
| 3     | Network      | IP addressing and routing                      |
| 2     | Data Link    | Frames, MAC addresses, local delivery          |
| 1     | Physical     | Electrical/radio signals and bits              |

## 3.1 Application Layer

Provides network functionality used by applications.

Examples:

* HTTP
* DNS
* DHCP
* FTP
* SMTP
* BGP

> BGP is considered an application-layer protocol even though it uses TCP port **179**.

BGP is responsible for exchanging routing information and making high-level routing policy decisions between autonomous systems.

---

## 3.2 Presentation Layer

Responsible for how data is represented.

Examples include:

* ASCII
* Binary encoding
* JPEG
* Encryption
* Compression

---

## 3.3 Session Layer

Responsible for establishing, managing, and terminating communication sessions.

It can also manage the communication between two endpoints, including bidirectional communication.

> In modern TCP/IP networking, the OSI Session and Presentation layers are commonly handled as part of the Application layer.

---

## 3.4 Transport Layer

Provides end-to-end communication between applications.

Two important protocols:

### TCP

TCP provides reliable delivery.

Conceptually:

```text
Sender                     Receiver

  1  -------------------->
     "Did you receive it?"

     <--------------------
              ACK

  2  -------------------->
```

TCP can:

* Detect lost data
* Retransmit data
* Maintain ordering
* Use acknowledgements
* Control the flow of data

### UDP

UDP is connectionless and provides minimal delivery guarantees.

```text
Sender                     Receiver

       ------------------->
             Data
```

UDP generally does not wait for an acknowledgement before continuing.

This makes UDP useful when low latency is more important than guaranteed delivery.

Examples:

* Streaming
* VoIP
* DNS
* Online gaming

---

# 4. Important Networking Terms

## 4.1 Packet vs Frame

A **packet** belongs to Layer 3.

A **frame** belongs to Layer 2.

```text
Layer 3 → Packet
Layer 2 → Frame
```

There is no such thing as a:

```text
TCP frame
UDP frame
```

TCP and UDP operate at Layer 4.

---

# 5. TCP vs UDP

| Feature        | TCP                       | UDP                      |
| -------------- | ------------------------- | ------------------------ |
| Connection     | Connection-oriented       | Connectionless           |
| Reliability    | Reliable                  | Best effort              |
| Retransmission | Yes                       | No                       |
| Ordering       | Yes                       | No guarantee             |
| Overhead       | Higher                    | Lower                    |
| Speed/latency  | Generally higher overhead | Generally lower overhead |

### Simple analogy

**TCP:**

> "I sent the package. Did you receive it?"

**UDP:**

> "I'm sending the package. Hopefully you receive it."

---

# 6. Routing

Routing is the process of determining where packets should be sent in order to reach their destination.

A router examines the destination IP address and uses its routing table to determine the next hop.

Conceptually:

```text
PC1
 |
 v
R1 -------- R2 -------- R3
                         |
                         v
                        PC2
```

A packet does not necessarily travel directly from the source to the destination.

Instead, routers forward it hop-by-hop.

---

# 7. ARP — Address Resolution Protocol

ARP is used to map an IPv4 address to a MAC address on a local network.

For example:

```text
IP: 192.168.1.20
       ↓
     ARP
       ↓
MAC: AA:BB:CC:DD:EE:FF
```

A device can ask:

```text
"Who has 192.168.1.20?"
```

The device with that IP address responds with its MAC address.

## Important

ARP is often described as operating between Layer 2 and Layer 3 because it resolves a Layer 3 IPv4 address to a Layer 2 MAC address.

---

# 8. How Does a Sender Know the Receiver's MAC Address?

If the destination is on the **same subnet**, the sender uses ARP to discover the destination MAC address.

If the destination is on a **different subnet**, the sender normally uses ARP to discover the MAC address of its **default gateway**, not the final destination.

Example:

```text
PC1
 |
 | Destination is outside local subnet
 v
Default Gateway
 |
 v
Router
 |
 v
Remote Network
```

The Ethernet frame is addressed to the next-hop device.

---

# 9. Ethernet vs HDLC

The Layer 2 protocol depends on the type of interface/link being used.

### Ethernet

Ethernet uses Ethernet frames containing MAC addresses.

```text
Ethernet Header
      |
      v
[Src MAC][Dst MAC][IP][TCP][Data][FCS]
```

### HDLC

HDLC is a Layer 2 protocol commonly used with serial links.

```text
Router ---- Serial Link ---- Router
              HDLC
```

Therefore:

> If two routers are connected through Ethernet ports, Ethernet framing is used.

> If they are connected through serial interfaces using HDLC, HDLC framing is used.

---

# 10. LAN — Local Area Network

A **LAN (Local Area Network)** connects devices within a limited geographical area.

Examples:

* Home network
* Office network
* School network
* Data center

Typical LAN devices:

```text
PC ─┐
PC ─┤
PC ─┤── Switch ── Router ── Internet
PC ─┘
```

---

# 11. Switch

A switch is a networking device used to connect devices within a LAN.

Example:

```text
        PC1
         |
        PC2
         |
PC3 --- Switch --- Router
         |
        PC4
```

A switch primarily operates at **Layer 2** and forwards Ethernet frames based on MAC addresses.

---

# 12. Router

A router operates primarily at Layer 3.

Its main job is to connect different networks and forward packets based on IP addresses.

Example:

```text
LAN 1
 |
 v
Router
 |
 v
LAN 2
```

---

# 13. Layer 3 Switch vs Router

A Layer 3 switch can perform routing functions, especially routing between VLANs inside a LAN.

A traditional router provides more comprehensive routing capabilities and is commonly used for communication between different networks, WANs, and other network segments.

### Simple distinction

```text
Layer 2 Switch
    ↓
MAC-based forwarding

Layer 3 Switch
    ↓
MAC + IP-based forwarding

Router
    ↓
IP routing between networks
```

---

# 14. STP — Spanning Tree Protocol

STP is used to prevent Layer 2 switching loops.

Example:

```text
       Switch 1
       /      \
      /        \
 Switch 2 ---- Switch 3
```

Without STP, redundant Layer 2 paths can create loops.

### Important

STP is a Layer 2 technology.

Routers do not normally use STP because routers operate at Layer 3 and do not have the same Layer 2 bridging-loop problem.

---

# 15. Wireless Networking

IEEE 802.11ax is commonly known as **Wi-Fi 6**.

Wireless networks can have characteristics such as:

* Interference
* Variable latency
* Packet loss
* Signal degradation
* Shared medium

Therefore, network design should consider whether a connection requires the consistency of a wired Ethernet connection.

---

# 16. Throughput vs Data Rate / Bit Rate

These terms are related but not identical.

### Data rate / Bit rate

The theoretical rate at which bits can be transmitted.

Example:

```text
100 Mbps
```

### Throughput

The actual amount of useful data successfully transferred.

Example:

```text
Link capacity: 100 Mbps
Actual throughput: 80 Mbps
```

The difference can be caused by:

* Protocol overhead
* Congestion
* Packet loss
* Network conditions
* Hardware limitations
* Application behavior

---

# 17. TTL — Time To Live

TTL is a value in an IP packet header that limits how many Layer 3 hops a packet can make.

Each router decreases the TTL.

Conceptually:

```text
TTL = 64

R1 → TTL 63
R2 → TTL 62
R3 → TTL 61
```

If TTL reaches zero, the packet is discarded.

This prevents packets from circulating forever because of routing loops.

---

# 18. Cisco Packet Tracer

Cisco Packet Tracer is used to build and simulate Cisco networking environments.

### Download

https://www.netacad.com/cisco-packet-tracer

### Installation Guide

https://www.youtube.com/watch?v=7sWn9MAuJFM

---

# 19. First Lab — Basic Cisco CLI

## 19.1 Enter the CLI

Select a router:

```text
Router → CLI
```

Wait for initialization.

When asked:

```text
Would you like to enter the initial configuration dialog?
```

Choose:

```text
no
```

Press Enter.

---

# 20. Cisco IOS Modes

Cisco IOS uses different configuration modes.

```text
Router>
```

User EXEC mode.

```text
Router#
```

Privileged EXEC mode.

Enter privileged mode:

```text
enable
```

Configuration mode:

```text
Router(config)#
```

Enter with:

```text
configure terminal
```

or:

```text
config t
```

---

# 21. Basic Cisco Commands

## `show running-config`

Displays the current running configuration.

```bash
show running-config
```

Short form:

```bash
show run
```

---

## `configure terminal`

Enters global configuration mode.

```bash
config t
```

---

## `hostname`

Changes the device hostname.

```bash
hostname R1
```

The prompt changes from:

```text
Router#
```

to:

```text
R1#
```

---

## `interface`

Enters an interface configuration mode.

Example:

```bash
interface g0/0
```

Short form:

```bash
int g0/0
```

---

## `interface loopback`

Creates or enters a loopback interface.

```bash
interface loopback 0
```

Short form:

```bash
int lo0
```

---

## `ip address`

Assigns an IP address and subnet mask.

```bash
ip address 172.16.1.1 255.255.255.252
```

Short form:

```bash
ip addr 172.16.1.1 255.255.255.252
```

---

## `no shutdown`

Enables an administratively disabled interface.

```bash
no shutdown
```

Short form:

```bash
no shut
```

---

## `exit`

Returns to the previous configuration mode.

```bash
exit
```

---

## `show ip interface brief`

Displays a summary of interfaces and their IP addresses.

```bash
show ip interface brief
```

Example:

```text
Interface              IP-Address      Status       Protocol
GigabitEthernet0/0     172.16.1.1      up           up
Loopback0              1.1.1.1         up           up
```

---

# 22. Understanding `administratively down`

If you run:

```bash
show ip interface brief
```

and see:

```text
Status              Protocol
administratively down
```

the interface has been manually disabled.

Check the configuration:

```bash
show running-config
```

You may see:

```text
interface GigabitEthernet0/0
 shutdown
```

Enable it:

```bash
configure terminal
interface g0/0
no shutdown
```

---

# 23. `no keepalive`

The command:

```bash
no keepalive
```

disables keepalive messages on an interface.

It can be useful in certain lab or interoperability scenarios.

Possible use cases include:

* Testing
* Connecting to non-Cisco equipment
* Some tunnel configurations
* Lab environments

> Use this command only when you understand why the keepalive mechanism needs to be disabled.

---

# 24. Connecting a Router and Switch

In Packet Tracer:

1. Select **Connections**.
2. Choose **Copper Straight-Through**.
3. Connect the router to the switch.
4. Configure the router interface.
5. Enable the interface with `no shutdown`.

Example:

```bash
enable
configure terminal
interface g0/0
ip address 172.16.1.1 255.255.255.252
no shutdown
```

Check:

```bash
show ip interface brief
```

You want:

```text
Status       Protocol
up           up
```

---

# 25. Link Status vs Line Protocol

When running:

```bash
show ip interface brief
```

you may see two status fields.

```text
Status       Protocol
```

They represent different things.

### Status

Primarily reflects the physical Layer 1 state.

For example:

```text
up
down
```

### Protocol

Reflects whether the Layer 2 line protocol is operational.

Example:

```text
up
down
```

Therefore:

```text
up / up
```

generally means the interface is physically working and the line protocol is operational.

---

# 26. Cisco Configuration via SSH

For remote device management, SSH is preferred over insecure protocols such as Telnet.

```text
Administrator
      |
      | SSH
      v
   Router
```

SSH provides encrypted communication.

---

# 27. Configuration from a Text File

Network administrators often prepare configurations in a text editor before applying them.

Example:

```text
hostname R1

interface g0/0
 ip address 172.16.1.1 255.255.255.252
 no shutdown
```

The configuration can then be pasted into the Cisco CLI.

For larger environments, configuration files can also be transferred using protocols such as TFTP.

---

# 28. Cisco IOS Command Help

Cisco IOS provides built-in command help.

Use:

```bash
?
```

For example:

```bash
Router# ?
```

This displays available commands in the current mode.

You can also use:

```bash
show ?
```

to see available options for `show`.

This is extremely useful when learning Cisco IOS.

---

# 29. `reload`

The `reload` command restarts the Cisco device.

```bash
reload
```

It requires privileged EXEC mode.

Conceptually:

```text
Running IOS
    ↓
Reload
    ↓
Restart device
    ↓
Load IOS from Flash
```

Be careful when using `reload` on real production equipment.

---

# 30. Second Lab — Connecting Two Routers

Topology:

```text
R1 ---------------- R2
```

Example addressing:

```text
R1:
Loopback0 = 1.1.1.1/32
G0/0      = 172.16.1.1/30

R2:
Loopback0 = 2.2.2.2/32
G0/0      = 172.16.1.2/30
```

The `/30` network is:

```text
172.16.1.0/30
```

Addresses:

```text
Network:    172.16.1.0
R1:         172.16.1.1
R2:         172.16.1.2
Broadcast:  172.16.1.3
```

---

# 31. Configure R1

```bash
enable
configure terminal

hostname R1

interface loopback 0
ip address 1.1.1.1 255.255.255.255
no shutdown

interface g0/0
ip address 172.16.1.1 255.255.255.252
no shutdown
```

Check:

```bash
show ip interface brief
```

---

# 32. Configure R2

```bash
enable
configure terminal

hostname R2

interface loopback 0
ip address 2.2.2.2 255.255.255.255
no shutdown

interface g0/0
ip address 172.16.1.2 255.255.255.252
no shutdown
```

Check:

```bash
show ip interface brief
```

---

# 33. Test the Direct Connection

Because:

```text
R1 = 172.16.1.1/30
R2 = 172.16.1.2/30
```

both routers are in the same subnet.

From R2:

```bash
ping 172.16.1.1
```

From R1:

```bash
ping 172.16.1.2
```

If the interfaces are correctly configured, the routers should be able to communicate.

---

# 34. Routing Table

Use:

```bash
show ip route
```

This displays the router's IP routing table.

Example:

```text
R1# show ip route
```

The routing table tells the router where to send packets.

---

# 35. OSPF

OSPF stands for:

> **Open Shortest Path First**

It is a dynamic routing protocol.

Start OSPF:

```bash
router ospf 1
```

Where:

```text
1
```

is the OSPF process ID.

---

# 36. Configure OSPF on R1

```bash
configure terminal

router ospf 1

network 1.1.1.1 0.0.0.0 area 0
network 172.16.1.0 0.0.0.3 area 0
```

The first command advertises the loopback network.

The second command enables OSPF on the R1-R2 link.

---

# 37. Configure OSPF on R2

```bash
configure terminal

router ospf 1

network 2.2.2.2 0.0.0.0 area 0
network 172.16.1.0 0.0.0.3 area 0
```

---

# 38. OSPF Areas

OSPF can divide a large network into areas.

The most common area is:

```text
Area 0
```

Area 0 is called the **backbone area**.

Conceptually:

```text
        Area 1
          |
          |
       Area 0
       /    \
   Area 2   Area 3
```

The purpose of areas is to improve scalability and reduce the amount of routing information that needs to be maintained throughout the network.

---

# 39. Verify OSPF Routing

Run:

```bash
show ip route
```

If OSPF has successfully learned the remote loopback, you should see an OSPF route.

You can also test connectivity.

From R1:

```bash
ping 2.2.2.2
```

From R2:

```bash
ping 1.1.1.1
```

---

# 40. Adding Another Network

Suppose R2 receives another interface/network.

OSPF will not automatically advertise that network just because the interface exists.

You need to include the network in OSPF.

Example:

```bash
router ospf 1
network 192.168.10.0 0.0.0.255 area 0
```

Then R1 can learn the network through OSPF.

---

# 41. Loopback Interface

A loopback interface is a logical interface rather than a physical Ethernet port.

Example:

```bash
interface loopback 0
ip address 1.1.1.1 255.255.255.255
```

A loopback interface is useful for:

* Router identification
* Management
* OSPF router IDs
* Testing
* Stable addressing

### Why use a loopback?

A physical interface can go down if:

```text
Cable disconnected
Interface shutdown
Physical link failure
```

A loopback interface is not tied to a physical cable.

---

# 42. Traceroute

Traceroute displays the path packets take toward a destination.

Cisco IOS:

```bash
traceroute <IP-address>
```

Example:

```bash
traceroute 2.2.2.2
```

Conceptually:

```text
PC
 ↓
R1
 ↓
R2
 ↓
Destination
```

Traceroute uses mechanisms related to TTL expiration to identify intermediate hops.

> TTL itself is not the same thing as traceroute.

---

# 43. Cisco Device Memory

Cisco devices use several types of memory.

| Memory | Purpose                                    |
| ------ | ------------------------------------------ |
| RAM    | Running configuration and active processes |
| NVRAM  | Startup configuration                      |
| Flash  | Cisco IOS software                         |
| ROM    | Bootstrap/low-level startup software       |

## 43.1 RAM

RAM contains the active running configuration.

```text
running-config
```

When the router loses power, changes in RAM can be lost unless they are saved.

---

## 43.2 NVRAM

NVRAM stores the startup configuration.

```text
startup-config
```

This configuration is loaded when the router boots.

---

## 43.3 Flash

Flash stores the Cisco IOS software image.

```text
Cisco IOS
```

---

## 43.4 ROM

ROM contains bootstrap and low-level startup functionality.

---

# 44. Running Configuration vs Startup Configuration

Two important configurations are:

```text
running-config
```

and:

```text
startup-config
```

### Running configuration

Current configuration stored in RAM.

```text
RAM
 ↓
running-config
```

### Startup configuration

Configuration saved for the next boot.

```text
NVRAM
 ↓
startup-config
```

To save the running configuration:

```bash
copy running-config startup-config
```

Short form:

```bash
copy run start
```

---

# 45. Copying Configuration

The command:

```bash
copy system:running-config
```

copies the currently active running configuration to another destination.

Commonly, you will use:

```bash
copy running-config startup-config
```

or:

```bash
copy run start
```

This saves the current configuration so it can survive a reboot.

---

# 46. ARP Cache

Routers and hosts maintain an ARP cache.

Check it on Cisco IOS with:

```bash
show ip arp
```

The cache stores mappings such as:

```text
IP Address       MAC Address
172.16.1.2       xxxx.xxxx.xxxx
```

ARP entries remain cached for a period of time and can eventually expire.

---

# 47. CDP — Cisco Discovery Protocol

CDP stands for:

> **Cisco Discovery Protocol**

It is a Cisco proprietary Layer 2 protocol used to discover directly connected Cisco devices.

It can provide information such as:

* Device name
* Connected interface
* Device type
* IP information
* Platform

Example:

```text
R1 -------- R2
     CDP
```

---

# 48. Multicast vs Broadcast

Broadcast traffic is intended for all devices within the relevant broadcast domain.

Multicast traffic is intended for a specific multicast group.

Routers generally do not forward normal Layer 2 broadcasts between interfaces.

Multicast routing is different and can use mechanisms such as Reverse Path Forwarding (RPF) and multicast distribution trees.

---

# 49. Common Q&A

## Q1. Can an IP address be spoofed?

Yes.

An attacker can forge the source IP address of packets.

This is called:

> **IP spoofing**

Firewalls and other security mechanisms can help detect or mitigate spoofed traffic.

---

## Q2. Is BGP an Application Layer protocol?

Yes.

BGP is an application-layer routing protocol and uses:

```text
TCP port 179
```

BGP exchanges routing information between autonomous systems.

---

## Q3. Does Layer 2 protect against IP spoofing?

Layer 2 itself does not inherently prevent IP spoofing.

Layer 2 deals primarily with:

* Ethernet frames
* MAC addresses
* Switching
* VLANs

IP spoofing occurs at Layer 3.

Additional security mechanisms may be used to mitigate it.

---

## Q4. What are Frame Relay and ATM?

These are older networking technologies.

### Frame Relay

A WAN technology that used virtual circuits to connect networks.

### ATM

ATM stands for:

> Asynchronous Transfer Mode

It uses fixed-size cells rather than Ethernet-style variable-length frames.

These technologies are mostly legacy technologies today.

---

## Q5. Why focus on only four layers?

The modern TCP/IP model commonly groups networking functionality into fewer layers than the seven-layer OSI model.

A common TCP/IP model is:

```text
Application
Transport
Internet
Network Access
```

The OSI model has:

```text
Application
Presentation
Session
Transport
Network
Data Link
Physical
```

Both models describe the same general networking concepts at different levels of abstraction.

---

## Q6. Why is BGP Application Layer if it uses TCP?

Because the protocol's purpose determines its layer.

BGP:

```text
BGP
 ↓
TCP
 ↓
IP
 ↓
Ethernet
```

BGP uses TCP as its transport protocol.

Using TCP does not make BGP a Transport Layer protocol.

---

## Q7. What is the difference between a Layer 3 switch and a router?

A Layer 3 switch can perform routing, particularly for high-speed routing within a LAN.

A router generally provides broader routing capabilities and is commonly used to connect different networks, WANs, and external networks.

---

## Q8. What is a TCP frame?

There is no TCP frame.

Correct terminology:

```text
TCP → Segment
IP  → Packet
Ethernet → Frame
```

---

## Q9. How does a PC learn another device's MAC address?

Through ARP when using IPv4 on the local network.

Example:

```text
PC1:
"Who has 192.168.1.20?"

PC2:
"192.168.1.20 is me.
My MAC is AA:BB:CC:DD:EE:FF."
```

---

## Q10. Does a router use ARP?

Yes.

Routers can use ARP to resolve IPv4 addresses to MAC addresses on Ethernet interfaces.

For example, when R1 needs to send an Ethernet frame to R2:

```text
R1
 |
 | ARP
 v
"What is the MAC of 172.16.1.2?"
 |
 v
R2
```

R1 can then build an Ethernet frame using R2's MAC address.

---

## Q11. Does a router send a broadcast MAC to the next router?

Normally, no.

For a directly connected Ethernet next hop, the router uses:

```text
Destination IP → Next-hop IP
Destination MAC → Next-hop MAC
```

ARP is used to discover the next-hop MAC address when necessary.

---

## Q12. Why is WLAN considered less reliable than wired Ethernet?

Wireless networks are susceptible to:

* Interference
* Signal attenuation
* Congestion
* Variable latency
* Packet loss

This doesn't mean WLAN cannot carry important traffic. Modern Wi-Fi is highly capable, but wired Ethernet can provide a more predictable physical connection in many environments.

---

# 50. Quick Command Reference

| Command                   | Purpose                         |
| ------------------------- | ------------------------------- |
| `enable`                  | Enter privileged EXEC mode      |
| `config t`                | Enter global configuration mode |
| `hostname R1`             | Change device hostname          |
| `int g0/0`                | Enter interface configuration   |
| `int lo0`                 | Enter Loopback0                 |
| `ip addr X.X.X.X Y.Y.Y.Y` | Assign IP address               |
| `no shutdown`             | Enable interface                |
| `show run`                | Show running configuration      |
| `show ip int brief`       | Show interface/IP summary       |
| `show ip route`           | Show routing table              |
| `show ip arp`             | Show ARP table                  |
| `ping X.X.X.X`            | Test connectivity               |
| `traceroute X.X.X.X`      | Show path to destination        |
| `router ospf 1`           | Start OSPF process              |
| `network ... area 0`      | Add network to OSPF             |
| `reload`                  | Restart device                  |
| `?`                       | Show available commands         |
| `copy run start`          | Save running config             |

---

# 51. Important Concepts to Remember

## OSI PDU Names

```text
Application  → Data
Transport    → Segment
Network      → Packet
Data Link    → Frame
Physical     → Bits
```

## Addressing

```text
Layer 2 → MAC address
Layer 3 → IP address
Layer 4 → TCP/UDP ports
```

## Main Devices

```text
Switch → primarily Layer 2
Router → primarily Layer 3
```

## Important Protocols

```text
ARP  → IPv4 → MAC resolution
TCP  → Reliable transport
UDP  → Connectionless transport
OSPF → Dynamic routing
BGP  → Inter-domain routing
CDP  → Cisco device discovery
STP  → Prevent Layer 2 loops
```

## Cisco Memory

```text
RAM    → Running configuration
NVRAM  → Startup configuration
Flash  → IOS
ROM    → Bootstrap
```

---

# 52. Lab Checklist

Before moving to more advanced CCNA topics, make sure you can perform these tasks in Packet Tracer:

* [ ] Enter Cisco IOS CLI
* [ ] Switch between IOS modes
* [ ] Change hostname
* [ ] Configure a physical interface
* [ ] Configure a loopback interface
* [ ] Use `no shutdown`
* [ ] Read `show ip interface brief`
* [ ] Understand `up/up`
* [ ] Configure two routers
* [ ] Assign IP addresses
* [ ] Ping between routers
* [ ] Read a routing table
* [ ] Configure basic OSPF
* [ ] Verify OSPF routes
* [ ] Use `traceroute`
* [ ] Understand ARP
* [ ] Check the ARP table
* [ ] Save running configuration
* [ ] Understand RAM, ROM, Flash, and NVRAM

---

# 53. References

* Cisco Packet Tracer:
  https://www.netacad.com/cisco-packet-tracer

* Packet Tracer Installation Guide:
  https://www.youtube.com/watch?v=7sWn9MAuJFM

* Layer 3 Switch vs Router:
  https://www.come-star.com/blog/layer-3-switch-vs-router

---

# 54. Key Takeaway

The most important thing from this lesson is understanding how data moves through a network:

```text
Application
    ↓
Transport
    ↓
Network
    ↓
Data Link
    ↓
Physical
```

And during transmission, each layer adds its own information:

```text
Data
 ↓
TCP Segment
 ↓
IP Packet
 ↓
Ethernet Frame
 ↓
Bits
```

For practical Cisco networking, the core concepts to understand next are:

```text
IP Addressing
     ↓
Subnetting
     ↓
Ethernet & Switching
     ↓
VLANs
     ↓
Trunking
     ↓
STP
     ↓
Routing
     ↓
OSPF
     ↓
ACL / NAT / DHCP
     ↓
Network Security
```

These concepts form the foundation for the rest of the **CCNA 200-301** material.
