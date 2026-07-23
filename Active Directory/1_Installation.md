# Windows Server 2016 & Active Directory Lab Setup

This guide explains how to install Windows Server 2016 in a virtual machine and configure it as an Active Directory Domain Controller.

## 1. Prerequisites

You need:

* Oracle VirtualBox or VMware
* Windows Server ISO file
* Windows Server 2016 or newer
* At least 4 GB RAM for the virtual machine
* 2 CPU cores
* 40–60 GB disk space

For this guide, Oracle VirtualBox and Windows Server 2016 are used as examples.

---

## 2. Create a Virtual Machine

Open Oracle VirtualBox and select:

`New`

### Virtual Machine Name and Operating System

Configure the following:

* **Name:** Your preferred VM name
* **VM Folder:** Optional. You can change the VM storage location.
* **ISO Image:** Select your Windows Server ISO file.
* **Unattended Installation:** Uncheck `Proceed with Unattended Installation`.

Example:

```text
Name: Windows Server 2016 - Domain Controller
ISO: Windows Server 2016 ISO
```

Disabling unattended installation allows you to manually configure the Windows Server installation process.

### Configure Hardware

For a basic Active Directory lab, use:

```text
Base Memory: 4096 MB
Number of CPUs: 2
```

### Configure Virtual Hard Disk

Set the virtual hard disk size to:

```text
40 GB - 60 GB
```

Recommended:

```text
50 GB
```

Then create the virtual machine.

---

## 3. Install Windows Server

Start the virtual machine and follow the Windows Server installation wizard.

After installation:

1. Complete the Windows Server setup.
2. Log in to Windows Server.
3. Configure a static IP address for the server.

> **Important:** A Domain Controller should use a static IP address because Active Directory relies heavily on DNS.

---

## 4. Install Active Directory Domain Services

Open:

```text
Server Manager
```

Navigate to:

```text
Manage
└── Add Roles and Features
```

Follow the wizard:

```text
Add Roles and Features
        ↓
Next
        ↓
Next
        ↓
Next
```

Select:

```text
Active Directory Domain Services
```

When prompted to install the required features, select:

```text
Add Features
```

Continue through the wizard:

```text
Next
    ↓
Next
    ↓
Next
    ↓
Install
```

Wait until the installation is complete.

---

## 5. Promote the Server to a Domain Controller

After installing Active Directory Domain Services, return to:

```text
Server Manager
```

You should see a notification flag in the top-right corner.

Click:

```text
Promote this server to a domain controller
```

---

## 6. Create a New Active Directory Forest

In the **Deployment Configuration** page, select:

```text
Add a new forest
```

Then enter your **Root domain name**.

For example:

```text
corp.example.com
```

For a local lab environment, you can use:

```text
company.internal
```

---

## 7. Choosing an Active Directory Domain Name

The general domain format is:

```text
<name>.<domain>
```

Examples:

```text
company.internal
corp.example.com
ad.example.com
```

Choosing the correct domain name is important because Active Directory uses DNS to resolve domain resources.

### Avoid Using Existing Public Domains

Do not use a public domain that you do not own.

For example, do not configure your internal Active Directory domain as:

```text
google.com
youtube.com
microsoft.com
```

Using a public domain you do not own can cause DNS conflicts.

For example:

```text
User requests google.com
        ↓
Internal DNS
        ↓
Internal Active Directory DNS
```

The request may be resolved by your internal DNS server instead of reaching the actual public website.

### Avoid `.local` When Possible

It is generally recommended to avoid using:

```text
company.local
```

The `.local` namespace is commonly associated with Multicast DNS (mDNS).

This can potentially cause name-resolution conflicts in environments that use mDNS, including some Apple/macOS, Linux, and network-device environments.

For a new Active Directory deployment, it is generally better to use a domain that you control or a dedicated internal namespace.

> **Important:** Using `.local` does not automatically mean that a Domain Controller will not work. The main concern is potential conflict with mDNS and other name-resolution mechanisms.

---

## 8. Recommended Domain Naming Strategies

