# Chapter 12 — systemd & Linux Services

> **Track:** Linux for DevSecOps  
> **Level:** Beginner → Intermediate  
> **Prerequisite:** Linux Processes & Process Management  
> **Goal:** Understand what Linux services are, how `systemd` manages them, how to inspect and troubleshoot them, and why this matters in DevSecOps.

---

## 1. Why Do We Need Services?

Imagine you start your computer. You expect several things to work automatically:

- You can connect through SSH.
- Your web server is already running.
- Your database is available.
- Scheduled tasks can run.
- Networking is initialized.

You do **not** want to manually run every program after every reboot.

Linux therefore uses **services** to run important background programs automatically.

A service usually performs a task continuously or waits for requests in the background.

Examples:

```text
SSH server       → allows remote login
Web server       → serves websites/APIs
Database server  → provides database access
Docker daemon    → manages containers
Cron service     → handles scheduled jobs
```

---

# 2. What Is a Daemon?

A **daemon** is a background process that performs a service for the system or other programs.

You will often see names ending in `d`:

```text
sshd
systemd
cron
```

The `d` commonly stands for **daemon**.

Do not memorize the naming convention as a rule—focus on the concept:

> A daemon is simply a background process designed to provide a service.

For example:

```text
SSH client
    │
    │ connection request
    ▼
 sshd daemon
    │
    ▼
 remote shell/session
```

---

# 3. What Is systemd?

Modern Linux distributions commonly use **systemd** to manage the system after the kernel has started.

It is responsible for things such as:

- Starting services
- Stopping services
- Restarting services
- Starting services during boot
- Managing service dependencies
- Tracking service status
- Organizing system targets
- Working with the system journal

The most important idea for now is:

> **systemd is the manager. Services are the things it manages.**

A simplified picture:

```text
Linux boot
   ↓
Kernel
   ↓
systemd (PID 1)
   ↓
Services
   ├── SSH
   ├── Web server
   ├── Database
   └── Other background services
```

---

# 4. Why Is systemd PID 1?

In Linux, the first userspace process is traditionally given **PID 1**.

On a system using systemd:

```text
PID 1 → systemd
```

Check it:

```bash
ps -p 1 -f
```

You may see something similar to:

```text
UID   PID  PPID  CMD
root    1     0  /sbin/init
```

On many systems, `/sbin/init` is a symbolic link to systemd.

You can verify with:

```bash
readlink -f /sbin/init
```

Typical result:

```text
/usr/lib/systemd/systemd
```

The exact path varies by distribution.

---

# 5. Connecting This to Processes

You just learned about processes in the previous chapter.

Now connect the concepts:

```text
systemd
   ↓
service management
   ↓
application/service
   ↓
process
   ↓
PID
```

For example:

```text
systemd
   ↓
nginx.service
   ↓
nginx process
   ↓
PID 2451
```

This is why process management knowledge is important before learning systemd.

---

# 6. What Is `systemctl`?

`systemctl` is the main command-line tool used to communicate with systemd.

Think of it as:

```text
You
 ↓
systemctl
 ↓
systemd
 ↓
service/process
```

General syntax:

```bash
systemctl <command> <unit>
```

Example:

```bash
systemctl status ssh
```

The exact service name may be `ssh` or `sshd`, depending on the distribution.

---

# 7. What Is a Unit?

systemd manages objects called **units**.

You do not need to learn every unit type yet.

The most important one for this chapter is:

```text
.service → describes a service
```

Other unit types you will encounter later include:

```text
.socket
.target
.timer
.mount
```

So when you see:

```text
nginx.service
```

you can think:

> "This is the systemd unit representing the nginx service."

---

# 8. Checking a Service's Status

Use:

```bash
systemctl status <service>
```

Example:

```bash
systemctl status ssh
```

A status output may look like:

```text
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (...)
     Active: active (running)
   Main PID: 1234 (sshd)
```

The exact output differs between distributions.

Important fields:

| Field | Meaning |
|---|---|
| `Loaded` | Whether systemd found and loaded the unit |
| `Active` | Current runtime state |
| `Main PID` | Main process associated with the service |
| `Loaded` + path | Helps identify where the unit definition came from |

