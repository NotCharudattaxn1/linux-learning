# Chapter 12 — systemd & Linux Services

> **Linux for DevSecOps**
> **Level:** Beginner → Intermediate
> **Prerequisite:** Processes & Process Management

---

## 1. Introduction

Until now, we learned about **processes**.

We learned that when a program is running, Linux treats it as a process:

```text
Program
   ↓
Process
   ↓
PID
```

But there is another important question:

> **Who starts and manages important processes on a Linux system?**

For example:

* Who starts the SSH server?
* Who starts a web server?
* What happens when Linux boots?
* What happens if a service crashes?
* How can we restart a service?
* How can we find out why a service failed?

This is where **systemd** comes in.

---

# 2. What Is a Service?

A **service** is a program that usually runs in the background and provides some functionality.

Examples:

```text
SSH server       → allows remote login
Web server       → serves websites
Database server  → stores data
Docker daemon    → manages containers
Cron service     → handles scheduled jobs
```

A service usually starts without requiring a user to manually run it every time.

### Simple example

Imagine a web server:

```text
Linux starts
    ↓
Web server starts
    ↓
Web server waits for requests
    ↓
User sends HTTP request
    ↓
Web server responds
```

The web server may continue running for hours or days.

---

# 3. What Is a Daemon?

You will frequently see the word **daemon** in Linux.

A daemon is a program that runs in the background and performs a specific task, often without direct user interaction.

Examples:

```text
sshd    → SSH daemon
cron    → scheduling daemon
dockerd → Docker daemon
```

A service and a daemon are related, but they are not exactly the same concept.

A simple way to remember:

```text
Daemon
→ background program

Service
→ functionality managed as a service
```

---

# 4. What Is systemd?

**systemd** is the system and service manager used by many modern Linux distributions.

Its responsibilities include:

* Starting services
* Stopping services
* Restarting services
* Managing dependencies
* Starting services during boot
* Tracking service state
* Collecting logs
* Managing system targets

Think of systemd as a **manager for the Linux system**.

```text
                    systemd
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
      SSH            Web Server     Database
        ↓              ↓              ↓
     process         process        process
```

---

# 5. Why Is systemd Important?

Imagine a Linux server running:

```text
SSH
Nginx
Database
Docker
Monitoring agent
Application
```

Without some system managing these programs, administration would become difficult.

You would need to manually:

* Start them
* Stop them
* Check whether they are running
* Restart them after crashes
* Start them after reboot

systemd provides a consistent way to manage them.

---

# 6. systemd and PID 1

One of the most important facts:

> On a system using systemd, systemd normally runs as **PID 1**.

Check it:

```bash
ps -p 1 -f
```

You may see something similar to:

```text
UID   PID  PPID  CMD
root    1     0  /sbin/init
```

On a system using systemd, `/sbin/init` may point to systemd.

Check:

```bash
readlink -f /sbin/init
```

You may get something similar to:

```text
/usr/lib/systemd/systemd
```

The exact path can vary by distribution.

---

# 7. Why Is PID 1 Special?

PID 1 is special because it is the first userspace process started by the Linux kernel.

A simplified boot process:

```text
Computer starts
      ↓
Firmware
      ↓
Bootloader
      ↓
Linux kernel
      ↓
systemd (PID 1)
      ↓
System services
      ↓
Login / Applications
```

Therefore systemd plays a major role in bringing the Linux system into a usable state.

---

# 8. systemctl

The main command used to interact with systemd is:

```bash
systemctl
```

Think of it as:

> "The control panel for systemd."

Basic syntax:

```bash
systemctl <command> <service>
```

Example:

```bash
systemctl status nginx
```

---

# 9. Checking Service Status

The most important command:

```bash
systemctl status <service>
```

Example:

```bash
systemctl status nginx
```

You may see:

```text
● nginx.service - A high performance web server
     Loaded: loaded
     Active: active (running)
   Main PID: 1234
```

Important parts:

### Loaded

```text
Loaded: loaded
```

systemd knows about the service unit.

### Active

```text
Active: active (running)
```

The service is currently running.

### Main PID

