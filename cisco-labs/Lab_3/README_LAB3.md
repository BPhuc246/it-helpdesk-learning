# Cisco Packet Tracer – Vic Modern Hotel Network Design & Implementation

**Project:** End-of-Year Networking Project – Vic Modern Hotel  
**Goal:** Design and implement a multi-floor hotel network with full inter-floor communication, VLANs, wireless, DHCP, OSPF, SSH, and Port Security.

---

## Project Requirements Summary

| #  | Requirement                                                                 |
|----|-----------------------------------------------------------------------------|
| 1  | Three routers (one per floor), all located in the IT/Server room            |
| 2  | Routers connected to each other using **Serial DCE** cables                 |
| 3  | Point-to-point networks: `10.10.10.0/30`, `10.10.10.4/30`, `10.10.10.8/30`  |
| 4  | One switch per floor                                                        |
| 5  | Wi-Fi network available on every floor for laptops and phones               |
| 6  | One printer per department                                                  |
| 7  | Each department in its own VLAN with the specified networks                 |
| 8  | Use **OSPF** as the routing protocol                                        |
| 9  | All devices obtain IP addresses dynamically (DHCP)                          |
| 10 | Full communication between all devices across all floors                    |
| 11 | Configure **SSH** on all routers for secure remote login                    |
| 12 | Add a Test-PC in the IT department (port Fa0/1) to test remote login        |
| 13 | Configure **Port Security** on the IT switch (sticky MAC, violation shutdown) |

---

## Network Topology Overview

```
                        SERVER ROOM (3rd Floor - IT)
        +-------------------+     +-------------------+     +-------------------+
        |   Router Floor 1  |-----|   Router Floor 2  |-----|   Router Floor 3  |
        |   (1st Floor)     |     |   (2nd Floor)     |     |   (3rd Floor)     |
        +---------+---------+     +---------+---------+     +---------+---------+
                  |                         |                         |
             Gi0/0 (Trunk)             Gi0/0 (Trunk)             Gi0/0 (Trunk)
                  |                         |                         |
        +---------+---------+     +---------+---------+     +---------+---------+
        | Switch Floor 1    |     | Switch Floor 2    |     | Switch Floor 3    |
        +--+----+----+----+-+     +--+----+----+----+-+     +--+----+----+----+-+
           |    |    |    |          |    |    |    |          |    |    |    |
        VLAN60 VLAN70 VLAN80      VLAN30 VLAN40 VLAN50      VLAN10 VLAN20  Test-PC
       Logistics Store Reception   Sales   HR  Finance        IT   Admin   (Fa0/1)
           |    |    |               |    |    |               |    |
          AP   AP   AP              AP   AP   AP              AP   AP
```

### Serial Link Addressing (/30)

| Link                  | Network          | Router Interface     | IP Address     | Other Side IP  |
|-----------------------|------------------|----------------------|----------------|----------------|
| R1 ↔ R2               | `10.10.10.0/30`  | R1 Se0/2/0           | `10.10.10.1`   | `10.10.10.2`   |
| R2 ↔ R3               | `10.10.10.4/30`  | R2 Se0/2/1           | `10.10.10.5`   | `10.10.10.6`   |
| R1 ↔ R3               | `10.10.10.8/30`  | R1 Se0/2/1           | `10.10.10.9`   | `10.10.10.10`  |

> **Note:** One side of each serial link must be configured with `clock rate` (DCE side).

---

## VLAN & IP Addressing Plan

### 1st Floor (Router 1)

| Department   | VLAN | Network            | Gateway (Router) | DHCP Pool    |
|--------------|------|--------------------|------------------|--------------|
| Logistics    | 60   | `192.168.6.0/24`   | `192.168.6.1`    | Logistics    |
| Store        | 70   | `192.168.7.0/24`   | `192.168.7.1`    | Store        |
| Reception    | 80   | `192.168.8.0/24`   | `192.168.8.1`    | Reception    |

### 2nd Floor (Router 2)

| Department   | VLAN | Network            | Gateway (Router) | DHCP Pool    |
|--------------|------|--------------------|------------------|--------------|
| Sales        | 30   | `192.168.3.0/24`   | `192.168.3.1`    | Sales        |
| HR           | 40   | `192.168.4.0/24`   | `192.168.4.1`    | HR           |
| Finance      | 50   | `192.168.5.0/24`   | `192.168.5.1`    | Finance      |

### 3rd Floor (Router 3)

| Department   | VLAN | Network            | Gateway (Router) | DHCP Pool    |
|--------------|------|--------------------|------------------|--------------|
| IT           | 10   | `192.168.1.0/24`   | `192.168.1.1`    | IT           |
| Admin        | 20   | `192.168.2.0/24`   | `192.168.2.1`    | Admin        |

---

## 1. Physical Setup & Serial Modules

### Adding Serial Interfaces (HWIC-2T)

