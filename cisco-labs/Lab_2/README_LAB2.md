# Cisco Packet Tracer – Small Office / Home Office (SOHO) Network Design & Implementation

**Project Title:** XYZ Company – Bonalbo Branch Network  
**Base Network:** `192.168.1.0/24`  
**Goal:** Design and implement a standalone branch network that meets all company requirements.

---

## Project Requirements

XYZ Company is opening a new branch in Bonalbo. The network must:

| Requirement | Description |
|-------------|-------------|
| a | Use **1 Router** and **1 Switch** (Cisco devices only) |
| b | Support **3 departments**: Admin/IT, Finance/HR, Customer Service/Reception |
| c | Each department must be in a **different VLAN** |
| d | Each department must have its own **wireless network** |
| e | All host devices must obtain IPv4 addresses **automatically (DHCP)** |
| f | Devices in all departments must be able to **communicate with each other** |

---

## Topology Overview

```
                          +-------------------+
                          |      Router       |
                          |   (Router-on-a-Stick)
                          +---------+---------+
                                    |
                              Trunk Link
                                    |
                          +---------+---------+
                          |      Switch       |
                          +--+----+----+----+--+
                             |    |    |    |
                    +--------+    |    |    +--------+
                    |             |    |             |
               VLAN 10         VLAN 20         VLAN 30
            (Admin/IT)     (Finance/HR)   (Customer Service)
                    |             |    |             |
            +-------+-------+     |    |     +-------+-------+
            |   Access Point|     |    |     |   Access Point|
            |   (Admin)     |     |    |     |   (Reception) |
            +---------------+     |    |     +---------------+
                                  |
                          +-------+-------+
                          |  Access Point |
                          |  (Finance)    |
                          +---------------+

End Devices per department:
- 1 PC (wired)
- 1 Printer (wired)
- Wireless clients (Laptop / Tablet / Smartphone) via Access Point
```

### Recommended Devices

| Device Type          | Quantity | Notes                                      |
|----------------------|----------|--------------------------------------------|
| Router               | 1        | e.g. 2911 or 1941                          |
| Switch               | 1        | e.g. 2960                                  |
| Access Point         | 3        | One per department (SSID isolation)        |
| PC                   | 3        | One per department (wired)                 |
| Printer              | 3        | One per department                         |
| Laptop / Tablet / Phone | Optional | For testing wireless connectivity       |

---

## 1. VLAN & Subnetting Design

**Number of departments = 3** → We need at least **3 subnets**.

### Subnet Calculation

$$
2^n \ge 3 \quad \Rightarrow \quad n = 2
$$

**Subnet Mask:** `255.255.255.192` (`/26`)  
**Block Size:** \( 256 - 192 = 64 \)

| VLAN | Department                  | Network ID       | Valid Host Range                 | Broadcast Address | Gateway (Router)   |
|------|-----------------------------|------------------|----------------------------------|-------------------|--------------------|
| 10   | Admin/IT                    | `192.168.1.0`    | `192.168.1.1` – `192.168.1.62`   | `192.168.1.63`    | `192.168.1.1`      |
| 20   | Finance/HR                  | `192.168.1.64`   | `192.168.1.65` – `192.168.1.126` | `192.168.1.127`   | `192.168.1.65`     |
| 30   | Customer Service/Reception  | `192.168.1.128`  | `192.168.1.129` – `192.168.1.190`| `192.168.1.191`   | `192.168.1.129`    |

> **Note:** The fourth possible subnet (`192.168.1.192/26`) is left unused.

---

## 2. Physical Connectivity

1. Connect **Router** GigabitEthernet0/0 to **Switch** GigabitEthernet0/1 using a **Copper Straight-Through** cable (this will be the trunk link).
2. On the Switch:
   - Connect PC and Printer of **Admin/IT** to FastEthernet ports in VLAN 10 range.
   - Connect PC and Printer of **Finance/HR** to FastEthernet ports in VLAN 20 range.
   - Connect PC and Printer of **Customer Service** to FastEthernet ports in VLAN 30 range.
3. Connect each **Access Point** to a switch port belonging to its respective VLAN.
4. Place wireless clients (Laptop, Tablet, etc.) near the Access Points.

**Suggested Port Assignment (example):**

| Switch Port     | Connected Device          | VLAN |
|-----------------|---------------------------|------|
| Fa0/1 – Fa0/3   | Admin PC, Printer, AP     | 10   |
| Fa0/4 – Fa0/6   | Finance PC, Printer, AP   | 20   |
| Fa0/7 – Fa0/9   | Reception PC, Printer, AP | 30   |
| Gi0/1           | Router (Trunk)            | —    |

---

## 3. Switch Configuration

```cisco
enable
configure terminal

! Create VLANs and name them
vlan 10
 name Admin-IT
vlan 20
 name Finance-HR
vlan 30
 name CustomerService-Reception
exit

! Assign access ports to VLANs
interface range FastEthernet0/1 - 3
 switchport mode access
 switchport access vlan 10
 no shutdown
exit

interface range FastEthernet0/4 - 6
 switchport mode access
 switchport access vlan 20
 no shutdown
exit

interface range FastEthernet0/7 - 9
 switchport mode access
 switchport access vlan 30
 no shutdown
exit

! Configure trunk port to the Router
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
 no shutdown
exit

end
write memory
```