```text
Main PID: 1234
```

This is the main process associated with the service.

---

# 10. Active vs Inactive

A service can be:

```text
active
inactive
failed
```

Check:

```bash
systemctl is-active nginx
```

Possible result:

```text
active
```

---

# 11. Enabled vs Disabled

This is one of the most important systemd concepts.

There are two separate questions:

```text
Is the service running right now?
        ↓
      active

Should it automatically start during boot?
        ↓
      enabled
```

Check runtime state:

```bash
systemctl is-active nginx
```

Check boot configuration:

```bash
systemctl is-enabled nginx
```

---

# 12. Active and Enabled Are Different

A service can have combinations such as:

```text
Active + Enabled
Active + Disabled
Inactive + Enabled
Inactive + Disabled
```

For example:

```text
Active + Enabled
→ Running now
→ Will start automatically during boot

Active + Disabled
→ Running now
→ Not configured for automatic startup
```

Remember:

```text
start   ≠ enable
stop    ≠ disable
```

---

# 13. Starting a Service

To start a service:

```bash
sudo systemctl start <service>
```

Example:

```bash
sudo systemctl start nginx
```

Then:

```bash
systemctl status nginx
```

---

# 14. Stopping a Service

```bash
sudo systemctl stop <service>
```

Example:

```bash
sudo systemctl stop nginx
```

Check:

```bash
systemctl status nginx
```

---

# 15. Restarting a Service

```bash
sudo systemctl restart <service>
```

Example:

```bash
sudo systemctl restart nginx
```

Conceptually:

```text
restart
   ↓
stop
   ↓
start
```

---

# 16. Reloading a Service

Some applications allow their configuration to be re-read without completely stopping the service.

```bash
sudo systemctl reload <service>
```

Example:

```bash
sudo systemctl reload nginx
```

Conceptually:

```text
restart
→ stop application
→ start application again

reload
→ keep application running
→ reload configuration
```

Not every service supports reload.

---

# 17. Enable a Service

To configure automatic startup during boot:

```bash
sudo systemctl enable <service>
```

Example:

```bash
sudo systemctl enable nginx
```

Check:

```bash
systemctl is-enabled nginx
```

---

# 18. Enable and Start Together

A useful command:

```bash
sudo systemctl enable --now nginx
```

This:

```text
enable → start automatically at boot
now    → start immediately
```

---

# 19. Disable a Service

Prevent automatic startup during boot:

```bash
sudo systemctl disable <service>
```

Important:

> Disabling a service does not necessarily stop it if it is already running.

To disable and stop it:

```bash
sudo systemctl disable --now nginx
```

---

# 20. Masking a Service

Masking is stronger than disabling.

```bash
sudo systemctl mask nginx
```

A masked service cannot normally be started until it is unmasked.

Unmask:

```bash
sudo systemctl unmask nginx
```

Think:

```text
disable
→ don't start automatically

mask
→ prevent normal startup
```

Use masking carefully.

---

# 21. Listing Services

List currently loaded service units:

```bash
systemctl list-units --type=service
```

List installed service unit files:

```bash
systemctl list-unit-files --type=service
```

The distinction:

```text
list-units
→ units currently loaded/active in systemd's view

list-unit-files
→ service definitions installed on the system
```

---

# 22. Finding Failed Services

Run:

```bash
systemctl --failed
```

This is useful when troubleshooting a Linux server.

Example:

```text
UNIT          LOAD   ACTIVE   SUB    DESCRIPTION
nginx.service loaded failed   failed A high performance web server
```

---

# 23. What Is a Unit?

A **unit** is a resource managed by systemd.

Common types:

| Unit       | Purpose               |
| ---------- | --------------------- |
| `.service` | Service               |
| `.socket`  | Socket                |
| `.target`  | Group/system state    |
| `.timer`   | Scheduled activation  |
| `.mount`   | Mount point           |
| `.path`    | Path-based activation |

Initially, focus on:

```text
.service
.target
.timer
```

---

# 24. Service Unit Files

A service is usually described using a **unit file**.

Example:

```text
example.service
```

Common locations include:

