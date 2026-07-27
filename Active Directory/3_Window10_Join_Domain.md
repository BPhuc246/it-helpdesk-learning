# Windows 10 Client Setup for Active Directory Lab

This guide explains how to set up a **Windows 10 client** and connect it to a **Windows Server 2016 Domain Controller** in a virtual lab environment.

---

## Prerequisites

### Download Windows 10 ISO

- Windows 10 ISO:
  - https://www.microsoft.com/en-us/software-download/windows10

---

# 1. Create the Windows 10 Virtual Machine

Create the virtual machine the same way as Windows Server 2016.

> **Important**
>
> During VM creation, **uncheck**:
>
> ```
> Proceed With Unattended Installation
> ```
>
> This allows you to complete the Windows installation manually.

---

# 2. Configure a Static IP Address

## Why use a Static IP?

In this lab there is:

- No DHCP Server
- No Cisco Router
- No Cisco Switch providing DHCP services

Normally, a DHCP server automatically assigns IP addresses to devices on the network.

Since our lab does **not** have one, we must manually configure static IP addresses.

Using static IPs ensures:

- Both virtual machines can communicate with each other.
- The Domain Controller always keeps the same IP address.
- DNS resolution remains consistent.
- Active Directory services work correctly.

---

## Windows Server 2016

Configure the following network settings:

| Setting | Value |
|----------|--------|
| IP Address | `10.1.10.2` |
| Subnet Mask | `255.0.0.0` |
| Default Gateway | `10.1.10.1` |
| Preferred DNS | `10.1.10.2` |
| Alternate DNS | `10.1.10.1` |

### Steps

```
Control Panel
    → Network and Internet
        → View network status and tasks
            → Change adapter settings
                → Ethernet
                    → Properties
                        → Internet Protocol Version 4 (TCP/IPv4)
                            → Properties
                                → Use the following IP address
```

Enter the values above and click **OK**.

---

## Change VM Network Adapter

In VirtualBox:

```
Devices
    → Network
        → Host-only Adapter
```

Replace:

```
NAT
```

with

```
Host-only Adapter
```

### Why use Host-only Adapter?

Host-only networking creates a **private virtual network** between:

- Windows Server
- Windows 10
- Your host computer (optional)

This is ideal for an Active Directory lab because:

- Both VMs can communicate.
- No Internet connection is required.
- The lab is isolated from your real network.
- Networking behaves similarly to an internal company LAN.

If NAT is used, communication between VMs may not match the network topology expected for the lab.

---

## Verify Network Configuration

Open Command Prompt.

```cmd
ipconfig
```

Verify:

- IPv4 Address
- Subnet Mask
- Default Gateway

---

# 3. Configure Windows 10 Administrator Account

Open:

```
File Explorer
    → This PC
        → Right Click
            → Manage
```

Navigate to:

```
System Tools
    → Local Users and Groups
        → Users
```

Select:

```
Administrator
```

Open **Properties**.

Uncheck:

```
Account is disabled
```

Click:

```
Apply
```

Then:

```
Set Password
```

Example:

```
user
```

---

## Sign in as Administrator

Sign out.

Log in using the built-in Administrator account.

---

## Delete the Temporary User Account

After logging in as Administrator:

```
Computer Management
    → Local Users and Groups
        → Users
```

Delete the temporary account created during Windows installation.

---

## Why enable Administrator and remove the temporary user?

This is **not strictly required**, but it is common practice in IT labs.

Reasons:

- The built-in Administrator has unrestricted local privileges.
- It avoids permission issues during Active Directory configuration.
- Many IT Support environments use the local Administrator account for maintenance.
- Removing the temporary account keeps the lab clean and avoids confusion.

In a real company, employees usually log in using **Domain Accounts**, not local accounts.

---

# 4. Install RSAT Tools

Search Windows for:

```
Optional Features
```

Install:

- RSAT: Active Directory Certificate Services Tools
- RSAT: Active Directory Domain Services and Lightweight Directory Services Tools
- RSAT: DHCP Server Tools
- RSAT: DNS Server Tools
- RSAT: Group Policy Management Tools
- RSAT: Remote Desktop Services Tools
- RSAT: Server Manager

Restart Windows after installation.

---

## Why install RSAT?

RSAT (Remote Server Administration Tools) allows administrators to manage Windows Server remotely from a Windows client.

Instead of logging into the server every time, you can manage:

- Active Directory
- DNS
- DHCP
- Group Policy
- Remote Desktop Services
- Server Manager

directly from Windows 10.

This is how many IT administrators work in real enterprise environments.

---

# 5. Install TeamViewer

Download and install TeamViewer.

> Used for remote support demonstrations during the lab.

---

# 6. Configure Windows 10 Static IP

| Setting | Value |
|----------|--------|
| IP Address | `10.1.10.3` |
| Subnet Mask | `255.0.0.0` |
| Default Gateway | `10.1.10.1` |
| Preferred DNS | `10.1.10.2` |
| Alternate DNS | `10.1.10.1` |

Configure using the same steps as Windows Server.

---

# 7. Change VM Network to Host-only Adapter

```
Virtual Machine
    → Settings
        → Network
            → Host-only Adapter
```

---

## Why?

Both Windows Server and Windows 10 must be on the **same virtual network** so they can communicate.

Without being on the same network:

- Windows 10 cannot locate the Domain Controller.
- DNS queries will fail.
- Joining the domain will fail.

---

# 8. Join Windows 10 to the Domain

Open:

```
Win + R
```

Run:

```
sysdm.cpl
```

Navigate to:

```
Computer Name
    → Change
```

Select:

```
Domain
```

Enter your domain name created on Windows Server.

Example:

```
company.local
```

Provide credentials of a Domain Administrator.

Example:

```
Administrator
Password: ********
```

Restart Windows.

---

## Verify

On Windows Server:

```
Active Directory Users and Computers
```

Navigate to:

```
Computers
```

You should now see the Windows 10 computer listed there.

---

# 9. Create a Helpdesk User

On Windows Server:

```
Active Directory Users and Computers
```

Create a new user.

Example:

```
Username:
helpdesk
```

Assign a password.

---

# 10. Sign in with the Domain Account

On Windows 10:

Choose:

```
Other User
```

Sign in using:

```
DOMAIN\helpdesk
```

or

```
helpdesk@company.local
```

If login succeeds, the Windows 10 computer is now successfully joined to Active Directory.

---

# Lab Network Topology

```text
                Windows Server 2016
              Domain Controller + DNS
                     10.1.10.2
                          │
                Host-only Network
                          │
                     10.1.10.3
                     Windows 10
                  Domain Client
```

---

# Summary

At the end of this lab, you should be able to:

- Create a Windows 10 virtual machine
- Configure static IP addresses
- Understand why static IPs are necessary in a lab without DHCP
- Use Host-only networking in VirtualBox
- Enable the local Administrator account
- Install RSAT tools
- Join a Windows 10 client to an Active Directory domain
- Verify the computer appears in Active Directory
- Create a Helpdesk domain user
- Log in using a domain account