---

# 9. Understanding `active (running)`

When you see:

```text
Active: active (running)
```

it means the service is currently running.

Common states include:

```text
active
inactive
failed
activating
deactivating
```

For now, focus on these three:

### Active

The service is running.

### Inactive

The service is not running.

### Failed

The service attempted to run but encountered a problem.

---

# 10. Active vs Enabled

This is one of the most important concepts in this chapter.

### Active

Means:

> Is the service running **right now**?

Check it:

```bash
systemctl is-active ssh
```

### Enabled

Means:

> Is the service configured to start automatically during boot?

Check it:

```bash
systemctl is-enabled ssh
```

These are **not the same thing**.

Think of it this way:

```text
ACTIVE
→ What is happening now?

ENABLED
→ What should happen during future boots?
```

A service can be:

```text
Active + Enabled
Active + Disabled
Inactive + Enabled
Inactive + Disabled
```

### Example

Suppose:

```bash
systemctl stop nginx
```

Now nginx is inactive.

But it could still be enabled:

```text
Inactive + Enabled
```

That means it is stopped **now**, but systemd is configured to start it at boot.

---

# 11. Starting a Service

To start a service immediately:

```bash
sudo systemctl start <service>
```

Example:

```bash
sudo systemctl start nginx
```

Then check:

```bash
systemctl status nginx
```

Important:

```text
start
→ affects the current running system
```

It does not automatically mean the service will start after the next reboot.

---

# 12. Stopping a Service

Stop a running service:

```bash
sudo systemctl stop <service>
```

Example:

```bash
sudo systemctl stop nginx
```

Check:

```bash
systemctl is-active nginx
```

Expected result when stopped:

```text
inactive
```

---

# 13. Restarting a Service

Restart:

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

Use restart when the application needs to be fully restarted.

On production systems, remember that a restart can cause downtime.

---

# 14. Reloading a Service

Some applications can reread configuration without being fully stopped.

Use:

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
→ service is stopped and started again

reload
→ service stays running
→ configuration is reread
```

Not every service supports reload.

If a service does not support it, `reload` may fail.

---

# 15. Enabling a Service at Boot

Suppose you want nginx to start automatically every time the machine boots.

Use:

```bash
sudo systemctl enable nginx
```

Check:

```bash
systemctl is-enabled nginx
```

Expected result:

```text
enabled
```

Important:

> `enable` usually changes what happens during future boots. It does not necessarily start the service immediately.

---

# 16. Enable and Start Together

You will often see:

```bash
sudo systemctl enable --now nginx
```

This does both:

```text
enable → configure automatic startup
now    → start immediately
```

Equivalent to:

```bash
sudo systemctl enable nginx
sudo systemctl start nginx
```

---

# 17. Disabling a Service

To prevent a service from being started automatically during boot:

```bash
sudo systemctl disable nginx
```

Check:

```bash
systemctl is-enabled nginx
```

Important:

> `disable` does not necessarily stop a service that is already running.

To disable **and** stop it:

```bash
sudo systemctl disable --now nginx
```

---

# 18. Masking a Service

Masking is stronger than disabling.

```bash
sudo systemctl mask <service>
```

A masked service cannot normally be started until it is unmasked.

Unmask it:

```bash
sudo systemctl unmask <service>
```

Think of the difference as:

```text
Disable
→ Do not start automatically at boot.

Mask
→ Do not allow normal service startup.
```

Use masking carefully, especially on production systems.

---

# 19. Listing Services

See loaded service units:

```bash
systemctl list-units --type=service
```

See installed service unit files:

```bash
systemctl list-unit-files --type=service
```

These commands answer different questions.

### `list-units`

What units are currently loaded/known in the running system?

### `list-unit-files`

What unit files are installed on the system?

---

# 20. Finding Failed Services

One very useful command is:

```bash
systemctl --failed
```

It lists units that systemd considers failed.

This is a good first command when troubleshooting a Linux machine.

Example workflow:

```text
Something is wrong
      ↓
Check failed units
      ↓