```text
/etc/systemd/system/
```

and distribution-provided locations such as:

```text
/usr/lib/systemd/system/
```

or:

```text
/lib/systemd/system/
```

The exact location depends on the distribution.

---

# 25. Basic Service Unit Structure

Example:

```ini
[Unit]
Description=My Application
After=network.target

[Service]
ExecStart=/usr/bin/myapp
User=myuser
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

We will break this down.

---

# 26. `[Unit]`

Example:

```ini
[Unit]
Description=My Application
After=network.target
```

### Description

```ini
Description=My Application
```

Human-readable description.

### After

```ini
After=network.target
```

Controls startup ordering.

It tells systemd that this unit should start after the specified target has been reached.

It does **not** by itself mean the target is a mandatory dependency.

---

# 27. `[Service]`

Defines how the service runs.

```ini
[Service]
ExecStart=/usr/bin/myapp
User=myuser
Restart=on-failure
```

---

# 28. ExecStart

Defines the command used to start the service.

Example:

```ini
ExecStart=/usr/bin/python3 /opt/myapp/app.py
```

This means systemd runs:

```bash
/usr/bin/python3 /opt/myapp/app.py
```

---

# 29. User

Example:

```ini
User=myuser
```

The application runs as that user.

This is an important security concept.

If an application does not need root privileges, avoid running it as root.

```text
Application
     ↓
Dedicated user
     ↓
Limited permissions
```

This is the principle of:

> **Least privilege**

---

# 30. Restart

Example:

```ini
Restart=on-failure
```

This tells systemd to restart the service when it fails.

Common values include:

```text
no
on-success
on-failure
always
```

Use restart policies carefully.

A broken service with aggressive restart settings can enter a restart loop.

---

# 31. `[Install]`

Example:

```ini
[Install]
WantedBy=multi-user.target
```

This defines how the service participates in boot-time activation when enabled.

For now, remember:

```text
[Install]
→ controls how enablement connects the service to boot targets
```

---

# 32. daemon-reload

After creating or modifying a unit file:

```bash
sudo systemctl daemon-reload
```

This tells systemd to reread unit definitions.

Important:

> `daemon-reload` does **not** restart the service.

Typical workflow:

```text
Edit unit file
      ↓
daemon-reload
      ↓
restart/reload service
      ↓
check status
```

---

# 33. journalctl

systemd provides a logging system called the **systemd journal**.

The command used to inspect it is:

```bash
journalctl
```

Think:

> `journalctl` = command-line tool for reading the systemd journal.

---

# 34. Viewing Logs

View all available journal entries:

```bash
journalctl
```

Show the latest 50 entries:

```bash
journalctl -n 50
```

Follow logs live:

```bash
journalctl -f
```

Stop with:

```text
Ctrl + C
```

---

# 35. Logs for a Specific Service

```bash
journalctl -u nginx
```

Follow service logs:

```bash
journalctl -u nginx -f
```

Recent logs:

```bash
journalctl -u nginx -n 50
```

Logs from the last 10 minutes:

```bash
journalctl -u nginx --since "10 minutes ago"
```

---

# 36. Boot Logs

Current boot:

```bash
journalctl -b
```

Previous boot:

```bash
journalctl -b -1
```

This is useful when a service works normally but fails after reboot.

---

# 37. Error Logs

Show error-level messages:

```bash
journalctl -p err
```

Current boot:

```bash
journalctl -b -p err
```

---

# 38. Checking Dependencies

View dependencies:

```bash
systemctl list-dependencies <service>
```

Example:

```bash
systemctl list-dependencies nginx
```

Conceptually:

```text
Application
    │
    ├── Network
    ├── Filesystem
    └── Other services
```

---

# 39. Checking a Service's Main PID

Run:

```bash
systemctl show nginx -p MainPID
```

Example:

```text
MainPID=1234
```

Then:

```bash
ps -p 1234 -f
```

This connects systemd to the process-management concepts from the previous chapter.

```text
systemd
   ↓
service
   ↓
Main PID
   ↓
