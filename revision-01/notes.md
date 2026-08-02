# Linux Fundamentals Revision Sheet
## Complete Revision (Lessons 1–10)

> **Course:** Linux for DevSecOps
> **Revision Time:** 60–90 Minutes
> **Level:** Beginner → Intermediate

---

# Learning Checklist

By this point, you should be comfortable with:

- [ ] Navigating the Linux terminal
- [ ] Understanding the Linux file system
- [ ] Working with files and directories
- [ ] Managing permissions
- [ ] Managing users and groups
- [ ] Monitoring processes
- [ ] Installing software with APT
- [ ] Working with environment variables
- [ ] Using Linux networking commands
- [ ] Writing basic Bash scripts

---

# Lesson 1 — Linux Basics

## Linux

- Open-source operating system kernel.
- Multi-user operating system.
- Multi-tasking operating system.
- Used in servers, cloud computing, cybersecurity, IoT, and embedded systems.

## Important Distributions

- Ubuntu
- Debian
- Fedora
- Red Hat Enterprise Linux (RHEL)
- CentOS Stream
- Arch Linux
- Kali Linux

---

## Essential Commands

```bash
pwd
ls
cd
clear
whoami
hostname
date
cal
history
man
```

---

## Important Shortcuts

```
Ctrl + C
Ctrl + D
Ctrl + L
Ctrl + R
Tab
Arrow Up
Arrow Down
```

---

# Lesson 2 — Linux File System

## Important Directories

| Directory | Purpose |
|-----------|----------|
| / | Root Directory |
| /home | User Home |
| /root | Root User Home |
| /etc | Configuration Files |
| /bin | Essential Commands |
| /usr | User Programs |
| /var | Logs & Variable Data |
| /tmp | Temporary Files |
| /dev | Devices |
| /proc | Process Information |
| /boot | Boot Files |

Remember:

Everything in Linux starts from:

```
/
```

---

# Lesson 3 — Files & Directories

## Commands

```bash
mkdir
rmdir
touch
cp
mv
rm
cat
less
head
tail
find
locate
```

---

# Lesson 4 — Permissions

Permission Structure

```
-rwxr-xr--
```

Meaning

```
File Type

Owner

Group

Others
```

Permission Values

| Symbol | Meaning |
|----------|----------|
| r | Read |
| w | Write |
| x | Execute |

Numeric Permissions

| Number | Permission |
|----------|------------|
| 7 | rwx |
| 6 | rw- |
| 5 | r-x |
| 4 | r-- |

Important Commands

```bash
chmod
chown
chgrp
```

---

# Lesson 5 — Users & Groups

Commands

```bash
whoami
id
groups
sudo
useradd
passwd
usermod
userdel
groupadd
groupdel
```

Important Files

```
/etc/passwd

/etc/shadow

/etc/group
```

Remember

- Root UID = 0
- Every user belongs to one or more groups.

---

# Lesson 6 — Process Management

Important Concepts

Program

↓

Process

↓

PID

↓

PPID

↓

Termination

Commands

```bash
ps
ps -ef
top
htop
kill
kill -9
killall
jobs
fg
bg
```

Important Signals

| Signal | Purpose |
|----------|----------|
| SIGINT | Ctrl+C |
| SIGTERM | Graceful Stop |
| SIGKILL | Force Stop |

Remember

Always try

```
kill
```

before

```
kill -9
```

---

# Lesson 7 — Package Management

APT Workflow

```
Repository

↓

apt update

↓

apt install

↓

apt upgrade

↓

apt remove
```

Commands

```bash
sudo apt update
sudo apt upgrade
sudo apt install
sudo apt remove
sudo apt purge
sudo apt autoremove
apt search
apt show
```

Difference

```
update

↓

Refresh package list

upgrade

↓

Install updates
```

---

# Lesson 8 — Environment Variables

Important Variables

```bash
USER

HOME

PATH

PWD

SHELL

HOSTNAME
```

Commands

```bash
printenv
env
echo $PATH
export
unset
source ~/.bashrc
```

Remember

PATH tells Linux where to search for executable commands.

---

# Lesson 9 — Networking Commands

Commands

```bash
ip addr
ip route
ping
curl
wget
hostname
hostnamectl
ss
nslookup
dig
traceroute
```

Troubleshooting Order

```
Check IP

↓

Ping Gateway

↓

Ping 8.8.8.8

↓

Ping google.com

↓

Check DNS
```

---

# Lesson 10 — Bash Scripting

Structure

```bash
#!/bin/bash
```

Variables

```bash
name="Linux"
```

Input

```bash
read name
```

Arguments

```bash
$0
$1
$2
$@
$#
```

Conditions

```bash
if
elif
else
```

Loops

```bash
for

while
```

Functions

```bash
function_name() {

}
```

Exit Code

```bash
echo $?
```

---

# Most Important Linux Commands

```bash
pwd
ls
cd
mkdir
rm
cp
mv
touch
cat
find
chmod
chown
whoami
id
ps
top
kill
apt
ip
ping
curl
ss
nano
vim
history
man
```

---

# Permission Numbers

```
7 = rwx

6 = rw-

5 = r-x

4 = r--

3 = -wx

2 = -w-

1 = --x

0 = ---
```

---

# Interview Cheat Sheet

Difference Between

Program vs Process

Foreground vs Background

kill vs kill -9

apt update vs apt upgrade

remove vs purge

curl vs wget

PATH vs PWD

Root vs Home Directory

---

# DevOps Connection

Linux Skills Used Daily

✅ SSH

✅ Bash

✅ Cron

✅ Docker

✅ Kubernetes

✅ Git

✅ Systemctl

✅ Logs

✅ Networking

---

# AWS Connection

Common Commands

```bash
ssh

apt

systemctl

ip

curl

journalctl
```

---

# Docker Connection

Important Linux Concepts

- Processes
- Namespaces
- Environment Variables
- Networking
- File System
- Permissions

---

# Kubernetes Connection

Relies On

- Linux Processes
- Networking
- Containers
- Environment Variables
- DNS
- File System

---

# DevSecOps Connection

Linux Knowledge Required

- User Management
- Permissions
- Processes
- Networking
- Bash
- Package Updates
- Environment Variables

---

# Top 50 Commands to Memorize

```bash
pwd
ls
cd
mkdir
rmdir
touch
cp
mv
rm
cat
less
head
tail
find
chmod
chown
chgrp
whoami
id
groups
useradd
passwd
groupadd
ps
top
htop
kill
killall
jobs
fg
bg
apt
dpkg
env
printenv
export
unset
ip
ping
curl
wget
hostname
hostnamectl
ss
nslookup
dig
nano
vim
history
man
echo
```

---

# Final Self-Assessment

Can you confidently answer these questions?

- What is Linux?
- Explain the Linux file system hierarchy.
- What do file permissions represent?
- Difference between `chmod` and `chown`.
- Explain PID and PPID.
- Difference between `kill` and `kill -9`.
- Difference between `apt update` and `apt upgrade`.
- What is the purpose of the `PATH` variable?
- Explain how `ping` differs from `curl`.
- How do you make a Bash script executable?

If you can answer all of these without looking at your notes, you're ready for Intermediate Linux.

---

# What's Next?

Intermediate Linux:

1. Advanced Bash Scripting
2. Cron Jobs
3. systemd & Services
4. Logs (`journalctl`)
5. SSH & Remote Administration
6. Disk & Storage Management
7. Firewall (`ufw`, `iptables`)
8. System Monitoring
9. Performance Tuning
10. Linux Security Hardening
