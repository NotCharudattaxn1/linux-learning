Chapter 12 — systemd and Linux Services

Track: Linux for DevSecOps
Level: Beginner → Intermediate
Prerequisite: Basic Linux commands and Processes & Process Management
Goal: Understand what Linux services are, how systemd manages them, how to control them, and how to troubleshoot them.

1. Why Do We Need Services?

Before learning systemd, first understand the problem it solves.

Imagine you have a Linux server running an application such as:

A web server

An SSH server

A database

A Docker daemon

A monitoring agent

These applications often need to:

Start automatically when the computer boots

Run in the background

Stop cleanly when the system shuts down

Restart if they crash

Write useful logs

Run as a specific user

Depend on other services being available first

Managing all of this manually would be difficult.

Linux therefore uses a service manager.

On most modern Linux distributions, that service manager is systemd.

2. What Is a Service?

A service is a program designed to perform a function in the background, usually without requiring a user to interact with it directly.

For example:

SSH server
   ↓
waits for incoming SSH connections

A web server:

Web server
   ↓
waits for HTTP/HTTPS requests

A database:

Database service
   ↓
waits for applications to request data

A service is usually long-running.

Service vs Normal Command

Suppose you run:

ls

The command executes, prints the result, and exits.

A service such as an SSH server usually behaves differently:

Start service
     ↓
Keep running
     ↓
Wait for requests
     ↓
Handle requests
     ↓
Continue running

3. What Is a Daemon?

You will often hear the word daemon in Linux.

A daemon is simply a background process that provides some service or function.

Examples:

sshd       → SSH daemon
cron       → scheduling daemon
containerd → container runtime daemon

Many Linux daemons have names ending in d, although this is only a naming convention.

For example:

sshd

is commonly the process that provides the SSH server functionality.

Important

Do not think:

daemon = a special kind of hardware

Think:

daemon = background process providing a service

4. What Is systemd?

systemd is the system and service manager used by many modern Linux distributions.

Its job is much bigger than simply starting programs.

It can:

Start services

Stop services

Restart services

Start services automatically at boot

Track running services

Manage service dependencies

Handle system startup

Collect logs through the systemd journal

Organize system states using targets

The main systemd process normally runs as:

PID 1

Check it:

ps -p 1 -f

You will commonly see something similar to:

UID   PID  PPID  CMD
root    1     0  /sbin/init

On a system using systemd, /sbin/init commonly points to systemd or launches it.

5. Connecting This to Processes

You just learned about processes in the previous chapter.

Now connect the ideas.

A process is a running program.

A service is a program that performs some ongoing background function.

systemd manages those services.

Conceptually:

systemd
   ↓
manages
service
   ↓
runs as
process(es)

For example:

systemd
   ↓
SSH service
   ↓
sshd process

This relationship is extremely important.

6. What Is systemctl?

systemctl is the main command-line tool used to communicate with systemd.

Think of it as:

systemctl = control panel for systemd

General syntax:

systemctl <action> <service>

For example:

systemctl status ssh

The exact service name can vary by Linux distribution. Some systems use ssh, while others use sshd.

7. Checking a Service

The first command you should learn is:

systemctl status <service>

Example:

systemctl status ssh

A status screen may contain information similar to:

● ssh.service - OpenSSH server daemon
     Loaded: loaded (...)
     Active: active (running)
   Main PID: 1234 (sshd)

Do not try to memorize the whole output.

Focus on these important parts.

Loaded

Tells you whether systemd knows about the service and has loaded its unit configuration.

Active

Tells you the current runtime state.

For example:

active (running)

means the service is currently running.

Main PID

Shows the main process associated with the service.

This connects systemd to what you learned about processes.

8. Understanding active and inactive

A service can be running:

active (running)

or not running:

inactive (dead)

There are other states as well, such as:

failed
activating
stopping

For now, the most important idea is:

active   → service is currently running
inactive → service is currently not running
failed   → systemd tried and something went wrong

9. Starting a Service

To start a service manually:

sudo systemctl start <service>

Example:

sudo systemctl start nginx

Then verify:

systemctl status nginx

Important

start means:

Start the service now.

It does not automatically mean the service will start after the next reboot.

That is what enable is for.

10. Stopping a Service

To stop a service:

sudo systemctl stop <service>

Example:

sudo systemctl stop nginx

Check:

systemctl status nginx

Remember:

start → run now
stop  → stop now

11. Restarting a Service

Sometimes an application needs to be completely stopped and started again.

Use:

sudo systemctl restart <service>

Example:

sudo systemctl restart nginx

Conceptually:

Running service
      ↓
    stop
      ↓
    start
      ↓
Running again

Restart can briefly interrupt the service, so do not use it blindly on production systems.

12. Reloading a Service

Some applications support a configuration reload without completely stopping the service.

sudo systemctl reload <service>

For example:

sudo systemctl reload nginx

Conceptually:

restart
→ stop process
→ start process again

reload
→ keep process running
→ ask application to reread configuration

Not every service supports reload.

13. The Very Important Difference: Start vs Enable

Beginners often confuse these two commands.

start

sudo systemctl start nginx

Means:

Start nginx now.

enable

sudo systemctl enable nginx

Means:

Configure nginx to start automatically during boot.

So:

start
→ affects the current session/runtime

 enable
→ affects future boot behavior

A service can therefore be:

Running but not enabled

or:

Not running but enabled for the next boot

14. Checking Whether a Service Is Active

Instead of reading the full status output, you can use:

systemctl is-active <service>

Example:

systemctl is-active nginx

Possible output:

active

or:

inactive

15. Checking Whether a Service Is Enabled

Use:

systemctl is-enabled <service>

Example:

systemctl is-enabled nginx

Possible output:

enabled

or:

disabled

Now the distinction should be clear:

is-active
→ Is it running right now?

is-enabled
→ Will it be configured to start automatically during boot?

16. Enable and Start at the Same Time

If you want a service to:

Start now

Start automatically on future boots

you can use:

sudo systemctl enable --now <service>

Example:

sudo systemctl enable --now nginx

This is a very useful command to know.

17. Disabling a Service

To stop a service from being automatically started at boot:

sudo systemctl disable <service>

Example:

sudo systemctl disable nginx

Remember:

disable
→ changes boot behavior

It does not necessarily stop the service that is already running.

To disable it and stop it now:

sudo systemctl disable --now nginx

18. Masking a Service

mask is stronger than disable.

sudo systemctl mask <service>

A masked service cannot normally be started until it is unmasked.

To undo it:

sudo systemctl unmask <service>

Think of it like this:

disable
→ don't automatically start it

mask
→ don't allow it to be started normally

Use masking carefully.

19. Finding Services

List currently loaded service units:

systemctl list-units --type=service

List installed service unit files:

systemctl list-unit-files --type=service

These commands are useful when you do not know the exact service name.

You can also search:

systemctl list-unit-files --type=service | grep ssh

20. Failed Services

One of the most useful troubleshooting commands is:

systemctl --failed

It shows units that are currently in a failed state.

Example:

UNIT          LOAD   ACTIVE SUB    DESCRIPTION
nginx.service loaded failed failed Web server

The important lesson is:

When a service fails, do not immediately restart it repeatedly. First investigate why it failed.

21. Understanding Logs

A service status may tell you:

The service failed.

But you usually need logs to answer:

Why did it fail?

This is where journalctl becomes important.

journalctl is the command used to read logs stored in the systemd journal.

22. journalctl Basics

View journal logs:

journalctl

Show the latest entries:

journalctl -n 50

Follow logs live:

journalctl -f

Press:

Ctrl + C

to stop following the logs.

23. Viewing Logs for One Service

This is especially important.

journalctl -u <service>

Example:

journalctl -u nginx

Here:

-u

means:

Show logs for this systemd unit.

Follow them live:

journalctl -u nginx -f

Show only the last 50 lines:

journalctl -u nginx -n 50

24. Viewing Logs from a Particular Time

You can restrict the logs to a time period.

Example:

journalctl -u nginx --since "10 minutes ago"

Current day's logs:

journalctl -u nginx --since today

This is useful when you know approximately when a problem started.

25. Viewing Logs From the Current Boot

Use:

journalctl -b

This shows logs from the current boot.

The previous boot can be inspected with:

journalctl -b -1

This is useful for diagnosing problems that appear after a reboot.

26. Error Logs

You can filter by priority.

For errors and more severe messages:

journalctl -p err

For the current boot:

journalctl -b -p err

A useful troubleshooting combination is:

journalctl -u nginx -b -p err

This asks for error-level messages from the nginx service during the current boot.

27. What Is a Unit?

You will see the word unit often when learning systemd.

A unit is a configuration object that systemd knows how to manage.

Common unit types include:

Unit type

Meaning

.service

Service

.socket

Socket-based activation/interface

.target

Group or system state

.timer

Scheduled activation

.mount

Mount point

.path

Watches a filesystem path

.slice

Resource-management group

For now, focus mainly on:

.service
.target
.timer

28. Service Unit Files

A systemd service is described by a unit file.

A simplified file may look like:

example.service

Common locations include:

/etc/systemd/system/
/usr/lib/systemd/system/
/lib/systemd/system/

The exact location depends on the Linux distribution and how the software was installed.

A useful command is:

systemctl cat <service>

Example:

systemctl cat ssh

This lets you inspect the unit configuration that systemd is using.

29. Anatomy of a Service File

A simple service file might look like this:

[Unit]
Description=My Example Application
After=network.target

[Service]
ExecStart=/usr/bin/myapp
User=myapp
Restart=on-failure

[Install]
WantedBy=multi-user.target

It may look complicated now, but the structure is simple.

There are three main sections:

[Unit]
[Service]
[Install]

30. The [Unit] Section

This section describes the unit and its relationships with other units.

Example:

[Unit]
Description=My Example Application
After=network.target

Description

A human-readable explanation.

After

Controls ordering.

For example:

After=network.target

means the service should be started after that target in the startup ordering.

Important:

After= controls ordering. It does not, by itself, mean the other unit is required.

Dependencies and ordering are related but not identical concepts.

31. The [Service] Section

This section explains how the actual application should run.

ExecStart

Specifies the command to start the application.

ExecStart=/usr/bin/myapp

User

Specifies which user should run the service.

User=myapp

This is important for security.

Running an application with unnecessary root privileges can increase the impact of a compromise.

Restart

Controls whether systemd should attempt to restart the service.

Example:

Restart=on-failure

This means systemd can restart it when it exits unsuccessfully.

32. The [Install] Section

This section describes how the service is integrated into system startup.

Example:

[Install]
WantedBy=multi-user.target

You do not need to memorize multi-user.target yet.

For now understand:

[Install]
→ helps define how the service is enabled

33. Reloading systemd Configuration

Suppose you create or modify a service unit file.

Systemd may not immediately know about your changes.

Run:

sudo systemctl daemon-reload

This tells systemd:

Reread the unit definitions from disk.

Important:

daemon-reload
≠
restart service

It only reloads the systemd configuration.

After changing a service, you may then need:

sudo systemctl restart myapp

34. Checking a Service's Main PID

Earlier you learned how to inspect processes with ps.

Now combine it with systemd.

Find the main PID:

systemctl show nginx -p MainPID

You may get:

MainPID=1234

Then inspect it:

ps -p 1234 -f

This creates the full connection:

systemd
   ↓
nginx.service
   ↓
Main PID
   ↓
nginx process

35. systemctl show

systemctl show displays detailed properties of a unit.

Example:

systemctl show nginx

Instead of everything, you can request one property:

systemctl show nginx -p MainPID

Other useful properties include:

ActiveState
SubState
MainPID
User
ExecStart
Restart

This format is especially useful in scripts and automation.

36. Service Dependencies

Real applications often depend on other system components.

For example:

Web application
       ↓
   database
       ↓
    storage

systemd can manage relationships between units.

Inspect dependencies:

systemctl list-dependencies <service>

Example:

systemctl list-dependencies nginx

This can help you understand why a service is starting, stopping, or waiting for something else.

37. Targets

A target is a systemd unit used to group units and represent a particular system state.

For example:

multi-user.target
graphical.target
rescue.target

For now, think of a target as:

a named system state/group of units

Check the default target:

systemctl get-default

On a server, you will commonly see:

multi-user.target

38. Why Targets Matter

A graphical desktop may need many graphical-related components.

A headless server may not.

Targets allow systemd to organize which units should be brought up for a particular state.

Conceptually:

Boot
 ↓
target
 ↓
required/associated units
 ↓
services

You do not need to master targets yet. That will become easier after practicing service management.

39. Real Troubleshooting Workflow

This is one of the most valuable parts of this chapter.

Imagine:

Your application is not working.

Do not immediately run random commands.

Use a process.

Step 1 — Check status

systemctl status myapp

Step 2 — Check whether systemd reports failures

systemctl --failed

Step 3 — Read logs

journalctl -u myapp -n 100

Step 4 — Inspect the unit configuration

systemctl cat myapp

Step 5 — Check the main PID

systemctl show myapp -p MainPID

Step 6 — Inspect the process

ps -p <PID> -f

Step 7 — Fix the root cause

Possible causes:

Wrong executable path

Missing file

Incorrect permissions

Configuration error

Missing dependency

Port already in use

Invalid environment variable

Application crash

Step 8 — Reload systemd if the unit file changed

sudo systemctl daemon-reload

Step 9 — Restart if appropriate

sudo systemctl restart myapp

Step 10 — Verify

systemctl status myapp

This workflow is much better than repeatedly restarting a broken service without investigating it.