process
```

---

# 40. `systemctl cat`

View the unit definition:

```bash
systemctl cat nginx
```

This is useful when you want to understand how a service has been configured.

---

# 41. `systemctl show`

Display service properties:

```bash
systemctl show nginx
```

Specific property:

```bash
systemctl show nginx -p MainPID
```

Another useful property:

```bash
systemctl show nginx -p ExecStart
```

---

# 42. Service Troubleshooting Workflow

Suppose:

```text
My application is not working.
```

Use a structured process.

### Step 1 — Status

```bash
systemctl status myapp
```

### Step 2 — Logs

```bash
journalctl -u myapp
```

### Step 3 — Recent logs

```bash
journalctl -u myapp -n 100
```

### Step 4 — Unit configuration

```bash
systemctl cat myapp
```

### Step 5 — Main PID

```bash
systemctl show myapp -p MainPID
```

### Step 6 — Inspect process

```bash
ps -p <PID> -f
```

### Step 7 — Fix the cause

Possible causes:

```text
Wrong executable path
Bad configuration
Permission problem
Missing dependency
Port already in use
Missing environment variable
Application crash
```

### Step 8 — If unit file changed

```bash
sudo systemctl daemon-reload
```

### Step 9 — Restart

```bash
sudo systemctl restart myapp
```

### Step 10 — Verify

```bash
systemctl status myapp
```

---

# 43. Security Relevance

systemd can also matter during security investigations.

An attacker who gains access to a Linux machine may attempt to establish persistence through services.

Inspect service definitions:

```bash
systemctl list-unit-files --type=service
```

Inspect a suspicious service:

```bash
systemctl status suspicious.service
```

View its configuration:

```bash
systemctl cat suspicious.service
```

Check the command it runs:

```bash
systemctl show suspicious.service -p ExecStart
```

Check logs:

```bash
journalctl -u suspicious.service
```

During a real incident:

> Do not blindly delete or stop suspicious services.

Instead:

```text
Observe
   ↓
Investigate
   ↓
Collect evidence
   ↓
Contain according to incident-response procedures
```

---

# 44. DevSecOps Relevance

Suppose you deploy a Python API to a Linux server.

Running:

```bash
python3 app.py
```

manually from an SSH terminal is not a robust production setup.

Instead:

```text
Developer
   ↓
Git
   ↓
CI/CD
   ↓
Deployment
   ↓
systemd service
   ↓
Application process
   ↓
Logs
   ↓
Monitoring
```

systemd can help:

* Start the application
* Start it after boot
* Restart it after certain failures
* Run it under a dedicated user
* Provide service status
* Integrate with system logging

---

# 45. Hands-On Lab

> **Important:** The commands in this lab are designed to work across common Linux distributions. Do not assume the SSH service is always named `ssh`.

## Lab 1 — Check Whether systemd Is PID 1

Run:

```bash
ps -p 1 -f
```

Then:

```bash
readlink -f /sbin/init
```

Your goal:

```text
Linux kernel
   ↓
PID 1
   ↓
systemd
```

---

# 46. Lab 2 — Discover the SSH Service Name

Different Linux distributions may use different names for the SSH server.

Common names are:

```text
ssh.service
sshd.service
```

First, look for an installed SSH service:

```bash
systemctl list-unit-files --type=service | grep -E '^(ssh|sshd)\.service'
```

You may get:

```text
ssh.service      enabled
```

or:

```text
sshd.service     enabled
```

### Set the service name

If you found `ssh.service`:

```bash
SSH_SERVICE=ssh.service
```

If you found `sshd.service`:

```bash
SSH_SERVICE=sshd.service
```

Verify:

```bash
echo "$SSH_SERVICE"
```

From this point onward, use:

```bash
$SSH_SERVICE
```

instead of assuming `ssh` or `sshd`.

---

# 47. Lab 3 — Check SSH Status

Run:

```bash
systemctl status "$SSH_SERVICE"
```

Then:

```bash
systemctl is-active "$SSH_SERVICE"
```

And:

```bash
systemctl is-enabled "$SSH_SERVICE"
```

Record:

```text
Service name:
Active state:
Enabled state:
Main PID:
```

To find the main PID:

```bash
systemctl show "$SSH_SERVICE" -p MainPID
```

---

# 48. What If No SSH Service Appears?

If this returns nothing:

```bash
systemctl list-unit-files --type=service | grep -E '^(ssh|sshd)\.service'
```

then the SSH **server** may not be installed.

That is different from having an SSH client.

Check whether the client exists:

```bash
ssh -V
```

You can have:

```text
SSH client
     ✅

