# Linux Lesson 6: Process Management

## Objective

Understand how Linux manages processes, how to monitor and control them, and why process management is a fundamental skill for Linux administrators, DevOps engineers, and cybersecurity professionals.

---

# Introduction

Everything running on a Linux system is a **process**.

Examples include:

- Web browsers
- Python programs
- Docker containers
- Databases
- Web servers
- System services

Whenever a program starts executing, Linux creates a process and assigns it system resources such as CPU time and memory.

Understanding processes is essential for troubleshooting, performance monitoring, automation, and security.

---

# Program vs Process

A **program** is a file stored on disk that contains executable instructions.

A **process** is a program that is currently running in memory.

Example:

Program:

```
python3
```

Process:

```bash
python3 app.py
```

| Program | Process |
|----------|----------|
| Stored on disk | Running in memory |
| Passive | Active |
| Uses no CPU | Uses CPU and RAM |
| Can exist without execution | Exists only while executing |

---

# Process Lifecycle

A Linux process typically moves through several states during its lifetime.

```
New
 ↓
Ready
 ↓
Running
 ↓
Waiting (optional)
 ↓
Running
 ↓
Terminated
```

### New

The operating system creates the process.

### Ready

The process is waiting for CPU time.

### Running

The CPU is actively executing the process.

### Waiting (Blocked)

The process is waiting for an external event, such as:

- User input
- Disk access
- Network response
- File I/O

### Terminated

The process finishes or is stopped.

---

# Process ID (PID)

Every running process has a unique **Process ID (PID)**.

Linux uses the PID to identify and manage processes.

View running processes:

```bash
ps
```

Example:

```
PID   TTY      TIME     CMD
2481  pts/0    00:00:00 bash
2524  pts/0    00:00:00 python3
```

Here:

- 2481 → Bash process
- 2524 → Python process

---

# Parent Process ID (PPID)

Most processes are created by another process.

The process that creates another process is called the **parent process**.

Example:

```
bash
 └── python3
      └── subprocess
```

View parent processes:

```bash
ps -ef
```

Important columns:

- PID
- PPID
- User
- Command

---

# Foreground Process

A foreground process occupies the terminal until it finishes.

Example:

```bash
python3 app.py
```

You generally cannot use that terminal for another command until the process exits or is suspended.

---

# Background Process

A background process continues running while allowing you to keep using the terminal.

Example:

```bash
python3 app.py &
```

The ampersand (`&`) starts the process in the background.

Use this for long-running tasks that do not require constant interaction.

---

# Viewing Processes

## ps

Displays a snapshot of running processes.

```bash
ps
```

---

## ps -ef

Displays detailed process information.

```bash
ps -ef
```

Shows:

- User
- PID
- PPID
- Start time
- Terminal
- Command

---

## top

Displays real-time information about:

- CPU usage
- Memory usage
- Running processes
- Load average
- System uptime

Command:

```bash
top
```

Press `q` to exit.

---

## htop

An improved interactive process viewer.

Install:

```bash
sudo apt install htop
```

Run:

```bash
htop
```

Advantages:

- User-friendly interface
- Color-coded output
- Interactive navigation
- Easy process management

---

# Process States

A process can exist in different states.

Common states include:

| State | Meaning |
|---------|----------|
| R | Running |
| S | Sleeping |
| D | Waiting for I/O |
| T | Stopped |
| Z | Zombie |

Zombie processes have completed execution but still occupy an entry in the process table until the parent process acknowledges them.

---

# Stopping Processes

## Graceful Termination

```bash
kill PID
```

Example:

```bash
kill 2524
```

This sends the **SIGTERM (15)** signal, allowing the application to clean up before exiting.

---

## Force Termination

```bash
kill -9 PID
```

Example:

```bash
kill -9 2524
```

This sends the **SIGKILL (9)** signal.

The process stops immediately without cleanup.

Use only when a process is unresponsive.

---

## Kill by Name

```bash
killall firefox
```

Terminates all processes with the specified name.

---

# Job Control

Suspend a running process:

```
Ctrl + Z
```

View suspended/background jobs:

```bash
jobs
```

Continue a job in the background:

```bash
bg
```

Bring it back to the foreground:

```bash
fg
```

---

# Linux Signals

Signals allow Linux to communicate with processes.

Common signals:

| Signal | Number | Purpose |
|----------|---------|----------|
| SIGINT | 2 | Interrupt process (Ctrl + C) |
| SIGTERM | 15 | Graceful termination |
| SIGKILL | 9 | Force termination |
| SIGHUP | 1 | Reload configuration or terminate session |

---

# Important Commands

| Command | Purpose |
|----------|----------|
| ps | Display running processes |
| ps -ef | Detailed process information |
| top | Real-time process monitor |
| htop | Interactive process monitor |
| kill | Gracefully terminate a process |
| kill -9 | Forcefully terminate a process |
| killall | Kill processes by name |
| jobs | Display background jobs |
| bg | Resume a suspended job in the background |
| fg | Bring a background job to the foreground |

---

# Why Process Management Matters

## Linux Administration

- Identify high CPU usage
- Monitor memory consumption
- Stop unresponsive applications
- Troubleshoot server performance

---

## DevOps

DevOps engineers monitor application health.

Examples:

- Restart failed services
- Identify memory leaks
- Investigate CPU spikes
- Manage long-running processes

---

## Cybersecurity

Attackers often leave malicious processes running.

Examples:

- Cryptocurrency miners
- Reverse shells
- Malware
- Backdoors

Monitoring running processes helps identify suspicious activity.

---

# Cloud (AWS) Connection

When you launch an EC2 instance:

- Applications
- Databases
- Web servers

all run as Linux processes.

If an application crashes, administrators inspect the running processes to diagnose the problem.

---

# Docker Connection

Docker containers are built around Linux processes.

Example:

```
Docker Container
      │
      └── Main Process (PID 1)
```

If the main process exits, the container stops.

Understanding Linux processes is essential for troubleshooting Docker containers.

---

# Kubernetes Connection

Kubernetes continuously monitors container processes.

If a process crashes:

- Kubernetes detects the failure.
- It restarts the container automatically (depending on the restart policy).

Understanding process behavior helps diagnose container failures.

---

# DevSecOps Connection

Process management plays a role in securing systems.

Examples:

- Detecting unauthorized processes.
- Monitoring resource usage.
- Identifying suspicious background services.
- Investigating malware persistence.
- Responding to security incidents.

Many Endpoint Detection and Response (EDR) tools rely on process monitoring.

---

# Best Practices

- Terminate processes gracefully before using force.
- Monitor system resources regularly.
- Investigate unknown processes.
- Avoid running unnecessary background processes.
- Keep servers free from zombie processes.
- Use `top` or `htop` during troubleshooting.

---

# Common Mistakes

❌ Using `kill -9` immediately.

❌ Ignoring high CPU usage.

❌ Running multiple unnecessary background processes.

❌ Forgetting to monitor long-running services.

❌ Killing the wrong process due to an incorrect PID.

---

# Real-World Scenario

A production web server suddenly becomes slow.

Administrator actions:

1. Run `top`.
2. Identify the process consuming the most CPU.
3. Verify whether the process is expected.
4. Restart or terminate it if necessary.
5. Investigate logs to determine the root cause.

This workflow is common in Linux administration and DevOps.

---

# Interview Questions

1. What is a process?
2. What is the difference between a program and a process?
3. What is a PID?
4. What is a PPID?
5. What is the difference between `ps` and `top`?
6. Explain foreground and background processes.
7. What does `kill -9` do?
8. What is a zombie process?
9. Why should `kill -9` be used cautiously?
10. How does Docker relate to Linux processes?

---

# Hands-on Lab

Run the following commands:

```bash
ps

ps -ef

top

sudo apt install htop

htop

sleep 60 &

jobs

ps -ef | grep sleep

kill <PID>

jobs
```

Replace `<PID>` with the Process ID of the `sleep` command.

Observe:

- Process creation
- Background execution
- Process termination
- Resource monitoring

---

# Mini Challenge

1. Start a process in the background.
2. Find its PID.
3. Bring it back to the foreground.
4. Stop it using `Ctrl + C`.
5. Explain the difference between `kill` and `kill -9`.
6. Why is `top` useful during troubleshooting?

---

# Summary

- A process is a running instance of a program.
- Every process has a unique PID.
- Processes can have parent and child relationships.
- Linux provides tools such as `ps`, `top`, and `htop` for monitoring processes.
- `kill` requests graceful termination, while `kill -9` forces immediate termination.
- Background processes allow continued use of the terminal.
- Process management is essential in Linux administration, DevOps, cloud computing, Docker, Kubernetes, and cybersecurity.