40. DevSecOps Relevance

Why does this matter to you as a future DevSecOps engineer?

Because you will eventually work with:

Linux servers

Web applications

APIs

CI/CD systems

Docker hosts

Monitoring agents

Security scanners

Logging agents

Cloud virtual machines

Many of these components run as services.

A practical deployment can look like:

Developer
   ↓
Git
   ↓
CI/CD pipeline
   ↓
Build/Test/Security Scan
   ↓
Deploy to Linux server
   ↓
systemd service
   ↓
Application process
   ↓
Logs + Monitoring

Understanding systemd therefore helps connect your Linux knowledge to DevOps and DevSecOps.

41. Security Importance of Services

Services are also security-sensitive.

Every service can potentially:

Open a network port

Run with certain privileges

Access files

Access environment variables

Interact with other systems

Therefore you should ask:

What service is running?
Who runs it?
What does it execute?
What resources can it access?
What network port does it expose?
Does it need root privileges?

Useful commands include:

systemctl status <service>

systemctl cat <service>

systemctl show <service> -p User

and:

ss -ltnp

for listening TCP sockets.

42. Service Persistence and Security

During incident response, defenders may inspect systemd units because attackers can sometimes create or modify services to maintain persistence.

For example, investigate the list of installed service definitions:

systemctl list-unit-files --type=service

Inspect a suspicious service:

systemctl status suspicious.service

View its unit configuration:

systemctl cat suspicious.service

View its configured start command:

systemctl show suspicious.service -p ExecStart

Check logs:

journalctl -u suspicious.service

Do not immediately delete or stop suspicious infrastructure during a real security incident. Follow your incident-response process and preserve evidence where required.

43. Common Beginner Mistakes

Mistake 1 — Confusing start and enable

systemctl start nginx

does not mean:

start automatically after reboot

That is related to:

systemctl enable nginx

Mistake 2 — Using restart Without Looking at Logs

If a service repeatedly fails, restart is not the diagnosis.

First investigate:

systemctl status <service>
journalctl -u <service>

Mistake 3 — Forgetting daemon-reload

If you modify a custom unit file and do not run:

sudo systemctl daemon-reload

systemd may continue using the old unit definition.

Mistake 4 — Running Everything as Root

A service should generally run with the minimum privileges it needs.

For a custom service, a dedicated user is often safer than running the application as root.

Mistake 5 — Using kill -9 Instead of Understanding the Service

A service is normally managed by systemd.

If the process is misbehaving, understand the service configuration and logs instead of treating every problem as a generic process-killing problem.

44. Hands-On Lab 1 — Identify a Service

Choose a service installed on your Linux system.

For example:

systemctl status ssh

or, depending on the distribution:

systemctl status sshd

Answer:

Is the service running?

What is its PID?

Which user is running it?

Is it enabled?

What does the service do?

45. Hands-On Lab 2 — Active vs Enabled

Run:

systemctl is-active ssh

Then:

systemctl is-enabled ssh

Write the meanings in your own words:

active   = ______________________

enabled  = ______________________

This simple exercise is worth mastering.

46. Hands-On Lab 3 — Read Service Logs

Run:

journalctl -u ssh -n 30

Then follow them:

journalctl -u ssh -f

Stop with:

Ctrl + C

Try:

journalctl -u ssh --since "10 minutes ago"

47. Hands-On Lab 4 — Connect systemd to a Process

Check a service:

systemctl status ssh

Find its main PID:

systemctl show ssh -p MainPID

Then inspect that PID:

ps -p <PID> -f

You should be able to explain:

systemd
   ↓
ssh.service
   ↓
Main PID
   ↓
sshd process

This is the most important conceptual connection between this chapter and the previous one.

48. Hands-On Lab 5 — Find Failed Services

Run:

systemctl --failed

If there are failures, choose one and investigate with:

systemctl status <service>

Then:

journalctl -u <service> -n 100

The goal is not simply to make the service green again.

The goal is to understand why it failed.

49. Mini Project — Run a Python Application as a systemd Service

Project goal: Turn a simple Python program into a managed Linux service.

Step 1 — Create the Python application

Create:

/opt/devsecops/devsecops_app.py

Example:

import time

while True:
    print("DevSecOps application is running", flush=True)
    time.sleep(30)

The flush=True helps make the output available immediately to the service's output stream.

Step 2 — Create a dedicated user

sudo useradd --system --no-create-home --shell /usr/sbin/nologin devsecops

This creates a system account that is not intended for interactive login.

Step 3 — Create the service file

Create:

/etc/systemd/system/devsecops-app.service

