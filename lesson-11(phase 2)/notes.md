# Linux Processes & Process Management

> **Track:** Linux for DevSecOps
> **Level:** Intermediate
> **Purpose:** Understand, inspect, control, and troubleshoot Linux processes in servers, CI/CD environments, and containers.

---

## 1. What Is a Process?

A **process** is a running instance of a program.

A program is a file containing instructions. When the operating system loads that program into memory and starts executing it, it becomes a process.

```text
Program
   │
   │ executed by OS
   ▼
Process
   ├── PID
   ├── Memory
   ├── CPU state
   ├── Open files
   ├── Environment variables
   └── Security credentials
```

### Program vs Process

| Program                     | Process                           |
| --------------------------- | --------------------------------- |
| Passive set of instructions | Running instance                  |
| Exists on disk              | Exists in memory while running    |
| Does not have a PID         | Has a PID                         |
| Example: `/usr/bin/python3` | Example: running `python3 app.py` |

---

# 2. Process Identification

Every Linux process has a **Process ID (PID)**.

## PID

A PID identifies a running process.

```bash
ps
```

Example:

```text
    PID TTY          TIME CMD
   2145 pts/0    00:00:00 bash
   3921 pts/0    00:00:00 sleep
```

Here:

```text
bash  → PID 2145
sleep → PID 3921
```

---

## PPID — Parent Process ID

Processes can create other processes.

The process that creates another process is its **parent**.

The child stores the parent's PID as its **PPID**.

```text
Parent Process
      │
      ├── Child Process
      │
      └── Child Process
```

View PID and PPID:

```bash
ps -o pid,ppid,cmd
```

Example:

```text
PID   PPID CMD
2145  2100 bash
3921  2145 sleep 100
```

Therefore:

```text
sleep → PID 3921
parent → PPID 2145
```

---

# 3. Process Hierarchy

Linux processes form a hierarchy.

A simplified example:

```text
systemd (PID 1)
    │
    ├── sshd
    │    └── bash
    │         └── command
    │
    ├── cron
    │
    └── application
```

On most modern Linux distributions, **systemd** is PID 1.

View the hierarchy:

```bash
pstree
```

With PIDs:

```bash
pstree -p
```

---

# 4. Creating a Process

Run:

```bash
sleep 100
```

The shell waits until the command finishes.

For a background process:

```bash
sleep 300 &
```

Example:

```text
[1] 4321
```

Here:

```text
Job number → 1
PID        → 4321
```

Check it:

```bash
ps -p 4321
```

---

# 5. Foreground vs Background Processes

## Foreground

```bash
sleep 100
```

The shell waits for the command to finish.

## Background

```bash
sleep 100 &
```

The shell remains available.

This is especially useful when working with remote Linux servers over SSH.

```text
Foreground:

Shell ──────> Process
              │
              └── Shell waits


Background:

Shell ──────> Process
   │
   └── Shell remains available
```

---

# 6. Shell Jobs

A **job** is a process managed by the current shell.

List jobs:

```bash
jobs
```

Example:

```text
[1]+ Running    sleep 300 &
```

---

## Suspend a Process

Start:

```bash
sleep 300
```

Press:

```text
Ctrl + Z
```

The process becomes stopped.

Check:

```bash
jobs
```

Example:

```text
[1]+ Stopped    sleep 300
```

---

## Resume in Background

```bash
bg %1
```

---

## Bring to Foreground

```bash
fg %1
```

---

# 7. Process States

Processes can exist in different states.

| State | Meaning               |
| ----- | --------------------- |
| `R`   | Running or runnable   |
| `S`   | Interruptible sleep   |
| `D`   | Uninterruptible sleep |
| `T`   | Stopped               |
| `Z`   | Zombie                |
| `I`   | Idle kernel thread    |

View states:

```bash
ps -eo pid,ppid,stat,cmd
```

Example:

```text
PID   PPID STAT CMD
2145  2100 S    bash
3921  2145 S    sleep 100
```

The `STAT` column contains process state information.

---

# 8. Viewing Processes with `ps`

`ps` provides a snapshot of running processes.

Basic:

```bash
ps
```

Detailed:

```bash
ps -f
```

Common Linux usage:

```bash
ps aux
```

Useful columns include:

```text
USER
PID
%CPU
%MEM
VSZ
RSS
TTY
STAT
START
TIME
COMMAND
```

Another common format:

```bash
ps -ef
```

---

## Sort by CPU

```bash
ps aux --sort=-%cpu
```

Show the top CPU consumers:

```bash
ps aux --sort=-%cpu | head
```

---

## Sort by Memory

```bash
ps aux --sort=-%mem
```

---

# 9. `top`

`top` provides real-time process monitoring.

```bash
top
```

It displays:

* CPU usage
* Memory usage
* Process count
* Load average
* Process states
* Individual process resource usage

Useful keys:

```text
q → quit
P → sort by CPU
M → sort by memory
k → send a signal
```

---

# 10. `htop`

`htop` is an interactive alternative to `top`.

```bash
htop
```

It is generally easier to navigate and read.

Install on Debian/Ubuntu:

```bash
sudo apt install htop
```

Install on Fedora/RHEL-based systems:

```bash
sudo dnf install htop
```

---

# 11. Finding Processes

## `pgrep`

Find a process by name:

```bash
pgrep nginx
```

Display PID and command:

```bash
pgrep -a nginx
```

Find processes belonging to a user:

```bash
pgrep -u username
```

---

## `ps` + `grep`

Another common approach:

```bash
ps aux | grep nginx
```

However, `grep` itself can appear in the results.

For simple process identification, `pgrep` is usually cleaner.

---

# 12. Signals

Linux uses **signals** to communicate with processes.

The `kill` command sends a signal.

```bash
kill <PID>
```

By default:

```text
kill <PID>
      │
      ▼
   SIGTERM
```

---

# 13. SIGTERM vs SIGKILL

This distinction is extremely important for DevSecOps.

## SIGTERM — 15

```bash
kill -15 <PID>
```

or simply:

```bash
kill <PID>
```

It requests graceful termination.

The application can:

* Close files
* Clean up resources
* Save state
* Finish important operations
* Exit normally

**Prefer SIGTERM first.**

---

## SIGKILL — 9

```bash
kill -9 <PID>
```

The kernel immediately terminates the process.

The process cannot catch or ignore `SIGKILL`.

Use it only when necessary.

### Recommended approach

```text
SIGTERM
   │
   ├── Process exits
   │      ↓
   │     Done
   │
   └── Process does not exit
             ↓
        Investigate
             ↓
        SIGKILL if necessary
```

Avoid blindly using:

```bash
kill -9
```

---

# 14. Important Signals

| Signal    | Number | Purpose                                    |
| --------- | -----: | ------------------------------------------ |
| `SIGHUP`  |      1 | Hangup / commonly used for reload behavior |
| `SIGINT`  |      2 | Interrupt                                  |
| `SIGKILL` |      9 | Force termination                          |
| `SIGTERM` |     15 | Graceful termination                       |
| `SIGSTOP` |     19 | Stop process                               |
| `SIGCONT` |     18 | Continue stopped process                   |

List signals:

```bash
kill -l
```

---

# 15. `pkill` and `killall`

## pkill

Send a signal to processes matching a name or criterion:

```bash
pkill process_name
```

Example:

```bash
pkill firefox
```

Be careful because multiple processes may match.

---

## killall

```bash
killall process_name
```

Example:

```bash
killall firefox
```

On production systems, always verify what you're targeting before using broad process-matching commands.

---

# 16. Process Priority

Linux scheduling can be influenced using the **nice value**.

Typical range:

```text
-20 → highest priority
  0 → default
+19 → lowest priority
```

Check:

```bash
ps -eo pid,ni,cmd
```

---

## Start with a Nice Value

```bash
nice -n 10 python3 script.py
```

A higher nice value generally makes the process less favored for CPU scheduling.

---

## Change Priority

```bash
renice 10 -p <PID>
```

Changing to negative nice values normally requires elevated privileges.

---

# 17. Zombie Processes

A **zombie** is a process that has finished execution but still has an entry in the process table because its parent has not collected its exit status.