1. Click each Router → **Physical** tab.
2. Power off the router (click the power button).
3. Drag the **HWIC-2T** module into an empty slot.
4. Power the router back on.
5. Repeat for all three routers.

### Cabling

- Connect the three routers using **Serial DCE** cables (the red cable with a clock icon).
- Connect each Router’s GigabitEthernet0/0 to its respective Switch using a Straight-Through cable (this will be the trunk).
- Connect PCs, Printers, and Access Points to the switches according to the VLAN port assignments below.

---

## 2. Switch Configuration

### Switch Floor 1 (Reception / Store / Logistics)

```cisco
enable
configure terminal
hostname Switch-Floor1

vlan 60
 name Logistics
vlan 70
 name Store
vlan 80
 name Reception
exit

! Access ports
interface range FastEthernet0/2 - 3
 switchport mode access
 switchport access vlan 60
 no shutdown

interface range FastEthernet0/4 - 5
 switchport mode access
 switchport access vlan 70
 no shutdown

interface range FastEthernet0/6 - 8
 switchport mode access
 switchport access vlan 80
 no shutdown

! Trunk to Router
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 60,70,80
 no shutdown
exit

end
write memory
```

### Switch Floor 2 (Sales / HR / Finance)

```cisco
enable
configure terminal
hostname Switch-Floor2

vlan 30
 name Sales
vlan 40
 name HR
vlan 50
 name Finance
exit

interface range FastEthernet0/2 - 3
 switchport mode access
 switchport access vlan 30
 no shutdown

interface range FastEthernet0/4 - 5
 switchport mode access
 switchport access vlan 40
 no shutdown

interface range FastEthernet0/6 - 8
 switchport mode access
 switchport access vlan 50
 no shutdown

interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 30,40,50
 no shutdown
exit

end
write memory
```

### Switch Floor 3 (IT / Admin) + Port Security

```cisco
enable
configure terminal
hostname Switch-Floor3

vlan 10
 name IT
vlan 20
 name Admin
exit

! IT ports (including Test-PC on Fa0/1)
interface range FastEthernet0/1 - 3
 switchport mode access
 switchport access vlan 10
 no shutdown

interface range FastEthernet0/4 - 6
 switchport mode access
 switchport access vlan 20
 no shutdown

! Trunk to Router
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown
exit

! ===== Port Security on Fa0/1 (Test-PC only) =====
interface FastEthernet0/1
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
exit

end
write memory
```

> **Port Security Explanation:**  
> - `maximum 1` → Only one MAC address allowed  
> - `mac-address sticky` → Learns the MAC of the currently connected device (Test-PC)  
> - `violation shutdown` → Port is disabled if another device is connected

---

## 3. Router Configuration

### Common Steps for All Routers

```cisco
enable
configure terminal
hostname Router-FloorX          ! Change X to 1, 2 or 3
```

### Router Floor 1 (1st Floor)

```cisco
! Serial Interfaces
interface Serial0/2/0
 ip address 10.10.10.1 255.255.255.252
 clock rate 64000                 ! DCE side
 no shutdown

interface Serial0/2/1
 ip address 10.10.10.9 255.255.255.252
 clock rate 64000                 ! DCE side (if required)
 no shutdown

! Subinterfaces (Router-on-a-Stick)
interface GigabitEthernet0/0
 no shutdown

interface GigabitEthernet0/0.60
 encapsulation dot1Q 60
 ip address 192.168.6.1 255.255.255.0

interface GigabitEthernet0/0.70
 encapsulation dot1Q 70
 ip address 192.168.7.1 255.255.255.0

interface GigabitEthernet0/0.80
 encapsulation dot1Q 80
 ip address 192.168.8.1 255.255.255.0
exit
```

### Router Floor 2 (2nd Floor)

```cisco
interface Serial0/2/0
 ip address 10.10.10.2 255.255.255.252
 no shutdown

interface Serial0/2/1
 ip address 10.10.10.5 255.255.255.252
 clock rate 64000
 no shutdown

interface GigabitEthernet0/0
 no shutdown

interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.3.1 255.255.255.0

interface GigabitEthernet0/0.40
 encapsulation dot1Q 40
 ip address 192.168.4.1 255.255.255.0

interface GigabitEthernet0/0.50
 encapsulation dot1Q 50
 ip address 192.168.5.1 255.255.255.0
exit
```

### Router Floor 3 (3rd Floor)

```cisco
interface Serial0/2/0
 ip address 10.10.10.6 255.255.255.252
 no shutdown

interface Serial0/2/1
 ip address 10.10.10.10 255.255.255.252
 no shutdown

interface GigabitEthernet0/0
 no shutdown

interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.1.1 255.255.255.0

interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.2.1 255.255.255.0
exit
```

---

## 4. DHCP Configuration (on each Router)

### Router Floor 1