Use:

[Unit]
Description=DevSecOps Demo Application
After=network.target

[Service]
User=devsecops
ExecStart=/usr/bin/python3 /opt/devsecops/devsecops_app.py
Restart=on-failure

[Install]
WantedBy=multi-user.target

Step 4 — Reload systemd

sudo systemctl daemon-reload

Step 5 — Start the service

sudo systemctl start devsecops-app

Step 6 — Check status

systemctl status devsecops-app

Step 7 — View logs

journalctl -u devsecops-app -n 20

Step 8 — Enable it at boot

sudo systemctl enable devsecops-app

Or do both operations together on a fresh setup:

sudo systemctl enable --now devsecops-app

Step 9 — Verify the process

systemctl show devsecops-app -p MainPID

Then:

ps -p <PID> -f

What You Should Learn From This Project

By the end, you should be able to explain:

Python program
      ↓
systemd unit
      ↓
service
      ↓
Python process
      ↓
journal logs

And you should understand why the application is safer to run as a dedicated user instead of automatically using root privileges.

50. Quick Reference

Service lifecycle

systemctl status SERVICE
systemctl start SERVICE
systemctl stop SERVICE
systemctl restart SERVICE
systemctl reload SERVICE

Boot behavior

systemctl enable SERVICE
systemctl disable SERVICE
systemctl enable --now SERVICE
systemctl disable --now SERVICE

State checking

systemctl is-active SERVICE
systemctl is-enabled SERVICE
systemctl --failed

Unit inspection

systemctl cat SERVICE
systemctl show SERVICE
systemctl show SERVICE -p MainPID
systemctl list-dependencies SERVICE

Journal/logging

journalctl
journalctl -u SERVICE
journalctl -u SERVICE -n 50
journalctl -u SERVICE -f
journalctl -b
journalctl -b -1
journalctl -p err

Configuration reload

systemctl daemon-reload

51. Command Meaning Table

Command

Meaning

systemctl status SERVICE

Show detailed service status

systemctl start SERVICE

Start service now

systemctl stop SERVICE

Stop service now

systemctl restart SERVICE

Stop and start service

systemctl reload SERVICE

Ask service to reload configuration, if supported

systemctl enable SERVICE

Configure automatic startup at boot

systemctl disable SERVICE

Remove automatic startup at boot

systemctl enable --now SERVICE

Enable and start

systemctl disable --now SERVICE

Disable and stop

systemctl is-active SERVICE

Check whether running now

systemctl is-enabled SERVICE

Check boot-time enablement

systemctl --failed

List failed units

systemctl cat SERVICE

Show unit configuration

systemctl show SERVICE

Show unit properties

systemctl daemon-reload

Reload systemd unit definitions

journalctl -u SERVICE

View logs for a service

journalctl -u SERVICE -f

Follow service logs

52. Knowledge Checklist

Before moving to the next chapter, you should be comfortable with:

Explaining what a Linux service is

Explaining what a daemon is

Explaining what systemd does

Knowing that systemd normally runs as PID 1

Using systemctl status

Starting and stopping a service

Restarting and reloading a service

Understanding start vs enable

Understanding active vs enabled

Enabling and disabling services

Understanding what mask does

Finding failed services

Reading service logs with journalctl

Checking logs from a particular boot

Understanding what a unit is

Understanding a basic .service file

Understanding ExecStart

Understanding User

Understanding Restart

Understanding daemon-reload

Finding a service's Main PID

Connecting a systemd service to a process

Using systemd when troubleshooting applications

Explaining why service configuration matters for security

53. Final Mental Model

Do not try to memorize everything at once.

Keep this model in your head:

Linux boots
    ↓
systemd (PID 1)
    ↓
starts/manages services
    ↓
services run processes
    ↓
processes perform the actual work
    ↓
logs record what happens

And when something breaks:

Problem
   ↓
systemctl status
   ↓
journalctl
   ↓
inspect configuration
   ↓
inspect process
   ↓
fix root cause
   ↓
restart/reload if appropriate
   ↓
verify

That troubleshooting workflow is one of the most useful Linux habits you can develop for DevSecOps.

54. What's Next?

After mastering services and systemd, the next topic is:

Processes & Process Management
            ↓
      systemd & Services
            ↓
    SSH & Remote Administration
            ↓
       Linux Networking
            ↓
    Storage & Filesystems
            ↓
          Firewalls
            ↓
      Bash & Automation
            ↓
  Linux Security & Hardening
            ↓
       Docker / Containers
            ↓
          DevSecOps

Next Chapter

SSH & Remote Linux Administration