```text
Parent
   │
   └── Child
         │
         └── exits
              │
              ▼
           Zombie
```

Find zombies:

```bash
ps -eo pid,ppid,stat,cmd | grep Z
```

Important:

> A zombie has already terminated, so sending `SIGKILL` to the zombie itself does not solve the underlying problem.

The parent process needs to reap the child.

---

# 18. Orphan Processes

An **orphan process** is a process whose original parent has terminated while the child continues running.

Linux re-parents the process to another process, commonly PID 1.

```text
Parent
   │
   └── Child
        │
        ├── Parent exits
        │
        ▼
      Orphan
        │
        ▼
   Re-parented
```

### Zombie vs Orphan

```text
Zombie
→ Process has finished
→ Parent has not collected its exit status

Orphan
→ Process is still running
→ Original parent has terminated
```

---

# 19. `/proc` and Process Information

Linux exposes process information through the virtual `/proc` filesystem.

For a process:

```text
/proc/<PID>/
```

Example:

```bash
cat /proc/4321/status
```

Useful files/directories:

```text
/proc/<PID>/status
/proc/<PID>/cmdline
/proc/<PID>/fd/
/proc/<PID>/exe
```

Check the executable:

```bash
readlink /proc/<PID>/exe
```

Check the command line:

```bash
tr '\0' ' ' < /proc/<PID>/cmdline
```

`/proc` is especially useful during security investigations.

---

# 20. Process Resource Usage

Processes consume resources such as:

* CPU
* RAM
* Disk I/O
* Network resources
* File descriptors

For a specific process:

```bash
ps -p <PID> -o pid,ppid,%cpu,%mem,stat,cmd
```

Example:

```bash
ps -p 4321 -o pid,ppid,%cpu,%mem,stat,cmd
```

---

# 21. Load Average

You may see:

```text
load average: 0.50, 0.75, 1.20
```

These represent approximately:

```text
1 minute
5 minutes
15 minutes
```

Load average is **not the same thing as CPU utilization**.

It represents the amount of work involving runnable tasks and certain tasks waiting for resources.

View it with:

```bash
uptime
```

or:

```bash
top
```

---

# 22. DevSecOps Application: Security Investigation

Suppose a Linux server suddenly has unusually high CPU usage.

Start with:

```bash
top
```

Then:

```bash
ps aux --sort=-%cpu | head
```

Identify the suspicious PID.

Inspect it:

```bash
ps -p <PID> -f
```

Check its executable:

```bash
readlink /proc/<PID>/exe
```

Inspect open file descriptors:

```bash
ls -l /proc/<PID>/fd
```

Check network connections:

```bash
ss -tunap
```

This can help determine whether a process is legitimate or requires further investigation.

---

# 23. DevSecOps Application: CI/CD

Consider a CI/CD server:

```text
CI/CD Server
     │
     ├── Build
     ├── Tests
     ├── Docker
     └── Security Scanner
```

If a build hangs or consumes excessive CPU:

```bash
ps aux
```

or:

```bash
top
```

can help identify the responsible process.

Then inspect:

```bash
ps -p <PID> -f
```

If appropriate:

```bash
kill <PID>
```

Understanding process management makes troubleshooting CI/CD infrastructure much easier.

---

# 24. DevSecOps Application: Containers

Containers depend heavily on Linux process concepts.

A simplified view:

```text
Linux Host
    │
    ├── Container A
    │      └── Application Process
    │
    └── Container B
           └── Application Process
```

Understanding:

* PIDs
* Signals
* Process states
* Resource usage
* Parent/child relationships

will make Docker and Kubernetes much easier to understand later.

---

# 25. Essential Command Cheat Sheet

| Command   | Purpose                         |
| --------- | ------------------------------- |
| `ps`      | Process snapshot                |
| `ps aux`  | Detailed process listing        |
| `ps -ef`  | Full-format process listing     |
| `top`     | Real-time monitoring            |
| `htop`    | Interactive monitoring          |
| `pstree`  | Process hierarchy               |
| `pgrep`   | Find processes                  |
| `pkill`   | Signal matching processes       |
| `kill`    | Send a signal to a PID          |
| `killall` | Signal processes by name        |
| `jobs`    | Show shell jobs                 |
| `fg`      | Bring job to foreground         |
| `bg`      | Continue job in background      |
| `nice`    | Start process with a nice value |
| `renice`  | Change nice value               |
| `uptime`  | Show uptime/load average        |
| `kill -l` | List signals                    |

