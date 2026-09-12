# Cisco Packet Tracer – Simple Networking Project

```
Design a network in CISCO packet tracer to connect ACCOUNTS and DELIVERY departments through the following: [40 points]  

a. Each department should contain at least 2 PCs + 1 printer.  

b. Appropriate number of switches and routers should be used in the network.  

c. Using the given network address 192.168.40.0, all interfaces should be configured with appropriate IP addresses, subnet mask and gateways.  

d. All devices in the network should be connected using appropriate cables.  

e. Test the connectivity between ACCOUNTS and DELIVERY department - PCs in DELIVERY department should be able to ping the PCs in ACCOUNTS department.
```

---

## Topology Overview

```
                +------------- Router -------------+
                |                                  |
PC0 --------- Switch 1                          Switch 2 ----------- PC2
               | |                                | |
PC1 -----------+ |                                | +---------------- PC3
Printer1 --------+                                +---------------- Printer 2
```

### Devices Required
| Device Type | Quantity | Purpose                          |
|-------------|----------|----------------------------------|
| Router      | 1        | Inter-department routing         |
| Switch      | 2        | One per department               |
| PC          | 4        | 2 per department                 |
| Printer     | 2        | 1 per department                 |

---

## 1. Subnetting Calculation

**Given Network:** `192.168.40.0`  
**Number of departments (subnets needed):** 2

### Borrowed Bits
$$
2^n \ge 2 \quad \Rightarrow \quad n = 1
$$

**Subnet Mask:** `255.255.255.128` (`/25`)  
Binary: `11111111.11111111.11111111.10000000`

**Block Size:** \( 256 - 128 = 128 \)

### Subnet Details

| Subnet | Network ID       | Valid Host Range              | Broadcast ID     | Department |
|--------|------------------|-------------------------------|------------------|------------|
| 1      | `192.168.40.0`   | `192.168.40.1` – `192.168.40.126` | `192.168.40.127` | ACCOUNTS   |
| 2      | `192.168.40.128` | `192.168.40.129` – `192.168.40.254` | `192.168.40.255` | DELIVERY   |

---

## 2. Physical Connectivity

1. Place **1 Router**, **2 Switches**, **4 PCs**, and **2 Printers**.
2. Connect the Router to both Switches using **Copper Straight-Through** cables (GigabitEthernet interfaces recommended).
3. On each Switch:
   - Connect **2 PCs**
   - Connect **1 Printer**
4. Use appropriate cables (Straight-Through for end devices ↔ switch).

---

## 3. Router Configuration

```cisco
enable
configure terminal

! Bring interfaces up
interface range GigabitEthernet0/0 - 1
no shutdown
exit

! ACCOUNTS Department (Subnet 1)
interface GigabitEthernet0/0
ip address 192.168.40.1 255.255.255.128
no shutdown
exit

! DELIVERY Department (Subnet 2)
interface GigabitEthernet0/1
ip address 192.168.40.129 255.255.255.128
no shutdown
exit

end
write memory
```

> **Note:** Switches do **not** need IP addresses for basic Layer-2 forwarding in this design. The Router acts as the default gateway for each subnet.

---

## 4. End Device IP Configuration

### ACCOUNTS Department (Switch 1 – Gateway: `192.168.40.1`)

| Device    | IPv4 Address     | Subnet Mask       | Default Gateway  |
|-----------|------------------|-------------------|------------------|
| PC0       | `192.168.40.2`   | `255.255.255.128` | `192.168.40.1`   |
| PC1       | `192.168.40.3`   | `255.255.255.128` | `192.168.40.1`   |
| Printer1  | `192.168.40.4`   | `255.255.255.128` | `192.168.40.1`   |

### DELIVERY Department (Switch 2 – Gateway: `192.168.40.129`)

| Device    | IPv4 Address      | Subnet Mask       | Default Gateway   |
|-----------|-------------------|-------------------|-------------------|
| PC2       | `192.168.40.130`  | `255.255.255.128` | `192.168.40.129`  |
| PC3       | `192.168.40.131`  | `255.255.255.128` | `192.168.40.129`  |
| Printer2  | `192.168.40.132`  | `255.255.255.128` | `192.168.40.129`  |

**How to configure in Packet Tracer:**
1. Click the device → **Desktop** tab → **IP Configuration**
2. Select **Static**
3. Enter the values from the tables above

---

## 5. Connectivity Testing

From any PC, open the **Command Prompt** (Desktop → Command Prompt) and run:

```bash
# From ACCOUNTS → DELIVERY
ping 192.168.40.130
ping 192.168.40.131

# From DELIVERY → ACCOUNTS
ping 192.168.40.2
ping 192.168.40.3
```

### Expected Result
- All pings should succeed with **0% packet loss**.
- Example successful output:
  ```
  Reply from 192.168.40.130: bytes=32 time=1ms TTL=127
  Reply from 192.168.40.130: bytes=32 time=1ms TTL=127
  Reply from 192.168.40.130: bytes=32 time=1ms TTL=127
  Reply from 192.168.40.130: bytes=32 time=1ms TTL=127

  Ping statistics for 192.168.40.130:
      Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
  ```