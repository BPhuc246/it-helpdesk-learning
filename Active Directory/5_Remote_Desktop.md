# Remote Desktop & Remote Assistance (Windows)

## Overview

Windows provides two built-in remote access features:

- **Remote Desktop (RDP)** – Allows one computer to fully control another.
- **Windows Remote Assistance (MSRA)** – Allows another user to view or help control your computer with your permission.

---

# Remote Desktop (RDP)

## Lab Setup

Create two virtual machines:

| VM | Purpose |
|----|---------|
| **desktop1** | Client computer (connects remotely) |
| **desktop2** | Host computer (is being controlled) |

---

## Enable Remote Desktop on desktop2

On **desktop2**:

1. Open **Settings**
2. Navigate to:

```
Settings
└── System
    └── Remote Desktop
```

3. Turn **Remote Desktop** **ON**
4. Click **Confirm** when prompted.

Alternatively:

```
Control Panel
└── System
    └── Remote Settings
```

Enable:

```
Allow remote connections to this computer
```

---

## Find the Computer Name or IP Address

### Option 1 - Computer Name

Open Command Prompt:

```cmd
hostname
```

Example:

```
DESKTOP2
```

---

### Option 2 - IP Address

Run:

```cmd
ipconfig
```

Look for:

```
IPv4 Address
```

Example:

```
192.168.1.25
```

---

## Connect from desktop1

On **desktop1**:

1. Press:

```
Win + R
```

2. Type:

```text
mstsc
```

or search for:

```
Remote Desktop Connection
```

3. Enter:

- Computer Name

or

- IP Address

Example:

```
192.168.1.25
```

4. Click **Connect**

5. Enter the username and password of **desktop2**

Example:

```
Username:
Desktop2\User

Password:
********
```

---

## Result

After a successful connection:

- desktop1 gains full control of desktop2
- desktop2's local user is normally signed out or locked
- Keyboard and mouse input are redirected to desktop1

---

## Common Issues

### Remote Desktop is disabled

Enable it:

```
Settings
→ System
→ Remote Desktop
```

---

### Firewall blocking RDP

Ensure Windows Firewall allows:

```
Remote Desktop
```

---

### Wrong credentials

Verify:

- Username
- Password
- Computer name/IP

---

### Different network

Ensure both VMs can communicate.

Test connectivity:

```cmd
ping <IP_Address>
```

Example:

```cmd
ping 192.168.1.25
```

---

# Windows Remote Assistance (MSRA)

## Overview

Windows Remote Assistance allows another user to:

- View your screen
- Help troubleshoot issues
- Optionally request control

Unlike Remote Desktop:

- The local user stays signed in.
- The local user must approve the connection.

---

## Enable Remote Assistance

Open:

```
Control Panel
└── System and Security
    └── System
        └── Allow remote access
```

Under the **Remote** tab, check:

```
Allow Remote Assistance connections to this computer
```

Click:

```
Apply
OK
```

---

## Launch Remote Assistance

Press:

```
Win + R
```

Run:

```text
msra
```

or search:

```
Windows Remote Assistance
```

---

## Connection Methods

### 1. Invitation File

Choose:

```
Invite someone you trust to help you
```

Create an invitation file:

```
Invitation.msrcIncident
```

Send it to the helper along with the generated password.

The helper opens the file and enters the password to connect.

---

### 2. Easy Connect

Choose:

```
Use Easy Connect
```

Windows generates a connection code.

The helper enters the code to establish the connection.

> **Note:** Easy Connect may not be available on all Windows editions or network configurations.

---

## Remote Assistance Workflow

```
Victim PC
      │
      │ Creates invitation
      ▼
Invitation File / Easy Connect Code
      │
      ▼
Helper connects
      │
      ▼
Victim accepts connection
      │
      ▼
Helper can view desktop
      │
      ▼
(Optional)
Helper requests control
      │
      ▼
Victim approves request
```

---

# Remote Desktop vs Remote Assistance

| Feature | Remote Desktop | Remote Assistance |
|----------|----------------|-------------------|
| Full remote login | ✅ | ❌ |
| Local user remains logged in | ❌ | ✅ |
| User permission required each session | Usually No | Yes |
| Best for | Remote administration | Technical support |
| Uses invitation | ❌ | ✅ |
| Allows screen sharing | ❌ | ✅ |
| Allows remote control | ✅ | ✅ (after approval) |

---

# Useful Commands

Open Remote Desktop Connection:

```cmd
mstsc
```

Open Remote Assistance:

```cmd
msra
```

Show computer name:

```cmd
hostname
```

Show IP address:

```cmd
ipconfig
```

Test network connectivity:

```cmd
ping <IP_Address>
```

Example:

```cmd
ping 192.168.1.25
```

---

# Summary

- **Remote Desktop (RDP)** is intended for full remote administration by logging into another computer.
- **Windows Remote Assistance (MSRA)** is designed for technical support while the remote user remains signed in and grants permission for viewing or control.
- Both tools are built into Windows and require the appropriate remote settings to be enabled before use.