```cisco
service dhcp

ip dhcp pool Logistics
 network 192.168.6.0 255.255.255.0
 default-router 192.168.6.1
 dns-server 8.8.8.8

ip dhcp pool Store
 network 192.168.7.0 255.255.255.0
 default-router 192.168.7.1
 dns-server 8.8.8.8

ip dhcp pool Reception
 network 192.168.8.0 255.255.255.0
 default-router 192.168.8.1
 dns-server 8.8.8.8
exit
```

### Router Floor 2

```cisco
service dhcp

ip dhcp pool Sales
 network 192.168.3.0 255.255.255.0
 default-router 192.168.3.1
 dns-server 8.8.8.8

ip dhcp pool HR
 network 192.168.4.0 255.255.255.0
 default-router 192.168.4.1
 dns-server 8.8.8.8

ip dhcp pool Finance
 network 192.168.5.0 255.255.255.0
 default-router 192.168.5.1
 dns-server 8.8.8.8
exit
```

### Router Floor 3

```cisco
service dhcp

ip dhcp pool IT
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 8.8.8.8

ip dhcp pool Admin
 network 192.168.2.0 255.255.255.0
 default-router 192.168.2.1
 dns-server 8.8.8.8
exit
```

---

## 5. OSPF Configuration

### Router Floor 1

```cisco
router ospf 10
 network 10.10.10.0 0.0.0.3 area 0
 network 10.10.10.8 0.0.0.3 area 0
 network 192.168.6.0 0.0.0.255 area 0
 network 192.168.7.0 0.0.0.255 area 0
 network 192.168.8.0 0.0.0.255 area 0
exit
```

### Router Floor 2

```cisco
router ospf 10
 network 10.10.10.0 0.0.0.3 area 0
 network 10.10.10.4 0.0.0.3 area 0
 network 192.168.3.0 0.0.0.255 area 0
 network 192.168.4.0 0.0.0.255 area 0
 network 192.168.5.0 0.0.0.255 area 0
exit
```

### Router Floor 3

```cisco
router ospf 10
 network 10.10.10.4 0.0.0.3 area 0
 network 10.10.10.8 0.0.0.3 area 0
 network 192.168.1.0 0.0.0.255 area 0
 network 192.168.2.0 0.0.0.255 area 0
exit
```

> **Note:** OSPF uses wildcard masks (`0.0.0.3` for /30 and `0.0.0.255` for /24).

---

## 6. SSH Configuration (All Routers)

Run the following on **every router**:

```cisco
ip domain-name hotel.local
username admin privilege 15 secret Cisco123
crypto key generate rsa
! When prompted, enter 1024 or 2048

line vty 0 15
 login local
 transport input ssh
exit

! Optional: Disable Telnet
line vty 0 15
 transport input ssh
```

---

## 7. Wireless Access Points

For each department:

1. Place an **Access Point** and connect it to a switch port belonging to the correct VLAN.
2. Configure:
   - **SSID**: Use department name (e.g., `Logistics`, `Finance`, `IT`…)
   - **Authentication**: WPA2-PSK
   - **Password**: Choose a strong password (e.g., `Hotel@2026`)

---

## 8. End Device Configuration

1. On every **PC** and **Printer**:
   - Go to **Desktop → IP Configuration**
   - Select **DHCP**
2. On wireless devices (Laptop / Smartphone):
   - Connect to the correct SSID
   - Set IP to **DHCP**

---

## 9. Verification & Testing

### 1. Check IP Addressing
```bash
ipconfig
```

### 2. Test Inter-VLAN and Inter-Floor Connectivity
From any PC, ping devices in other VLANs and other floors. All should succeed.

### 3. Test SSH Remote Login
From the **Test-PC** (or any PC):
```bash
ssh -l admin 192.168.1.1          ! Router Floor 3
ssh -l admin 192.168.6.1          ! Router Floor 1
ssh -l admin 192.168.3.1          ! Router Floor 2
```

### 4. Verify Port Security
```cisco
! On Switch-Floor3
show port-security interface FastEthernet0/1
show port-security address
```

### 5. Useful Verification Commands
```cisco
show ip interface brief
show ip route
show ip ospf neighbor
show ip dhcp binding
show vlan brief
```

---

## Final Checklist

- [x] 3 Routers with Serial modules installed
- [x] Serial links using /30 networks with clock rate
- [x] 3 Switches (one per floor)
- [x] All VLANs created and correctly assigned
- [x] Trunk links between Switches and Routers
- [x] Subinterfaces (Router-on-a-Stick) configured
- [x] DHCP pools for every VLAN
- [x] OSPF running and advertising all networks
- [x] SSH configured on all routers
- [x] Port Security on IT switch (Fa0/1) with sticky MAC
- [x] Wireless Access Points for each department
- [x] Full communication between all devices verified

---

**End of Document**
```