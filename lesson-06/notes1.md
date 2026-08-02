# Linux Lesson 6: Process Management

> **Module:** Linux Fundamentals
> **Difficulty:** Beginner → Intermediate
> **Prerequisites:** Terminal Basics, Linux File System, File Permissions, Users & Groups
> **Estimated Study Time:** 2–3 Hours

---

# Learning Objectives

After completing this lesson, you should be able to:

- Explain what a Linux process is.
- Differentiate between a program and a process.
- Understand Process IDs (PID) and Parent Process IDs (PPID).
- Monitor running processes.
- Manage foreground and background jobs.
- Safely terminate processes.
- Understand how Linux processes relate to Docker, Kubernetes, and DevSecOps.

---

# Introduction

Every application running on Linux is executed as one or more **processes**.

Examples:

- Google Chrome
- Python
- VS Code
- Docker
- MySQL
- Nginx
- SSH Server

When a program starts, Linux loads it into memory and creates a process.

A Linux system may have hundreds or even thousands of processes running simultaneously.

---

# Program vs Process

A common interview question.

## Program

A **program** is an executable file stored on disk.

Examples:

```
python3
nginx
firefox
```

A program is passive until it is executed.

---

## Process

A **process** is an active instance of a running program.

Example:

```bash
python3 app.py
```

Linux allocates:

- CPU time
- Memory
- File descriptors
- Network resources

to the process.

---

## Comparison

| Program | Process |
|----------|----------|
| Stored on disk | Stored in RAM |
| Passive | Active |
| Doesn't consume CPU | Consumes CPU |
| Doesn't consume memory | Uses memory |
| Can exist without running | Exists only while running |

---

# Process Lifecycle

Every process goes through multiple stages.

```
            +-------+
            |  New  |
            +-------+
                 |
                 ▼
            +-------+
            | Ready |
            +-------+
                 |
                 ▼
            +---------+
            | Running |
            +---------+
             |      |
             |      ▼
             |  +---------+
             |  | Waiting |
             |  +---------+
             |      |
             +------+
                 |
                 ▼
          +-------------+
          | Terminated  |
          +-------------+
```

### New

The operating system creates the process.

---

### Ready

The process is waiting for CPU scheduling.

---

### Running

The CPU executes the instructions.

---

### Waiting (Blocked)

The process is waiting for:

- User input
- File access
- Database response
- Network packet
- Disk operation

---

### Terminated

The process exits normally or is killed.

---

# Process ID (PID)

Every running process receives a unique **Process ID (PID)**.

Example:

```bash
ps
```

Output:

```
PID TTY          TIME CMD
2481 pts/0   00:00:00 bash
2524 pts/0   00:00:00 python3
```

Here:

```
2481
```

is Bash's PID.

```
2524
```

is Python's PID.

Linux uses PIDs to identify and manage processes.

---

# Parent Process (PPID)

Most processes are created by another process.

Example:

```
systemd
   │
   ├── sshd
   │      │
   │      └── bash
   │             │
   │             └── python3
   │
   └── nginx
```

Every process has:

- PID
- PPID

View them:

```bash
ps -ef
```

---

# Foreground Process

Runs directly in the terminal.

Example:

```bash
python3 app.py
```

The terminal waits until the process finishes.

---

# Background Process

Runs independently while the terminal remains available.

Example:

```bash
python3 app.py &
```

Linux immediately returns the terminal prompt.

Useful for:

- Servers
- Long-running scripts
- Downloads
- Automation

---

# Process Monitoring Commands

---

## ps

Displays currently running processes.

```bash
ps
```

---

## ps -ef

Shows:

- User
- PID
- PPID
- CPU Time
- Start Time
- Terminal
- Command

```bash
ps -ef
```

---

## top

Displays:

- CPU usage
- Memory usage
- Running processes
- Load Average
- Uptime

```bash
top
```

Press:

```
q
```

to exit.

---

## htop

Modern interactive process viewer.

Install:

```bash
sudo apt install htop
```

Run:

```bash
htop
```

Advantages:

- Search processes
- Kill processes
- Sort by CPU
- Sort by Memory
- Mouse support
- Better UI

---

# Process States

| Code | Meaning |
|------|----------|
| R | Running |
| S | Sleeping |
| D | Waiting for Disk I/O |
| T | Stopped |
| Z | Zombie |

---

## Zombie Process

A zombie process has:

- Finished execution.
- Released most resources.
- Not yet been acknowledged by its parent.

Zombie processes consume very little memory but indicate that the parent process has not cleaned them up properly.

---

# Signals

Linux communicates with processes using **signals**.

Common signals:

| Signal | Number | Description |
|---------|---------|-------------|
| SIGINT | 2 | Interrupt (Ctrl + C) |
| SIGTERM | 15 | Graceful termination |
| SIGKILL | 9 | Immediate termination |
| SIGHUP | 1 | Reload configuration or terminate session |

---

# Stopping Processes

## Graceful

```bash
kill PID
```

Example:

```bash
kill 1234
```

Linux sends:

```
SIGTERM
```

The application can:

- Save data
- Close files
- Release memory
- Exit safely

---

## Force Kill

```bash
kill -9 PID
```