---

## 4. Router Configuration (Router-on-a-Stick)

The router will perform **Inter-VLAN Routing** using subinterfaces.

```cisco
enable
configure terminal

! Physical interface must be up (no IP address on the main interface)
interface GigabitEthernet0/0
 no shutdown
exit

! Subinterface for VLAN 10 (Admin/IT)
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.1.1 255.255.255.192
exit

! Subinterface for VLAN 20 (Finance/HR)
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.1.65 255.255.255.192
exit

! Subinterface for VLAN 30 (Customer Service)
interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.1.129 255.255.255.192
exit

end
write memory
```

---

## 5. DHCP Configuration on the Router

All host devices will obtain IP addresses automatically.

```cisco
configure terminal

! Enable DHCP service
service dhcp

! DHCP Pool for Admin/IT (VLAN 10)
ip dhcp pool Admin-Pool
 network 192.168.1.0 255.255.255.192
 default-router 192.168.1.1
 dns-server 8.8.8.8
 domain-name admin.xyz.local
exit

! DHCP Pool for Finance/HR (VLAN 20)
ip dhcp pool Finance-Pool
 network 192.168.1.64 255.255.255.192
 default-router 192.168.1.65
 dns-server 8.8.8.8
 domain-name finance.xyz.local
exit

! DHCP Pool for Customer Service (VLAN 30)
ip dhcp pool Reception-Pool
 network 192.168.1.128 255.255.255.192
 default-router 192.168.1.129
 dns-server 8.8.8.8
 domain-name reception.xyz.local
exit

end
write memory
```

> **Tip:** You can also exclude the gateway addresses if desired:
> ```cisco
> ip dhcp excluded-address 192.168.1.1
> ip dhcp excluded-address 192.168.1.65
> ip dhcp excluded-address 192.168.1.129
> ```

---

## 6. Wireless Access Point Configuration

For each Access Point:

1. Click the Access Point → **Config** tab → **Port 1** (or the interface connected to the switch).
2. Set the following:

| Access Point     | SSID          | Authentication | Password Example | Connected to VLAN |
|------------------|---------------|----------------|------------------|-------------------|
| AP-Admin         | `Admin`       | WPA2-PSK       | `Admin@123`      | 10                |
| AP-Finance       | `Finance`     | WPA2-PSK       | `Finance@123`    | 20                |
| AP-Reception     | `Reception`   | WPA2-PSK       | `Reception@123`  | 30                |

> Make sure the Access Point is connected to a switch port that belongs to the correct VLAN.

---

## 7. End Device Configuration

### Wired Devices (PC & Printer)

1. Click the device → **Desktop** tab → **IP Configuration**.
2. Change from **Static** to **DHCP**.
3. The device should automatically receive:
   - Correct IP address from its VLAN subnet
   - Subnet mask `255.255.255.192`
   - Default gateway of its VLAN
   - DNS server

### Wireless Clients (Laptop / Tablet / Smartphone)

1. Click the device → **Desktop** / **Config** → Wireless or PC Wireless.
2. Connect to the correct SSID (`Admin`, `Finance`, or `Reception`).
3. Enter the WPA2 password.
4. Set IP configuration to **DHCP**.

---

## 8. Verification & Testing

### 1. Check IP addresses
On any PC or wireless client:
```bash
ipconfig
```
Verify that the IP belongs to the correct subnet and the gateway is correct.

### 2. Test connectivity within the same VLAN
```bash
ping <IP of another device in same VLAN>
```

### 3. Test Inter-VLAN communication (most important)
From a device in VLAN 10, ping a device in VLAN 20 and VLAN 30:
```bash
ping 192.168.1.70      # Example Finance device
ping 192.168.1.140     # Example Reception device
```

### Expected Result
- All pings should succeed with **0% packet loss**.
- This confirms that Inter-VLAN Routing is working correctly.

### Useful Router Commands for Troubleshooting
```cisco
show ip interface brief
show ip route
show ip dhcp binding
show vlan brief          ! (on the Switch)
```

---

## Summary Checklist

- [x] 1 Router + 1 Switch used
- [x] 3 VLANs created (10, 20, 30)
- [x] Correct subnetting with `/26` mask
- [x] Trunk link between Switch and Router
- [x] Router-on-a-Stick (subinterfaces) configured
- [x] Three separate wireless SSIDs (one per department)
- [x] DHCP pools created for all three VLANs
- [x] All wired and wireless devices obtain IP via DHCP
- [x] Full communication between all departments verified

---

## Quick Reference – IP Addressing

| VLAN | Network            | Gateway          | DHCP Pool Name   | SSID       |
|------|--------------------|------------------|------------------|------------|
| 10   | 192.168.1.0/26     | 192.168.1.1      | Admin-Pool       | Admin      |
| 20   | 192.168.1.64/26    | 192.168.1.65     | Finance-Pool     | Finance    |
| 30   | 192.168.1.128/26   | 192.168.1.129    | Reception-Pool   | Reception  |

---

**End of Document**
```