### Option 1: Use an Internal Namespace for a Lab

For testing and learning, you can use a private namespace such as:

```text
company.internal
```

or:

```text
company.lan
```

Example:

```text
mycompany.internal
```

This is suitable for a personal Active Directory lab environment.

### Option 2: Use a Subdomain of a Domain You Own

If your organization owns:

```text
example.com
```

You can use:

```text
corp.example.com
```

or:

```text
ad.example.com
```

For example:

```text
Public website:
example.com

Active Directory:
corp.example.com
```

This approach separates your public website from your internal Active Directory environment.

It can also make integration with infrastructure such as the following easier:

* Microsoft 365
* Microsoft Azure
* VPN infrastructure
* Cloud services
* Internal applications

> **Important:** Using a subdomain does not automatically make internal hostnames publicly accessible or automatically provide SSL certificates. DNS and certificate management still need to be configured correctly.

---

## 9. Configure Domain Controller Options

After entering the root domain name, continue through the configuration wizard.

You will configure:

* Domain Controller options
* DNS Server
* Global Catalog
* Directory Services Restore Mode (DSRM) password
* DNS delegation
* Database location
* Log file location
* SYSVOL location

For a basic lab environment, you can generally keep the default settings unless you have a specific requirement.

Set a strong **DSRM password** and save it securely.

Continue through the wizard:

```text
Next
    ↓
Next
    ↓
Next
    ↓
Next
```

Then select:

```text
Install
```

The server will be promoted to a Domain Controller.

---

## 10. Restart the Virtual Machine

After the promotion process is complete, Windows Server will restart.

Wait for the virtual machine to boot again.

---

## 11. Log in After Domain Controller Promotion

After restarting, Windows Server may display the domain in the login screen.

For example:

```text
CORP\Administrator
```

or:

```text
Administrator@corp.example.com
```

You can log in using the domain administrator account created during the Domain Controller setup.

> **Important:** If this is the first Domain Controller in the forest, the `Administrator` account is associated with the Active Directory domain.

---

## 12. Open Active Directory Users and Computers

After logging in, open:

```text
Server Manager
```

Navigate to:

```text
Tools
└── Active Directory Users and Computers
```

You can now manage your Active Directory environment.

From here, you can create and manage:

* Users
* Groups
* Computers
* Organizational Units (OUs)
* Domain resources

Example Active Directory structure:

```text
corp.example.com
│
├── Builtin
├── Computers
├── Domain Controllers
├── Users
│
└── IT
    ├── Users
    ├── Computers
    └── Groups
```

---

## 13. Final Architecture

After completing the setup, your lab environment should look similar to:

```text
┌─────────────────────────────────────┐
│       Windows Server 2016 VM        │
│                                     │
│  ┌───────────────────────────────┐  │
│  │ Active Directory Domain       │  │
│  │ Services (AD DS)              │  │
│  │                               │  │
│  │ Domain: corp.example.com      │  │
│  │                               │  │
│  │ DNS Server                    │  │
│  │ Global Catalog                │  │
│  │ Domain Controller             │  │
│  └───────────────────────────────┘  │
│                                     │
└─────────────────────────────────────┘
                  │
                  ▼
       Active Directory Clients
                  │
        ┌─────────┴─────────┐
        ▼                   ▼
   Windows 10/11        Windows 10/11
      Client                Client
```

---

## 14. Quick Summary

The complete process is:

```text
Install VirtualBox / VMware
        ↓
Download Windows Server ISO
        ↓
Create Virtual Machine
        ↓
Configure RAM, CPU, and Disk
        ↓
Install Windows Server
        ↓
Set Static IP Address
        ↓
Open Server Manager
        ↓
Install Active Directory Domain Services
        ↓
Promote Server to Domain Controller
        ↓
Create a New Forest
        ↓
Configure Root Domain Name
        ↓
Set DSRM Password
        ↓
Install
        ↓
Restart Server
        ↓
Login as Domain Administrator
        ↓
Server Manager
        ↓
Tools
        ↓
Active Directory Users and Computers
```