Linux sends:

```
SIGKILL
```

The process is terminated immediately.

No cleanup occurs.

Use only when necessary.

---

## Kill by Name

```bash
killall firefox
```

Kills every Firefox process.

---

# Job Control

Suspend a running process:

```
Ctrl + Z
```

View jobs:

```bash
jobs
```

Resume in background:

```bash
bg
```

Bring back:

```bash
fg
```

---

# Why Process Management Matters

## Linux Administration

Administrators use process management to:

- Find memory leaks
- Investigate high CPU usage
- Restart failed applications
- Monitor servers

---

## DevOps

Engineers monitor:

- Containers
- Build pipelines
- CI/CD agents
- Web servers
- Databases

A large part of troubleshooting starts with checking running processes.

---

## Cybersecurity

Attackers often:

- Launch malware
- Start reverse shells
- Run cryptocurrency miners
- Create persistence

Security analysts inspect running processes to detect malicious activity.

---

# AWS Connection

An EC2 instance runs Linux processes.

Examples:

```
Nginx
Python
Apache
Node.js
MySQL
```

If an application crashes, administrators inspect its process to diagnose the issue.

---

# Docker Connection

Every Docker container revolves around a main Linux process.

```
Container

      │

      ▼

 Main Process (PID 1)
```

If the main process exits, the container stops.

This is why understanding Linux processes is essential before learning Docker.

---

# Kubernetes Connection

Kubernetes monitors container processes.

If the main process crashes:

- Kubernetes detects the failure.
- It restarts the container (depending on the restart policy).

---

# DevSecOps Connection

Process monitoring is important for:

- Endpoint Detection and Response (EDR)
- Runtime Security
- Threat Detection
- Incident Response
- Performance Monitoring

Examples of tools:

- Falco
- CrowdStrike
- Microsoft Defender for Endpoint
- Wazuh

These tools observe process activity to detect suspicious behavior.

---

# Troubleshooting Example

Problem:

Users report that a website is responding slowly.

Typical investigation:

```
top
        ↓
High CPU Usage?
        ↓
Find PID
        ↓
ps -ef
        ↓
Identify Application
        ↓
Restart Service
        ↓
Check Logs
```

This is a common troubleshooting workflow in Linux administration.

---

# Command Cheat Sheet

| Command | Purpose |
|----------|----------|
| ps | Show running processes |
| ps -ef | Detailed process list |
| top | Live monitoring |
| htop | Interactive monitoring |
| jobs | Show background jobs |
| bg | Resume in background |
| fg | Bring to foreground |
| kill PID | Graceful termination |
| kill -9 PID | Force termination |
| killall process | Kill by process name |

---

# Best Practices

✅ Prefer `kill` before `kill -9`.

✅ Monitor CPU and RAM regularly.

✅ Investigate unknown processes.

✅ Remove zombie processes when necessary.

✅ Keep unnecessary background jobs to a minimum.

---

# Common Mistakes

❌ Killing the wrong PID.

❌ Using `kill -9` without trying `kill`.

❌ Ignoring processes consuming excessive CPU.

❌ Leaving long-running jobs unmanaged.

❌ Confusing a program with a process.

---

# Interview Questions

### Beginner

1. What is a process?
2. What is the difference between a process and a program?
3. What is a PID?
4. What is a PPID?
5. What does `ps` display?

---

### Intermediate

6. Explain foreground and background processes.
7. Difference between `top` and `htop`.
8. Difference between `kill` and `kill -9`.
9. What is a zombie process?
10. Why should `SIGKILL` be used carefully?

---

### Advanced

11. Why is PID 1 important in Docker containers?
12. How does Kubernetes detect a crashed application?
13. Why do security tools monitor Linux processes?
14. How can high CPU usage affect a production server?

---

# Hands-on Lab

```bash
# View running processes
ps

# Detailed process information
ps -ef

# Live monitoring
top

# Install htop
sudo apt install htop

# Start htop
htop

# Start a background process
sleep 120 &

# View jobs
jobs

# Find the PID
ps -ef | grep sleep

# Gracefully terminate
kill <PID>

# Verify
jobs
```

---

# Mini Project

Create a **Process Monitoring Guide**.

Tasks:

1. Start multiple background processes.
2. Monitor them using `top`.
3. Find their PIDs.
4. Stop one gracefully.
5. Stop another forcefully.
6. Record your observations.

---

# Quick Revision

✔ Process = Running program

✔ Program = Executable file

✔ PID = Process Identifier

✔ PPID = Parent Process Identifier

✔ `ps` = Snapshot

✔ `top` = Live Monitoring

✔ `kill` = Graceful Termination

✔ `kill -9` = Force Termination

✔ Docker containers depend on Linux processes.

✔ Process management is fundamental in Linux, Cloud, Docker, Kubernetes, and DevSecOps.

---

# Key Takeaways

- Every application running on Linux is a process.
- Linux assigns a unique PID to every process.
- Parent-child relationships help organize process creation.
- Tools like `ps`, `top`, and `htop` are essential for monitoring.
- Graceful termination should always be preferred over forceful termination.
- Understanding Linux processes is foundational for Docker, Kubernetes, cloud administration, and DevSecOps.