SSH server
     ❌
```

The SSH client lets you connect **to another machine**.

The SSH server allows **other machines to connect to yours**.

Do not install or expose an SSH server merely for this lab unless you actually need one.

---

# 49. Lab 4 — Inspect SSH Logs

If the SSH service exists, run:

```bash
journalctl -u "$SSH_SERVICE" -n 30
```

Follow live logs:

```bash
journalctl -u "$SSH_SERVICE" -f
```

Stop with:

```text
Ctrl + C
```

---

# 50. Lab 5 — Connect Service and Process

Run:

```bash
systemctl show "$SSH_SERVICE" -p MainPID
```

Suppose:

```text
MainPID=1234
```

Then:

```bash
ps -p 1234 -f
```

You have now connected the concepts:

```text
systemd
   ↓
SSH service
   ↓
Main PID
   ↓
Linux process
```

---

# 51. Lab 6 — Explore the Unit

Run:

```bash
systemctl cat "$SSH_SERVICE"
```

Look for:

```text
[Unit]
[Service]
[Install]
```

You do not need to understand every line yet.

The goal is to become comfortable reading a systemd unit.

---

# 52. Mini Project — Create a systemd Service

Create:

```text
devsecops_app.py
```

Example:

```python
import time

while True:
    print("DevSecOps application is running")
    time.sleep(30)
```

Place it somewhere such as:

```text
/opt/devsecops/devsecops_app.py
```

---

# 53. Create a Dedicated User

Create a system account:

```bash
sudo useradd --system --no-create-home --shell /usr/sbin/nologin devsecops
```

This helps apply least privilege.

---

# 54. Create the Service File

Create:

```text
/etc/systemd/system/devsecops-app.service
```

Example:

```ini
[Unit]
Description=DevSecOps Demo Application
After=network.target

[Service]
User=devsecops
ExecStart=/usr/bin/python3 /opt/devsecops/devsecops_app.py
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

---

# 55. Load the Service

Run:

```bash
sudo systemctl daemon-reload
```

Start:

```bash
sudo systemctl start devsecops-app
```

Check:

```bash
systemctl status devsecops-app
```

---

# 56. Enable the Service

```bash
sudo systemctl enable devsecops-app
```

Or:

```bash
sudo systemctl enable --now devsecops-app
```

Check:

```bash
systemctl is-enabled devsecops-app
```

---

# 57. View Application Logs

```bash
journalctl -u devsecops-app
```

Follow live:

```bash
journalctl -u devsecops-app -f
```

---

# 58. Practice Troubleshooting

Suppose:

```text
devsecops-app.service
→ failed
```

Start with:

```bash
systemctl status devsecops-app
```

Then:

```bash
journalctl -u devsecops-app -n 50
```

Then:

```bash
systemctl cat devsecops-app
```

Then:

```bash
systemctl show devsecops-app -p MainPID
```

Ask:

```text
Is the executable path correct?
Does the Python file exist?
Does the service user have permission?
Does Python exist at that path?
Does the application itself crash?
```

This is the mindset you need for DevSecOps troubleshooting.

---

# 59. Common Beginner Mistakes

## Mistake 1

Thinking:

```bash
systemctl start nginx
```

means the service will automatically start after reboot.

It does not.

---

## Mistake 2

Thinking:

```bash
systemctl enable nginx
```

means the service starts immediately.

It mainly configures boot-time activation.

---

## Mistake 3

Using:

```bash
kill -9
```

every time a service or process behaves unexpectedly.

Investigate the problem first.

---

## Mistake 4

Forgetting:

```bash
systemctl daemon-reload
```

after modifying a unit file.

---

## Mistake 5

Assuming every distribution names the SSH server:

```text
ssh.service
```

Some use:

```text
sshd.service
```

Always discover the actual service name when writing portable instructions.

---

# 60. Essential Commands

```bash
systemctl status SERVICE
```

Show service status.

```bash
systemctl start SERVICE
```

Start service.

```bash
systemctl stop SERVICE
```

Stop service.

```bash
systemctl restart SERVICE
```

Restart service.

```bash
systemctl reload SERVICE
```

Reload configuration when supported.

```bash
systemctl enable SERVICE
```

Enable at boot.

```bash
systemctl disable SERVICE
```

Disable at boot.

```bash
systemctl enable --now SERVICE
```

Enable and start.

```bash
systemctl disable --now SERVICE
```

Disable and stop.

```bash
systemctl is-active SERVICE
```

Check runtime state.

```bash
systemctl is-enabled SERVICE
```

Check boot configuration.

```bash
systemctl --failed
```

Show failed units.

```bash
systemctl cat SERVICE
```

View unit configuration.

```bash
systemctl show SERVICE
```

Show unit properties.

```bash
systemctl daemon-reload
```

Reload unit definitions.

```bash
journalctl -u SERVICE
```

View service logs.

```bash
journalctl -u SERVICE -f
```

Follow service logs.

```bash
journalctl -b
```

View current boot logs.

```bash
journalctl -p err
```

View error-level messages.

```bash
systemctl list-dependencies SERVICE
```

View dependencies.

---

# 61. Knowledge Checklist

Before moving to the next chapter, you should be able to:

* [ ] Explain what a Linux service is.
* [ ] Explain what a daemon is.
* [ ] Explain what systemd does.
* [ ] Explain why systemd commonly runs as PID 1.
* [ ] Use `systemctl status`.
* [ ] Start and stop services.
* [ ] Restart services.
* [ ] Understand reload vs restart.
* [ ] Understand active vs inactive.
* [ ] Understand active vs enabled.
* [ ] Enable and disable services.
* [ ] Explain masking.
* [ ] Find failed services.
* [ ] Use `journalctl`.
* [ ] View logs for a specific service.
* [ ] Follow logs live.
* [ ] View current boot logs.
* [ ] Understand basic unit files.
* [ ] Understand `[Unit]`.
* [ ] Understand `[Service]`.
* [ ] Understand `[Install]`.
* [ ] Understand `ExecStart`.
* [ ] Understand `User`.
* [ ] Understand `Restart`.
* [ ] Understand `daemon-reload`.
* [ ] View dependencies.
* [ ] Find a service's Main PID.
* [ ] Connect a service to its process.
* [ ] Troubleshoot a failed service.
* [ ] Discover distribution-specific service names.
* [ ] Understand why systemd matters in DevSecOps.
* [ ] Understand why least privilege matters for services.

---

# 62. Key Mental Model

Don't try to memorize every systemd command.

Understand this:

```text
Linux boots
    ↓
systemd (PID 1)
    ↓
systemd manages units
    ↓
.service unit
    ↓
Application process
    ↓
PID
    ↓
Logs
```

And when something fails:

```text
Service problem
      ↓
systemctl status
      ↓
journalctl
      ↓
Inspect configuration
      ↓
Inspect process
      ↓
Fix problem
      ↓
Restart/reload
      ↓
Verify
```

---

# 63. DevSecOps Connection

Your Linux foundation is building toward:

```text
Linux
  ↓
Git
  ↓
Bash + Python
  ↓
Docker
  ↓
CI/CD
  ↓
Cloud
  ↓
Kubernetes
  ↓
Security
  ↓
DevSecOps
```

systemd is an important bridge between basic Linux knowledge and real server administration.

Once you understand systemd, concepts such as:

* application deployment
* service monitoring
* restart policies
* logging
* least privilege
* production troubleshooting

will become much easier to understand.

---

# 64. Next Chapter

```text
Chapter 11
Processes & Process Management
        ↓
Chapter 12
systemd & Linux Services
        ↓
Chapter 13
SSH & Remote Linux Administration
```

**Next: SSH & Remote Linux Administration**