systemctl --failed
```

---

# 21. What Is `journalctl`?

systemd can collect logs in a system journal.

The command used to read those logs is:

```bash
journalctl
```

Think of it as:

```text
Applications / services
        ↓
      logs
        ↓
 systemd journal
        ↓
   journalctl
        ↓
      you
```

This becomes extremely useful when a service fails.

---

# 22. Viewing Logs for a Service

Use:

```bash
journalctl -u <service>
```

Example:

```bash
journalctl -u nginx
```

The `-u` means **unit**.

It tells `journalctl`:

> Show log messages associated with this unit.

---

# 23. Follow Logs Live

To watch new log entries appear:

```bash
journalctl -u nginx -f
```

The `-f` means follow.

This is similar to:

```bash
tail -f logfile
```

Stop following with:

```text
Ctrl + C
```

Live logs are particularly useful while testing deployments.

---

# 24. Show the Last N Log Entries

For example:

```bash
journalctl -u nginx -n 50
```

This shows the most recent 50 entries for the nginx service.

This is often more practical than reading a huge amount of historical output.

---

# 25. Filter Logs by Time

Recent logs:

```bash
journalctl -u nginx --since "10 minutes ago"
```

Logs from today:

```bash
journalctl -u nginx --since today
```

This is useful when you know approximately when a problem started.

---

# 26. Boot Logs

View logs from the current boot:

```bash
journalctl -b
```

View logs from the previous boot:

```bash
journalctl -b -1
```

This is useful when a problem appears after rebooting.

Example:

```text
Server rebooted
     ↓
Application no longer works
     ↓
journalctl -b
     ↓
Investigate startup errors
```

---

# 27. Errors in the Journal

Show error-level messages:

```bash
journalctl -p err
```

For the current boot:

```bash
journalctl -b -p err
```

A common troubleshooting pattern is:

```bash
systemctl --failed
journalctl -b -p err
```

---

# 28. Inspecting a Service's Unit File

To see how systemd defines a service:

```bash
systemctl cat <service>
```

Example:

```bash
systemctl cat ssh
```

You may see a file containing sections such as:

```ini
[Unit]
Description=Example Service

[Service]
ExecStart=/path/to/application

[Install]
WantedBy=multi-user.target
```

Do not worry if every directive is unfamiliar yet. We will learn the important ones step by step.

---

# 29. Basic systemd Service File Structure

A `.service` file commonly contains sections like these:

```ini
[Unit]
...

[Service]
...

[Install]
...
```

## `[Unit]`

Contains general information and relationships with other units.

Example:

```ini
[Unit]
Description=My Application
After=network.target
```

`After=` describes ordering.

It does not automatically mean the application requires the other unit; it specifies startup order.

---

## `[Service]`

Defines how the service actually runs.

Example:

```ini
[Service]
ExecStart=/usr/bin/python3 /opt/myapp/app.py
User=myapp
Restart=on-failure
```

Important directives:

### `ExecStart`

Command used to start the service.

### `User`

The user account under which the service runs.

### `Restart`

Defines when systemd should restart the service.

---

## `[Install]`

Defines how the service can be enabled.

Example:

```ini
[Install]
WantedBy=multi-user.target
```

You do not need to memorize targets yet.

For now, remember:

> `[Install]` helps define how a service participates in startup when it is enabled.

---

# 30. Why `User=` Matters for Security

Suppose an application does not need root privileges.

It is safer to run it as a dedicated user:

```ini
[Service]
User=myapp
```

rather than:

```ini
[Service]
User=root
```

Why?

If the application is compromised, running as a low-privileged user can limit what the attacker can access.

This follows the security principle of:

> **Least privilege**

This concept will become very important in your DevSecOps learning.

---

# 31. Restart Policies

A service can be configured to restart when it fails.

Example:

```ini
Restart=on-failure
```

Common values include:

```text
no
on-success
on-failure
always
```

For example:

```ini
Restart=on-failure
```

means systemd should restart the service when it terminates due to failure.

Be careful with restart policies. A broken service that restarts endlessly can make troubleshooting harder and may consume resources.

---

# 32. `daemon-reload`

Suppose you edit a service unit file.

systemd may still be using the previously loaded definition.

After changing the unit file, run:

```bash
sudo systemctl daemon-reload
```

This tells systemd:

> "The unit definitions may have changed. Read them again."

Important:

```text
daemon-reload
→ reloads systemd's unit configuration
→ does NOT automatically restart the service
```

Typical workflow:

```text
Edit service file
      ↓
 daemon-reload
      ↓
 restart service
      ↓
 check status
