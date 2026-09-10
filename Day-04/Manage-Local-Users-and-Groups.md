# Chapter — Manage Local Users and Groups

This chapter covers how Linux manages **local user accounts, groups, passwords, and administrative privileges**.

The goal is to understand not only the commands, but also how Linux represents users internally and how administrators safely control access to the system.

---

## Contents

- [1. User Accounts and Identity](#1-user-accounts-and-identity)
  - [UID and GID](#uid-and-gid)
  - [Regular vs System Users](#regular-vs-system-users)
  - [Local User and Group Files](#local-user-and-group-files)
- [2. Creating and Managing Users](#2-creating-and-managing-users)
  - [`useradd`](#useradd)
  - [`usermod`](#usermod)
  - [`userdel`](#userdel)
- [3. Managing Groups](#3-managing-groups)
  - [`groupadd`](#groupadd)
  - [`groupmod`](#groupmod)
  - [`groupdel`](#groupdel)
- [4. Password Management and Aging](#4-password-management-and-aging)
  - [`passwd`](#passwd)
  - [`chage`](#chage)
- [5. Privilege Escalation](#5-privilege-escalation)
  - [`sudo`](#sudo)
  - [`visudo`](#visudo)
  - [`/etc/sudoers`](#etcsudoers)
  - The `wheel` group
- [6. Useful Verification Commands](#6-useful-verification-commands)
- [7. Practical Lab Workflow](#7-practical-lab-workflow)
- [8. Quick Reference](#8-quick-reference)

---

# 1. User Accounts and Identity

Linux is a multi-user operating system. Instead of identifying users only by their usernames, the system internally identifies them using numerical IDs.

## UID and GID

### UID — User ID

A **UID (User ID)** is a numerical identifier assigned to a user account.

For example:

```console
[user@host ~]$ id alice
uid=1001(alice) gid=1001(alice) groups=1001(alice)
```

Here:

```text
uid=1001(alice)
```

means that the user `alice` has UID `1001`.

Linux uses the UID internally when checking ownership and permissions.

### GID — Group ID

A **GID (Group ID)** identifies a group.

In:

```text
gid=1001(alice)
```

the user's primary group is `alice`, whose GID is `1001`.

A user can also belong to additional groups:

```console
[user@host ~]$ id alice
uid=1001(alice) gid=1001(alice) groups=1001(alice),1002(developers)
```

This means:

- UID `1001` → user `alice`
- GID `1001` → primary group `alice`
- GID `1002` → supplementary group `developers`

---

## Regular vs System Users

Linux commonly distinguishes between **regular users** and **system users**.

### Regular users

Regular users are created for people who need to log in and work on the system.

They commonly have:

- A home directory such as `/home/alice`
- A login shell such as `/bin/bash`
- A UID in the normal user range

### System users

System users are generally used by services and applications rather than human users.

Examples can include accounts associated with services such as web servers, databases, or other system processes.

Their purpose is to allow services to run with a dedicated identity instead of running everything as `root`.

---

# 2. Local User and Group Files

Linux stores local account information in several important files.

## `/etc/passwd`

Contains basic information about local user accounts.

Example:

```console
[user@host ~]$ cat /etc/passwd
alice:x:1001:1001::/home/alice:/bin/bash
```

The fields are separated by `:`.

The general structure is:

```text
username : password-placeholder : UID : GID : GECOS : home-directory : shell
```

Example:

```text
alice : x : 1001 : 1001 : : /home/alice : /bin/bash
```

The `x` does not contain the actual password. Modern Linux systems store password hashes in `/etc/shadow`.

---

## `/etc/shadow`

Contains password-related information for local users, including password hashes and password-aging information.

Because this file contains sensitive authentication information, access to it is restricted.

You may see it with:

```bash
sudo cat /etc/shadow
```

Avoid modifying it directly unless you specifically know what you are doing. Use tools such as `passwd` and `chage` instead.

---

## `/etc/group`

Contains information about local groups and their members.

Example:

```console
[user@host ~]$ getent group developers
developers:x:1002:alice
```

The general structure is:

```text
group-name : password-placeholder : GID : members
```

---

## `getent`

`getent` retrieves entries from configured system databases.

For example:

```bash
getent passwd alice
```

Find a user entry.

```bash
getent group developers
```

Find a group entry.

This is often preferable to directly reading `/etc/passwd` or `/etc/group` because it uses the system's configured name-service sources.

---

# 3. Creating and Managing Users

## `useradd`

Creates a new local user account.

Basic syntax:

```bash
sudo useradd username
```

Example:

```bash
sudo useradd alice
```

You can then verify the account:

```bash
id alice
```

Depending on the system configuration and options used, additional account properties such as the home directory and login shell can be specified explicitly.

For example:

```bash
sudo useradd -m -s /bin/bash alice
```

Here:

```text
-m           → create the user's home directory
-s /bin/bash → set the login shell
```

Set a password separately:

```bash
sudo passwd alice
```

---

## `usermod`

Modifies an existing user account.

One common task is adding a user to a supplementary group.

```bash
sudo usermod -aG developers alice
```

Meaning:

```text
-a → append; keep existing supplementary groups
-G → specify supplementary group(s)
```

### Important

Be careful with:

```bash
usermod -G
```

without `-a`.

It can replace the user's existing supplementary group memberships.

Verify the result:

```bash
id alice
```

---

## `userdel`

Deletes a user account.

```bash
sudo userdel alice
```

To remove the user's home directory as well:

```bash
sudo userdel -r alice
```

The `-r` option removes the user's home directory and mail spool.

Always verify that important user data has been backed up before deleting an account.

---

# 4. Managing Groups

Groups are used to organize users and manage access to resources.

For example, instead of giving permissions to several users individually, an administrator can create a group and assign the required permissions to that group.

---

## `groupadd`

Creates a new group.

```bash
sudo groupadd developers
```

Verify:

```bash
getent group developers
```

---

## `groupmod`

Modifies an existing group.

For example, to rename a group:

```bash
sudo groupmod -n developers-dev developers
```

The group keeps its identity while its name changes.

---

## `groupdel`

Deletes a group.

```bash
sudo groupdel developers
```

The group should not be the primary group of an existing user when you attempt to remove it.

---

# 5. Password Management and Aging

## `passwd`

Changes a user's password.

For the current user:

```bash
passwd
```

For another user, an administrator can use:

```bash
sudo passwd alice
```

---

## `chage`

`chage` manages password-aging information.

View the current password-aging settings:

```bash
sudo chage -l alice
```

Example output:

```text
Last password change                                    : Apr 27, 2025
Password expires                                        : never
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 0
Maximum number of days between password change          : 99999
Number of days of warning before password expires       : 7
```

Important options include:

| Option | Purpose |
|---|---|
| `-l` | List current password-aging information |
| `-m` | Set minimum days between password changes |
| `-M` | Set maximum days before password expires |
| `-W` | Set warning days before expiration |
| `-I` | Set inactivity period after password expiration |
| `-E` | Set account expiration date |

Example:

```bash
sudo chage -M 90 alice
```

This sets the maximum password age to 90 days.

Example:

```bash
sudo chage -W 7 alice
```

This gives the user a 7-day warning before password expiration.

---

# 6. Privilege Escalation

Some administrative operations require elevated privileges.

Instead of logging in directly as `root`, Linux commonly uses `sudo` to allow authorized users to execute specific commands with elevated privileges.

---

## `sudo`

Basic syntax:

```bash
sudo command
```

Example:

```bash
sudo useradd alice
```

The command is executed with elevated privileges if the current user is authorized.

Another example:

```bash
sudo passwd alice
```

This allows an authorized administrator to change `alice`'s password.

You can verify the effective user for a command with:

```bash
sudo whoami
```

Expected output:

```text
root
```

---

# 7. `/etc/sudoers`

The `/etc/sudoers` file defines which users and groups can use `sudo` and what commands they are allowed to run.

A common RHEL configuration gives members of the `wheel` group administrative privileges.

Example:

```text
%wheel ALL=(ALL) ALL
```

This can be read as:

```text
%wheel     → members of the wheel group
ALL        → on all hosts
(ALL)      → as any user
ALL        → all commands
```

Do not casually edit `/etc/sudoers` with a normal text editor. A syntax mistake can break administrative access.

---

# 8. `visudo`

`visudo` is the recommended tool for editing the sudoers configuration.

Run:

```bash
sudo visudo
```

On many RHEL systems, this opens the configuration in an editor such as Vim or Nano.

The major advantage of `visudo` is that it checks the sudoers syntax before accepting the changes.

This helps prevent accidentally creating an invalid sudo configuration.

---

## The `wheel` Group

On RHEL systems, the `wheel` group is commonly used for administrative access.

Check the group:

```bash
getent group wheel
```

Add a user to the group:

```bash
sudo usermod -aG wheel alice
```

Verify:

```bash
id alice
```

The output should include `wheel` among the user's supplementary groups.

After the user starts a new login session, an authorized user can use:

```bash
sudo command
```

---

# 9. Understanding the Sudoers Example

A typical line is:

```text
%wheel ALL=(ALL) ALL
```

Compare it with a user-specific rule:

```text
alice ALL=(ALL) ALL
```

The difference is:

```text
%wheel
```

refers to the **group** `wheel`, while:

```text
alice
```

refers directly to the **user** `alice`.

A more restricted sudo rule can specify particular commands rather than allowing everything.

The important principle is:

> Give users only the administrative access they actually need.

---

# 10. Useful Verification Commands

After changing users or groups, verify the configuration instead of assuming it worked.

### Check the current user

```bash
whoami
```

### Display UID, GID, and group memberships

```bash
id alice
```

### Check a user in the passwd database

```bash
getent passwd alice
```

### Check a group

```bash
getent group developers
```

### Check password aging

```bash
sudo chage -l alice
```

### Check group membership

```bash
groups alice
```

### Check sudo configuration

```bash
sudo visudo
```

### Test sudo access

```bash
sudo whoami
```

Expected:

```text
root
```

---

# 11. Practical Lab Workflow

A useful way to practice this chapter is to create a test user and manage it through the complete lifecycle.

## Step 1 — Create a group

```bash
sudo groupadd developers
```

## Step 2 — Create a user

```bash
sudo useradd -m alice
```

## Step 3 — Set a password

```bash
sudo passwd alice
```

## Step 4 — Add the user to the group

```bash
sudo usermod -aG developers alice
```

## Step 5 — Verify identity and groups

```bash
id alice
```

## Step 6 — Check the group database

```bash
getent group developers
```

## Step 7 — Check password aging

```bash
sudo chage -l alice
```

## Step 8 — Configure administrative access

If the lab requires `wheel` access:

```bash
sudo usermod -aG wheel alice
```

Then verify:

```bash
id alice
```

## Step 9 — Test sudo access

After starting a new login session as the user:

```bash
sudo whoami
```

Expected:

```text
root
```

---

# 12. Important Differences

## `useradd` vs `usermod` vs `userdel`

| Command | Purpose |
|---|---|
| `useradd` | Create a user |
| `usermod` | Modify a user |
| `userdel` | Delete a user |

## `groupadd` vs `groupmod` vs `groupdel`

| Command | Purpose |
|---|---|
| `groupadd` | Create a group |
| `groupmod` | Modify a group |
| `groupdel` | Delete a group |

## `passwd` vs `chage`

```text
passwd
```

Primarily manages passwords.

```text
chage
```

Manages password-aging and expiration policies.

## `sudo` vs `visudo`

```text
sudo
```

Runs a command with elevated privileges.

```text
visudo
```

Safely edits and validates sudoers configuration.

---

# 13. Visual Lab Notes

The repository can include terminal screenshots next to the commands they demonstrate.

For example:

![User creation and identity verification](images/user-management-lab.png)

The screenshot demonstrates a typical workflow:

```text
Create user
    ↓
Verify UID/GID
    ↓
Create group
    ↓
Add user to group
    ↓
Check password aging
    ↓
Configure sudo access
    ↓
Verify administrative privileges
```

For your actual labs, it is useful to capture screenshots of **your own RHEL VM** after successfully completing important tasks. This makes the repository a record of what you actually practiced rather than only a collection of copied commands.

---

# 14. Quick Reference

| Command | Purpose |
|---|---|
| `id` | Display UID, GID, and group memberships |
| `whoami` | Display current user |
| `getent passwd` | Query user account information |
| `getent group` | Query group information |
| `useradd` | Create a user |
| `usermod` | Modify a user |
| `userdel` | Delete a user |
| `groupadd` | Create a group |
| `groupmod` | Modify a group |
| `groupdel` | Delete a group |
| `passwd` | Set/change a password |
| `chage` | Manage password aging |
| `sudo` | Run a command with elevated privileges |
| `visudo` | Safely edit sudoers configuration |
| `/etc/passwd` | Local user account information |
| `/etc/shadow` | Password hashes and aging information |
| `/etc/group` | Local group information |
| `/etc/sudoers` | Sudo authorization rules |

---

# Key Takeaways

By the end of this chapter, you should be able to:

- Explain the difference between a **username, UID, group, and GID**.
- Understand the purpose of `/etc/passwd`, `/etc/shadow`, and `/etc/group`.
- Create, modify, and remove local users.
- Create, modify, and remove local groups.
- Add users to supplementary groups safely using `usermod -aG`.
- Inspect password-aging policies with `chage`.
- Understand why `sudo` is used instead of routinely working as `root`.
- Explain the purpose of `/etc/sudoers` and `visudo`.
- Understand the role of the `wheel` group in RHEL administrative access.
- Verify account and permission changes using `id`, `getent`, `groups`, and `chage`.

The main idea of this chapter is that **Linux access control starts with identity**: users have UIDs, groups have GIDs, files and processes use those identities, and tools such as `sudo` build controlled administrative access on top of them.
