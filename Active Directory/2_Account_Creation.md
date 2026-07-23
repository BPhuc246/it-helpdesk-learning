# Active Directory User Management

This guide explains how to search for users, enable the Active Directory Recycle Bin, create user accounts, copy existing users, and view user information using the command line.

---

# 1. Search for a User in Active Directory

You can search for users and other Active Directory objects using **Active Directory Users and Computers (ADUC)**.

### Steps

1. Open **Server Manager**.
2. Select **Tools** from the top-right menu.
3. Open **Active Directory Users and Computers**.
4. Expand your domain.
5. Right-click the location where you want to search, for example:

```text
your-domain
└── Computers
```

6. Select:

```text
Find
```

7. Enter the name of the user or computer you want to search for.

For example, searching for:

```text
guest
```

will display matching Active Directory objects with the name `guest`.

> **Note:** The search results may include multiple objects if more than one account or object has a matching name.

---

# 2. Enable the Active Directory Recycle Bin

The **Active Directory Recycle Bin** allows administrators to restore deleted Active Directory objects, such as user accounts and groups.

By default, the Active Directory Recycle Bin is disabled.

### Steps

1. Open the Windows Start menu.
2. Search for:

```text
Windows Administrative Tools
```

3. Open:

```text
Active Directory Administrative Center
```

4. In the left panel, select your domain.

For example:

```text
your-domain (local)
```

5. Right-click your domain.
6. Select:

```text
Enable Recycle Bin
```

7. Confirm the operation.
8. Refresh the Active Directory Administrative Center.

The Active Directory Recycle Bin is now enabled.

> **Important:** Once enabled, the Active Directory Recycle Bin cannot be disabled.

---

## Why Enable the Active Directory Recycle Bin?

The Recycle Bin is useful because it allows administrators to recover deleted Active Directory objects.

It is especially useful for:

* Learning Active Directory administration.
* Testing administrative operations.
* Testing scripts that modify Active Directory.
* Recovering accidentally deleted users or groups.
* Reducing the risk of permanent data loss.

For a lab environment, enabling the Recycle Bin is highly recommended.

---

# 3. Create a New Active Directory User

You can create a new user account using **Active Directory Users and Computers (ADUC)**.

### Steps

1. Open:

```text
Server Manager
```

2. Navigate to:

```text
Tools
└── Active Directory Users and Computers
```

3. Expand your domain.
4. Select the `Users` container or another appropriate Organizational Unit (OU).
5. Right-click the location.
6. Select:

```text
New
└── User
```

7. Enter the user's information.
8. Set a username and password.
9. Configure the required account options.
10. Finish the wizard.

The new user account will now appear in the selected container or OU.

Example:

```text
your-domain
│
└── Users
    ├── Administrator
    ├── Guest
    └── John.Doe
```

---

# 4. Create a User by Copying an Existing User

If you need to create a new user with similar settings to an existing user, you can copy an existing Active Directory account.

This is useful when multiple users need similar permissions or group memberships.

### Steps

1. Open:

```text
Server Manager
```

2. Navigate to:

```text
Tools
└── Active Directory Users and Computers
```

3. Open the appropriate container or OU.
4. Find the existing user account you want to copy.
5. Right-click the user.
6. Select:

```text
Copy
```

7. Enter the information for the new user.
8. Set the new user's password.
9. Complete the wizard.

The new account will inherit many of the original user's settings, including their **group memberships**.

For example:

```text
Original User:
Administrator
    ├── Domain Admins
    ├── Administrators
    └── Other Groups

Copy:
New User
    ├── Domain Admins
    ├── Administrators
    └── Other Groups
```

> **Important:** Copying an account can give the new user the same group memberships and permissions as the original account. Be careful when copying privileged accounts such as `Administrator`.

> **Best Practice:** Do not copy highly privileged accounts unless you specifically need the same permissions. Instead, create a normal user account and add only the required groups.

---

# 5. View Active Directory User Information Using Command Prompt

You can use the `net user` command to view information about a domain user.

Use the following command:

```cmd
net user <username> /domain
```

Example:

```cmd
net user guest /domain
```

The command can display information such as:

* User name
* Full name
* Account status
* Account expiration
* Password information
* Last logon
* Password expiration
* Workstations allowed
* Local group memberships
* Global group memberships

Example:

```text
User name                    guest
Account active               Yes
Account expires              Never
Password last set            ...
Password expires             ...
Last logon                   ...
Local Group Memberships      ...
Global Group memberships     ...
```

> **Note:** The command must be executed in a context where the domain can be contacted. If the computer is not connected to the domain or the domain controller is unavailable, the command may fail.

---

# 6. Quick Summary

| Task                  | Location / Command                                                                                              |
| --------------------- | --------------------------------------------------------------------------------------------------------------- |
| Search for a user     | Server Manager → Tools → Active Directory Users and Computers → Right-click container → Find                    |
| Enable Recycle Bin    | Windows Administrative Tools → Active Directory Administrative Center → Right-click domain → Enable Recycle Bin |
| Create a new user     | Active Directory Users and Computers → Users/OU → New → User                                                    |
| Copy an existing user | Right-click existing user → Copy                                                                                |
| View user information | `net user <username> /domain`                                                                                   |

---

# 7. Useful Commands

### View a Domain User

```cmd
net user <username> /domain
```

Example:

```cmd
net user guest /domain
```

### View All Local Users

```cmd
net user
```

### View Local User Information

```cmd
net user <username>
```

Example:

```cmd
net user administrator
```