```

---

# 33. `systemctl show`

`systemctl show` displays detailed properties of a unit.

Example:

```bash
systemctl show nginx
```

You can request one property:

```bash
systemctl show nginx -p MainPID
```

Other useful properties include:

```text
MainPID
User
ActiveState
SubState
ExecStart
Restart
```

This is especially useful for scripting and troubleshooting.

---

# 34. Connecting a Service to Its Process

You can find the main PID of a service:

```bash
systemctl show nginx -p MainPID
```

Then inspect that process:

```bash
ps -p <PID> -f
```

This connects your previous chapter with this one:

```text
systemd
   ↓
nginx.service
   ↓
MainPID
   ↓
nginx process
```

You should become comfortable moving between these layers.

---

# 35. Service Dependencies

Applications sometimes need other services or system components.

For example:

```text
Web application
      │
      ├── network
      │
      └── database
```

systemd can manage relationships and dependencies between units.

View dependencies:

```bash
systemctl list-dependencies <service>
```

Example:

```bash
systemctl list-dependencies nginx
```

You do not need to master advanced dependency configuration yet. Just understand why dependencies exist.

---

# 36. A Real Troubleshooting Workflow

Imagine your application is not working after a reboot.

Do **not** randomly restart everything.

Use a structured investigation.

## Step 1 — Check the service

```bash
systemctl status myapp
```

Ask:

```text
Is it active?
Is it failed?
What is the main PID?
Does status show an obvious error?
```

## Step 2 — Check failed units

```bash
systemctl --failed
```

## Step 3 — Check logs

```bash
journalctl -u myapp -n 100
```

## Step 4 — Check the unit definition

```bash
systemctl cat myapp
```

## Step 5 — Check the process

```bash
systemctl show myapp -p MainPID
```

Then:

```bash
ps -p <PID> -f
```

## Step 6 — Check external causes

Possible causes include:

- Wrong executable path
- Wrong permissions
- Missing files
- Missing environment variables
- Invalid configuration
- Port already in use
- Dependency unavailable
- Application crash

## Step 7 — Fix and verify

After changing a unit file:

```bash
sudo systemctl daemon-reload
```

Then:

```bash
sudo systemctl restart myapp
```

Finally:

```bash
systemctl status myapp
```

---

# 37. Example: Port Already in Use

Suppose an application fails because it cannot bind to port `8080`.

Check listening ports:

```bash
ss -ltnp
```

Or:

```bash
ss -ltnp | grep :8080
```

You may discover another process is already using the port.

This gives you a useful troubleshooting chain:

```text
Service failure
     ↓
Read logs
     ↓
Error mentions port
     ↓
Use ss
     ↓
Find conflicting process
```

Networking will be covered in much greater detail in a later chapter.

---

# 38. Security Relevance of systemd

systemd is not just an administration tool. It is also security-relevant.

An attacker who gains access to a Linux machine may attempt to create persistence.

One possible technique is abusing startup mechanisms so malicious code runs automatically.

When investigating a Linux machine, you may inspect service units using:

```bash
systemctl list-unit-files --type=service
```

Then inspect suspicious services:

```bash
systemctl status suspicious.service
```

View its definition:

```bash
systemctl cat suspicious.service
```

Inspect its start command:

```bash
systemctl show suspicious.service -p ExecStart
```

Read logs:

```bash
journalctl -u suspicious.service
```

Do not blindly delete or stop suspicious services during an actual incident. Preserve evidence and follow the incident-response procedure used by the environment.

---

# 39. Why systemd Matters in DevSecOps

In DevSecOps, you will work with applications running on Linux servers, virtual machines, cloud instances, build agents, and sometimes inside containers.

A simplified deployment flow might look like:

```text
Developer
    ↓
Git repository
    ↓
