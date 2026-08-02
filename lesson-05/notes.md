# Linux Lesson 5: Users and Groups

## Objective

Understand how Linux manages users and groups, how ownership works, and why user management is one of the most important aspects of Linux administration and cybersecurity.

---

# Introduction

Linux is a **multi-user operating system**, meaning multiple users can access the same system simultaneously.

Each user has:
- A unique username
- A User ID (UID)
- A home directory
- A default shell
- One primary group
- Zero or more secondary groups

Linux uses users and groups to control access to files, directories, and system resources.

---

# Why Users and Groups Exist

Imagine a company with:

- HR Department
- Developers
- Finance Department
- System Administrators

Not everyone should have access to every file.

For example:

- Developers should not access payroll records.
- HR should not modify server configuration.
- Guests should not install software.

Linux solves this problem using **users, groups, and permissions**.

---

# Types of Users

Linux mainly has three types of users.

## 1. Root User

The root user is the superuser (administrator).

Characteristics:

- UID = 0
- Full control over the entire system
- Can access every file
- Can install software
- Can create and delete users
- Can modify system configuration

Example:

```bash
whoami
```

Output:

```text
root
```

**Warning:** Avoid using the root account for daily tasks. Perform administrative actions with `sudo` whenever possible.

---

## 2. Regular User

A regular user is created for everyday work.

Examples:

- Writing code
- Browsing the web
- Creating files
- Running applications

A regular user has limited permissions and cannot perform administrative tasks without elevated privileges.

Example:

```bash
whoami
```

Output:

```text
max
```

---

## 3. System User

System users are created automatically by Linux or installed services.

Examples:

- nginx
- mysql
- www-data
- nobody

These accounts are used to run services securely instead of running everything as the root user.

This improves security because if a service is compromised, the attacker has fewer privileges.

---

# User ID (UID)

Every user has a unique numeric identifier called a **User ID (UID)**.

View your UID:

```bash
id
```

Example:

```text
uid=1000(max) gid=1000(max) groups=1000(max),27(sudo)
```

Meaning:

- UID = 1000
- Username = max

Special values:

| UID | Meaning |
|------|---------|
| 0 | Root user |
| 1–999 | System users (distribution-dependent) |
| 1000+ | Regular users (common default on many distributions) |

---

# Groups

A group is a collection of users.

Instead of assigning permissions to every individual user, permissions can be assigned to a group.

Example:

Developers:

```
alice
bob
charlie
```

All three belong to:

```
developers
```

Granting permissions to the `developers` group allows every member to access the required resources.

---

# Primary Group

Every user belongs to one primary group.

Example:

```
User:

max

Primary Group:

max
```

The primary group is usually assigned when the user account is created.

---

# Secondary Groups

A user may belong to multiple additional groups.

Example:

```
User:

max

Groups:

developers
docker
sudo
```

This allows the user to perform additional tasks without giving unnecessary permissions.

---

# Viewing Groups

View the groups of the current user:

```bash
groups
```

Example:

```text
max sudo docker developers
```

---

# The `id` Command

Displays:

- UID
- GID
- Groups

Example:

```bash
id
```

Output:

```text
uid=1000(max)
gid=1000(max)
groups=1000(max),27(sudo),998(docker)
```

---

# Important Files

Linux stores user and group information in specific system files.

---

## /etc/passwd

Stores basic information about users.

Each line represents one user.

Example:

```text
max:x:1000:1000:Max:/home/max:/bin/bash
```

Fields:

```
Username
Password Placeholder
UID
GID
Description
Home Directory
Default Shell
```

Passwords are **not** stored here.

---

## /etc/shadow

Stores encrypted password information.

Only privileged users can read this file.

Example:

```text
max:$y$j9T$...
```

Never edit this file manually unless you know exactly what you are doing.

---

## /etc/group

Contains information about groups.

Example:

```text
developers:x:1001:max
```

Fields:

```
Group Name
Password Placeholder
GID
Members
```

---

# Important Commands

## whoami

Displays the current logged-in user.

```bash
whoami
```

---

## id

Displays user and group information.

```bash
id
```

---

## groups

Shows all groups of the current user.

```bash
groups
```

---

## passwd

Changes the user's password.

```bash
passwd
```

---

## sudo

Executes a command with administrative privileges.

Example:

```bash
sudo apt update
```

---

## su

Switches to another user.

Example:

```bash
su username
```

Switch to the root user (if enabled):

```bash
su -
```

---

# Principle of Least Privilege

Users should only receive the permissions necessary to perform their tasks.

Example:

A web server should not run as the root user.

Instead, it should run as:

```
www-data
```

This limits the damage if the service is compromised.

---

# Why This Matters in Cybersecurity

Improper user management can lead to:

- Privilege Escalation
- Unauthorized Access
- Data Theft
- System Compromise
- Insider Threats

Attackers often attempt to gain root privileges after compromising a system.

Restricting permissions helps reduce the impact of such attacks.

---

# Best Practices

- Use a regular account for daily work.
- Use `sudo` only when required.
- Avoid logging in directly as the root user.
- Follow the Principle of Least Privilege.
- Review user accounts regularly.
- Remove unused accounts.
- Use strong passwords.

---

# Common Mistakes

❌ Using the root account for everyday tasks.

❌ Giving every user sudo access.

❌ Sharing user accounts.

❌ Leaving unused accounts active.

❌ Running services as the root user.

---

# Real-World Usage

- Web servers often run as `www-data`.
- Database servers use dedicated accounts like `mysql` or `postgres`.
- Docker users are commonly added to the `docker` group.
- Administrators receive `sudo` privileges instead of using the root account directly.

---

# Interview Questions

1. What is a multi-user operating system?
2. What is the difference between the root user and a regular user?
3. What is a UID?
4. What is the difference between a primary group and a secondary group?
5. What is the purpose of `/etc/passwd`?
6. Why are passwords stored in `/etc/shadow`?
7. What does the `id` command display?
8. Why should services not run as the root user?
9. Explain the Principle of Least Privilege.
10. What is the purpose of `sudo`?

---

# Hands-on Lab

Run the following commands:

```bash
whoami

id

groups

cat /etc/passwd

cat /etc/group

sudo cat /etc/shadow
```

Observe:

- Your username
- Your UID
- Your groups
- The structure of each file

**Note:** Reading `/etc/shadow` requires administrative privileges.

---

# Mini Challenge

1. Find your UID.
2. Find your primary group.
3. List all the groups you belong to.
4. Explain the difference between `sudo` and `su`.
5. Why should applications like Nginx or MySQL avoid running as the root user?

---

# Summary

- Linux is a multi-user operating system.
- Every user has a unique UID.
- Users belong to one primary group and can belong to multiple secondary groups.
- Important files include `/etc/passwd`, `/etc/shadow`, and `/etc/group`.
- `whoami`, `id`, `groups`, `sudo`, `su`, and `passwd` are essential user-management commands.
- Running services as non-root users improves system security.
- The Principle of Least Privilege is a fundamental security practice.
