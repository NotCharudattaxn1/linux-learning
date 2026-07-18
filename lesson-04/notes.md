# Linux Lesson 4: File Permissions

## Objective

Understand Linux file permissions, ownership, and access control. Learn how to manage permissions using `chmod`, `chown`, and `chgrp`, and understand their importance in Linux administration, DevOps, and Cybersecurity.

---

# Why are File Permissions Important?

Linux is a multi-user operating system. Multiple users can access the same system simultaneously.

File permissions ensure that:
- Only authorized users can access sensitive files.
- Unauthorized users cannot modify or delete important data.
- System security is maintained.
- Applications can only perform permitted operations.

Example:

A company's database configuration file should only be accessible by the administrator, not by every user on the system.

---

# Every File Has Three Things

Every file and directory in Linux has:

1. Owner (User)
2. Group
3. Others

Example:

```bash
-rwxr-xr-- 1 max developers 2500 Jul 18 notes.txt
```

Breakdown:

```
-rwxr-xr--
│││ │ │
│││ │ └── Others
│││ └──── Group
│└──────── Owner
└───────── File Type
```

---

# File Types

| Symbol | Meaning |
|---------|----------|
| - | Regular File |
| d | Directory |
| l | Symbolic Link |
| c | Character Device |
| b | Block Device |

Example:

```
-rw-r--r--
```

The first character is:

```
-
```

Meaning:

Regular File

---

# Permission Types

There are three basic permissions.

## Read (r)

Symbol:

```
r
```

Meaning:

Allows viewing the contents of a file.

For directories:

Allows listing files inside the directory.

---

## Write (w)

Symbol:

```
w
```

Meaning:

Allows modifying the contents of a file.

For directories:

Allows creating, deleting, and renaming files.

---

## Execute (x)

Symbol:

```
x
```

Meaning:

Allows executing a file as a program or script.

For directories:

Allows entering the directory using `cd`.

---

# Understanding Permission Groups

Example:

```
-rwxr-xr--
```

Split it into groups:

```
Owner  Group  Others

rwx    r-x    r--
```

Meaning:

Owner:

- Read
- Write
- Execute

Group:

- Read
- Execute

Others:

- Read only

---

# Viewing Permissions

Command:

```bash
ls -l
```

Example Output:

```bash
-rwxr-xr-- 1 max developers 1500 Jul 18 script.sh
```

This displays:

- File permissions
- Owner
- Group
- File size
- Modification date
- File name

---

# Numeric Permissions

Each permission has a numeric value.

| Permission | Value |
|------------|------:|
| Read (r) | 4 |
| Write (w) | 2 |
| Execute (x) | 1 |

Examples:

```
rwx
```

Calculation:

```
4 + 2 + 1 = 7
```

So:

```
rwx = 7
```

---

Examples:

```
rw-
```

```
4 + 2 = 6
```

```
r-x
```

```
4 + 1 = 5
```

```
r--
```

```
4
```

```
---
```

```
0
```

---

# Common Permission Values

| Number | Permission | Meaning |
|---------|------------|----------|
| 777 | rwxrwxrwx | Everyone has full access (Avoid unless necessary) |
| 755 | rwxr-xr-x | Owner has full access, others can read and execute |
| 700 | rwx------ | Only owner has access |
| 644 | rw-r--r-- | Owner can modify, others can only read |
| 600 | rw------- | Private file, only owner can read/write |

---

# Changing Permissions

Command:

```bash
chmod
```

Example:

```bash
chmod 755 script.sh
```

Meaning:

```
Owner  -> 7 -> rwx
Group  -> 5 -> r-x
Others -> 5 -> r-x
```

---

Another example:

```bash
chmod 644 notes.txt
```

Result:

```
rw-r--r--
```

---

# Symbolic Method

Grant execute permission:

```bash
chmod +x script.sh
```

Remove execute permission:

```bash
chmod -x script.sh
```

Add write permission for the owner:

```bash
chmod u+w file.txt
```

Remove write permission for the group:

```bash
chmod g-w file.txt
```

Permission Symbols:

| Symbol | Meaning |
|---------|----------|
| u | User (Owner) |
| g | Group |
| o | Others |
| a | All Users |

---

# Changing File Owner

Command:

```bash
sudo chown username filename
```

Example:

```bash
sudo chown max report.txt
```

---

# Changing Group

Command:

```bash
sudo chgrp groupname filename
```

Example:

```bash
sudo chgrp developers project.cpp
```

---

# Why Permissions Matter in Cybersecurity

Incorrect permissions can lead to:

- Data leakage
- Privilege escalation
- Unauthorized modification
- Malware execution
- System compromise

Example:

If a sensitive configuration file is set to `777`, any user on the system could modify it, creating a serious security risk.

---

# Best Practices

- Follow the Principle of Least Privilege (give only the permissions that are necessary).
- Avoid using `777` unless absolutely required.
- Use `644` for most text files.
- Use `755` for executable scripts and directories when appropriate.
- Use `600` for sensitive files such as SSH private keys.
- Regularly audit file permissions on important systems.

---

# Common Mistakes

❌ Using `777` for every file.

❌ Forgetting to make a script executable before running it.

❌ Confusing the owner with the group.

❌ Giving write permission to everyone.

---

# Real-world Usage

- Web servers use permissions to control website files.
- SSH private keys require restrictive permissions (commonly `600`).
- DevOps engineers configure permissions for deployment scripts.
- System administrators secure configuration files using appropriate ownership and permissions.

---

# Interview Questions

1. What are Linux file permissions?
2. Explain the meaning of `r`, `w`, and `x`.
3. What is the difference between `644` and `755`?
4. What does `chmod` do?
5. What is the difference between `chown` and `chgrp`?
6. Why should `777` generally be avoided?
7. Explain the Principle of Least Privilege.
8. How do directory permissions differ from file permissions?

---

# Hands-on Lab

```bash
mkdir permission_lab

cd permission_lab

touch notes.txt

ls -l

chmod 644 notes.txt

ls -l

chmod 755 notes.txt

ls -l

chmod +x notes.txt

ls -l
```

Observe how the permission string changes after each command.

---

# Mini Challenge

1. Create a file named `script.sh`.
2. Make it executable.
3. Change its permission to `700`.
4. Verify the permissions using `ls -l`.
5. Explain why `700` is suitable for some scripts.

---

# Summary

- Linux permissions control access to files and directories.
- Every file has an owner, a group, and permissions for others.
- Permission types are Read (r), Write (w), and Execute (x).
- `chmod` changes permissions.
- `chown` changes the owner.
- `chgrp` changes the group.
- Common permissions include `644`, `755`, `700`, and `600`.
- Avoid `777` unless there is a clear and justified reason.
- Proper permissions are a key part of Linux security.