CI/CD pipeline
    ↓
Build + tests + security scans
    ↓
Deployment
    ↓
Linux server
    ↓
systemd service
    ↓
Application process
    ↓
Logs + monitoring
```

You may need to:

- Start an application after deployment
- Configure automatic startup
- Restart a failed application
- Read application logs
- Diagnose boot-time failures
- Run an application as a non-root user
- Configure restart behavior
- Investigate suspicious persistence

That is why systemd is directly relevant to your DevSecOps path.

---

# 40. Hands-On Lab 1 — Explore PID 1

Run:

```bash
ps -p 1 -f
```

Then:

```bash
readlink -f /sbin/init
```

### Questions

1. What is PID 1 on your system?
2. Is it systemd?
3. What executable does `/sbin/init` point to?

Write the answers in your notes.

---

# 41. Hands-On Lab 2 — Inspect a Service

Choose a service available on your system.

For example:

```bash
systemctl status ssh
```

or:

```bash
systemctl status sshd
```

Record:

```text
Service name:
Active state:
Enabled/disabled:
Main PID:
Description:
```

Then run:

```bash
systemctl is-active ssh
```

and:

```bash
systemctl is-enabled ssh
```

Do not assume the two answers will be the same.

---

# 42. Hands-On Lab 3 — Explore Logs

Run:

```bash
journalctl -u ssh -n 30
```

Then:

```bash
journalctl -u ssh -f
```

Stop with:

```text
Ctrl + C
```

Now try:

```bash
journalctl -b -p err
```

Your goal is to become comfortable reading logs rather than being afraid of them.

---

# 43. Hands-On Lab 4 — Connect systemd to Processes

Run:

```bash
systemctl show ssh -p MainPID
```

Suppose the result is:

```text
MainPID=1234
```

Then:

```bash
ps -p 1234 -f
```

You have just traced:

```text
Service
   ↓
Main PID
   ↓
Process
```

This connection is extremely important.

---

# 44. Mini Project — Create a systemd Service

> **Goal:** Run your own Python application as a Linux service.

This project is designed to connect your Python and Linux learning.

## Step 1 — Create a Python application

Create:

```text
/opt/devsecops/devsecops_app.py
```

Example:

```python
import time

while True:
    print("DevSecOps application is running")
    time.sleep(30)
```

For a real deployment, you would normally also configure proper logging instead of relying only on standard output. For this beginner lab, standard output is enough to demonstrate journal logging.

## Step 2 — Create a dedicated user

Create a service user:

```bash
sudo useradd --system --no-create-home --shell /usr/sbin/nologin devsecops
```

This is an example of least-privilege thinking.

## Step 3 — Create the service file

Create:

```text
/etc/systemd/system/devsecops-app.service
```

Use:

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

## Step 4 — Tell systemd to reread unit files

```bash
sudo systemctl daemon-reload
```

## Step 5 — Start the application

```bash
sudo systemctl start devsecops-app
```

## Step 6 — Check status

```bash
systemctl status devsecops-app
```

## Step 7 — Read logs

```bash
journalctl -u devsecops-app
```

Live:

```bash
journalctl -u devsecops-app -f
```

## Step 8 — Enable startup

```bash
sudo systemctl enable devsecops-app
```

Or enable and start together:

```bash
sudo systemctl enable --now devsecops-app
```

## Step 9 — Inspect the process

```bash
systemctl show devsecops-app -p MainPID
```

Then inspect the returned PID:

```bash
ps -p <PID> -f
```

### What you should understand after this project

You should be able to explain:

```text
Python script
     ↓
systemd service file
     ↓
systemctl
     ↓
systemd
     ↓
Python process
     ↓
journal logs
```

---

# 45. Common Mistakes

## Mistake 1 — Confusing start and enable

Wrong assumption:

```text
enable = start
```

Correct:

```text
start  → run now
 enable → start automatically at boot
