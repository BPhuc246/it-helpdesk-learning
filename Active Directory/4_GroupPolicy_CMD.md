# Organizational Unit (OU)

## Grant Helpdesk Permission

By default, the **helpdesk** account cannot create Organizational Units (OUs) or manage Active Directory objects.

To give the account administrative privileges in this lab:

1. Open **Active Directory Users and Computers** on **Windows Server 2016**.
2. Navigate to:

```
Users
    → Domain Admins
```

3. Double-click **Domain Admins**.
4. Open the **Members** tab.
5. Click **Add**.
6. Add the **helpdesk** account.
7. Click **Apply** → **OK**.

> **Note**
>
> After adding the account to **Domain Admins**, sign out of Windows 10 and sign back in so the new permissions take effect.

---

## Create an Organizational Unit (OU)

After signing back in as **helpdesk** on Windows 10:

1. Open **Active Directory Users and Computers**.
2. Right-click the domain.
3. Select:

```
New
    → Organizational Unit
```

4. Enter the OU name.
5. Click **OK**.

---

# Group Policy

Group Policy allows administrators to centrally manage Windows computers and user accounts within an Active Directory domain.

---

## 1. View the Group Policy Applied to a Specific User

Open **Active Directory Users and Computers**.

Right-click the desired user.

Choose:

```
All Tasks
    → Resultant Set of Policy (Logging)
```

Click:

```
Next
    → Next
        → Finish
```

This displays the Group Policy settings currently applied to that user.

---

## 2. Open Group Policy Management

On **Windows Server 2016**:

```
Server Manager
    → Tools
        → Group Policy Management
```

Expand:

```
Forest
    → Domains
        → <Your Domain>
            → Default Domain Policy
```

---

# Account Lockout Policy

## What is Account Lockout Policy?

An **Account Lockout Policy** is a security feature that automatically locks a user account after a specified number of incorrect password attempts.

This helps protect user accounts from brute-force password attacks.

---

## Configure Account Lockout Policy

Right-click:

```
Default Domain Policy
```

Choose:

```
Edit
```

Navigate to:

```
Computer Configuration
    → Policies
        → Windows Settings
            → Security Settings
                → Account Policies
                    → Account Lockout Policy
```

Common settings include:

| Policy | Description |
|---------|-------------|
| Account lockout threshold | Number of failed login attempts before the account is locked. |
| Account lockout duration | How long the account remains locked before it is automatically unlocked. |
| Reset account lockout counter after | Time before the failed login counter is reset. |

Example:

| Setting | Value |
|---------|-------|
| Account lockout threshold | 5 attempts |
| Account lockout duration | 15 minutes |
| Reset account lockout counter after | 15 minutes |

---

# Password Policy

Within the same location:

```
Computer Configuration
    → Policies
        → Windows Settings
            → Security Settings
                → Account Policies
                    → Password Policy
```

You can configure:

- Minimum password length
- Maximum password age
- Minimum password age
- Password complexity requirements
- Password history
- Store passwords using reversible encryption

These policies are automatically applied to all domain users unless overridden by another Group Policy Object (GPO).

---

# Summary

After completing this section, you should be able to:

- Add the **helpdesk** account to **Domain Admins** (lab environment).
- Create Organizational Units (OUs) from Windows 10 using RSAT.
- Open and manage **Group Policy Management**.
- View the Resultant Set of Policy (RSoP) for a user.
- Configure **Account Lockout Policies**.
- Configure **Password Policies** for the domain.