---

# 26. Hands-On Lab

## Lab 1 — Create a Process

Run:

```bash
sleep 300
```

From another terminal:

```bash
ps aux | grep sleep
```

Record the PID.

---

## Lab 2 — Background Process

Run:

```bash
sleep 300 &
```

Then:

```bash
jobs
```

Find its PID:

```bash
pgrep -a sleep
```

---

## Lab 3 — Stop and Resume

Run:

```bash
sleep 300
```

Press:

```text
Ctrl + Z
```

Check:

```bash
jobs
```

Resume:

```bash
bg %1
```

Bring it back:

```bash
fg %1
```

---

## Lab 4 — Signals

Start:

```bash
sleep 300 &
```

Find its PID:

```bash
pgrep -a sleep
```

Send SIGTERM:

```bash
kill <PID>
```

Verify:

```bash
pgrep -a sleep
```

---

## Lab 5 — Inspect a Process

Start:

```bash
sleep 300 &
```

Find the PID:

```bash
pgrep -a sleep
```

Inspect:

```bash
ps -p <PID> -o pid,ppid,stat,%cpu,%mem,cmd
```

Then:

```bash
ls -l /proc/<PID>
```

Finally:

```bash
readlink /proc/<PID>/exe
```

---

# 27. Mini Project — Process Monitor

Create:

```text
process_monitor.sh
```

The script should:

1. Accept a process name as an argument.
2. Determine whether the process is running.
3. Display its PID.
4. Display CPU usage.
5. Display memory usage.
6. Display process state.
7. Display its parent PID.
8. Print a warning if the process is not running.

Example:

```bash
./process_monitor.sh nginx
```

Expected output:

```text
================================
 Process Monitor
================================

Process : nginx
PID     : 1245
PPID    : 1
State   : S
CPU     : 0.2%
Memory  : 1.4%

Status  : RUNNING
```

This is your first Linux automation mini-project for the DevSecOps track.

---

# 28. Knowledge Checklist

Before moving forward, you should be able to:

* [ ] Explain what a process is.
* [ ] Explain program vs process.
* [ ] Understand PID and PPID.
* [ ] Explain parent/child relationships.
* [ ] Understand foreground/background processes.
* [ ] Use `jobs`, `fg`, and `bg`.
* [ ] Use `ps`.
* [ ] Use `top`/`htop`.
* [ ] Find processes using `pgrep`.
* [ ] Understand common signals.
* [ ] Explain SIGTERM vs SIGKILL.
* [ ] Use `kill` safely.
* [ ] Understand process states.
* [ ] Explain zombie vs orphan processes.
* [ ] Understand nice values.
* [ ] Inspect `/proc/<PID>`.
* [ ] Investigate high CPU/memory usage.
* [ ] Understand the importance of processes in containers.
* [ ] Understand why process management matters in CI/CD.

---

# 29. Key Takeaways

```text
Program
   ↓
Process
   ↓
PID
   ↓
Parent / Child relationship
   ↓
Process states
   ↓
Monitoring
   ↓
Signals
   ↓
Process control
```

Remember:

```text
Program ≠ Process

PID  = Process ID
PPID = Parent Process ID

SIGTERM = graceful termination
SIGKILL = forced termination

Zombie = terminated but not reaped
Orphan = running without original parent

Foreground = shell waits
Background = shell remains available
```

---

# 30. What's Next?

After completing this chapter:

```text
Processes & Process Management
            ↓
     systemd & Services
            ↓
    Logs & journalctl
            ↓
           SSH
            ↓
   Linux Networking
            ↓
 Storage & Filesystems
            ↓
        Firewalls
            ↓
   Bash Automation
            ↓
 Linux Security & Hardening
            ↓
     Docker / Containers
            ↓
         DevSecOps
```

**Next topic: ****`systemd`**** and Linux Services**