```

---

## Mistake 2 — Running everything as root

Do not automatically configure:

```ini
User=root
```

Use a dedicated account when root privileges are unnecessary.

---

## Mistake 3 — Forgetting `daemon-reload`

After modifying a service unit file:

```bash
sudo systemctl daemon-reload
```

Otherwise systemd may continue using the previously loaded definition.

---

## Mistake 4 — Immediately using `kill -9`

You learned in the previous chapter that force-killing a process is usually not the first option.

Similarly, when a service fails, investigate its status and logs before taking destructive action.

---

## Mistake 5 — Ignoring logs

A status screen may tell you:

```text
failed
```

The journal may tell you **why** it failed.

Always learn to use:

```bash
journalctl -u <service>
```

---

# 46. Essential Command Cheat Sheet

| Command | Purpose |
|---|---|
| `systemctl status SERVICE` | Show service status |
| `systemctl start SERVICE` | Start service now |
| `systemctl stop SERVICE` | Stop service now |
| `systemctl restart SERVICE` | Restart service |
| `systemctl reload SERVICE` | Reload service configuration when supported |
| `systemctl enable SERVICE` | Enable startup at boot |
| `systemctl disable SERVICE` | Disable startup at boot |
| `systemctl enable --now SERVICE` | Enable and start |
| `systemctl disable --now SERVICE` | Disable and stop |
| `systemctl is-active SERVICE` | Check runtime state |
| `systemctl is-enabled SERVICE` | Check boot configuration |
| `systemctl --failed` | Show failed units |
| `systemctl list-units --type=service` | List loaded service units |
| `systemctl list-unit-files --type=service` | List installed service unit files |
| `systemctl cat SERVICE` | Show unit configuration |
| `systemctl show SERVICE` | Show detailed properties |
| `systemctl daemon-reload` | Reload unit definitions |
| `journalctl -u SERVICE` | View service logs |
| `journalctl -u SERVICE -f` | Follow service logs |
| `journalctl -b` | View current boot logs |
| `journalctl -b -1` | View previous boot logs |
| `journalctl -p err` | Show error-level logs |
| `systemctl list-dependencies SERVICE` | Show dependencies |

---

# 47. Knowledge Checklist

Before moving to the next chapter, make sure you can:

- [ ] Explain what a Linux service is.
- [ ] Explain what a daemon is.
- [ ] Explain what systemd does.
- [ ] Explain why systemd commonly has PID 1.
- [ ] Explain what a systemd unit is.
- [ ] Explain what a `.service` unit represents.
- [ ] Use `systemctl status`.
- [ ] Start and stop a service.
- [ ] Restart and reload a service.
- [ ] Explain active vs enabled.
- [ ] Enable and disable a service.
- [ ] Explain what masking does.
- [ ] Find failed services.
- [ ] Use `journalctl`.
- [ ] Read logs for a specific service.
- [ ] Read logs from a particular boot.
- [ ] Understand the basic structure of a service unit file.
- [ ] Explain `ExecStart`.
- [ ] Explain `User`.
- [ ] Explain `Restart`.
- [ ] Explain `daemon-reload`.
- [ ] Find the main PID of a service.
- [ ] Connect a systemd service to its process.
- [ ] Perform basic service troubleshooting.
- [ ] Explain why service configuration is security-relevant.
- [ ] Create and manage a simple Python systemd service.

---

# 48. Final Mental Model

Keep this model in your head:

```text
                 Linux System
                      │
                      ▼
              systemd (PID 1)
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
       Service      Service     Service
          │           │           │
          ▼           ▼           ▼
       Process      Process     Process
          │           │           │
          └───────────┼───────────┘
                      ▼
                    Logs
                      │
                      ▼
                journalctl
```

And remember these differences:

```text
start
→ start a service now

stop
→ stop a service now

restart
→ stop + start

reload
→ reread configuration when supported

enable
→ configure automatic startup at boot

disable
→ remove automatic startup at boot

daemon-reload
→ make systemd reread unit definitions

active
→ running now

enabled
→ configured for boot
```

---

# 49. What's Next?

Your Linux path now becomes:

```text
Processes & Process Management
            ↓
       systemd & Services
            ↓
   Chapter 13: SSH & Remote Administration
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

The next chapter will teach **SSH from the ground up**, including how remote Linux administration works, keys, authentication, permissions, secure configuration, and DevSecOps use cases.
