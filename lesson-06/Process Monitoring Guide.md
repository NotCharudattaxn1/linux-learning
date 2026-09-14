# Process Monitoring Guide – Linux

## Objective

In this practical exercise, you will learn how to:

1. Start multiple background processes.
2. Monitor processes using `top`.
3. Find Process IDs (PIDs).
4. Stop a process gracefully.
5. Forcefully terminate a process.
6. Record and analyze your observations.

---

# 1. Understanding the Scenario

A **process** is a running instance of a program.

For this exercise, we will use the `sleep` command because it creates simple processes that remain active for a specified amount of time.

For example:

```bash
sleep 300
```

This process does nothing for **300 seconds** before exiting.

Adding `&` runs it in the background:

```bash
sleep 300 &
```

This allows you to continue using the terminal while the process runs.

---

# 2. Start Multiple Background Processes

Open a terminal and run:

```bash
sleep 300 &
sleep 400 &
sleep 500 &
```

You should see output similar to:

```text
[1] 1234
[2] 1235
[3] 1236
```

The numbers will be different on your system.

### Understanding the Output

```text
[1] 1234
```

* `[1]` → Job number assigned by the shell.
* `1234` → PID (Process ID).

> The PID uniquely identifies a running process.

---

# 3. View Background Jobs

Run:

```bash
jobs
```

Example output:

```text
[1]   Running                 sleep 300 &
[2]-  Running                 sleep 400 &
[3]+  Running                 sleep 500 &
```

This command shows the background jobs started from the current terminal.

---

# 4. Monitor Processes Using `top`

Run:

```bash
top
```

The `top` command displays running processes and system resource usage in real time.

Look for your `sleep` processes.

You may see something similar to:

```text
PID      USER      PR   NI   VIRT   RES   SHR   S   %CPU   %MEM   COMMAND

1234     user      20    0   ...    ...   ...   S   0.0    0.0    sleep
1235     user      20    0   ...    ...   ...   S   0.0    0.0    sleep
1236     user      20    0   ...    ...   ...   S   0.0    0.0    sleep
```

### Important Columns

| Column    | Meaning                  |
| --------- | ------------------------ |
| `PID`     | Process ID               |
| `USER`    | User running the process |
| `PR`      | Process priority         |
| `NI`      | Nice value               |
| `S`       | Process state            |
| `%CPU`    | CPU usage                |
| `%MEM`    | Memory usage             |
| `COMMAND` | Program name             |

---

## Process State

The `sleep` processes will normally appear in the following state:

```text
S
```

`S` means:

> **Interruptible sleep**

The process is waiting and is not actively using the CPU.

---

# 5. Find Process PIDs

You can find the PID using:

```bash
ps aux | grep sleep
```

Example:

```text
user     1234  0.0  0.0  ...  sleep 300
user     1235  0.0  0.0  ...  sleep 400
user     1236  0.0  0.0  ...  sleep 500
```

Alternatively:

```bash
pgrep sleep
```

Example:

```text
1234
1235
1236
```

You can also use:

```bash
ps -ef | grep sleep
```

---

# 6. Stop One Process Gracefully

Choose one PID.

For example:

```text
PID: 1234
```

Send the default termination signal:

```bash
kill 1234
```

This sends:

```text
SIGTERM (Signal 15)
```

`SIGTERM` requests that the process terminate gracefully.

Verify whether the process stopped:

```bash
ps -p 1234
```

If the process has terminated, you should not see the process in the output.

You can also check:

```bash
jobs
```

---

# 7. Stop Another Process Forcefully

Choose another PID.

For example:

```text
PID: 1235
```

Run:

```bash
kill -9 1235
```

This sends:

```text
SIGKILL (Signal 9)
```

`SIGKILL` immediately terminates the process.

The process cannot:

* Ignore the signal.
* Perform cleanup operations.
* Gracefully close resources.

Verify that it has stopped:

```bash
ps -p 1235
```

---

# 8. Compare Graceful vs Forceful Termination

| Method               | Command       | Signal         | Description                              |
| -------------------- | ------------- | -------------- | ---------------------------------------- |
| Graceful termination | `kill PID`    | `SIGTERM (15)` | Requests the process to terminate safely |
| Forceful termination | `kill -9 PID` | `SIGKILL (9)`  | Immediately terminates the process       |

---

# 9. Monitor the Changes

Run:

```bash
top
```

Observe what happens after terminating the processes.

You should notice:# Process Monitoring Guide – Linux

## Objective

In this practical exercise, you will learn how to:

1. Start multiple background processes.
2. Monitor processes using `top`.
3. Find Process IDs (PIDs).
4. Stop a process gracefully.
5. Forcefully terminate a process.
6. Record and analyze your observations.

---

# 1. Understanding the Scenario

A **process** is a running instance of a program.

For this exercise, we will use the `sleep` command because it creates simple processes that remain active for a specified amount of time.

For example:

```bash
sleep 300
```

This process does nothing for **300 seconds** before exiting.

Adding `&` runs it in the background:

```bash
sleep 300 &
```

This allows you to continue using the terminal while the process runs.

---

# 2. Start Multiple Background Processes

Open a terminal and run:

```bash
sleep 300 &
sleep 400 &
sleep 500 &
```

You should see output similar to:

```text
[1] 1234
[2] 1235
[3] 1236
```

The numbers will be different on your system.

### Understanding the Output

```text
[1] 1234
```

* `[1]` → Job number assigned by the shell.
* `1234` → PID (Process ID).

> The PID uniquely identifies a running process.

---

# 3. View Background Jobs

Run:

```bash
jobs
```

Example output:

```text
[1]   Running                 sleep 300 &
[2]-  Running                 sleep 400 &
[3]+  Running                 sleep 500 &
```

This command shows the background jobs started from the current terminal.

---

# 4. Monitor Processes Using `top`

Run:

```bash
top
```

The `top` command displays running processes and system resource usage in real time.

Look for your `sleep` processes.

You may see something similar to:

```text
PID      USER      PR   NI   VIRT   RES   SHR   S   %CPU   %MEM   COMMAND

1234     user      20    0   ...    ...   ...   S   0.0    0.0    sleep
1235     user      20    0   ...    ...   ...   S   0.0    0.0    sleep
1236     user      20    0   ...    ...   ...   S   0.0    0.0    sleep
```

### Important Columns

| Column    | Meaning                  |
| --------- | ------------------------ |
| `PID`     | Process ID               |
| `USER`    | User running the process |
| `PR`      | Process priority         |
| `NI`      | Nice value               |
| `S`       | Process state            |
| `%CPU`    | CPU usage                |
| `%MEM`    | Memory usage             |
| `COMMAND` | Program name             |

---

## Process State

The `sleep` processes will normally appear in the following state:

```text
S
```

`S` means:

> **Interruptible sleep**

The process is waiting and is not actively using the CPU.

---

# 5. Find Process PIDs

You can find the PID using:

```bash
ps aux | grep sleep
```

Example:

```text
user     1234  0.0  0.0  ...  sleep 300
user     1235  0.0  0.0  ...  sleep 400
user     1236  0.0  0.0  ...  sleep 500
```

Alternatively:

```bash
pgrep sleep
```

Example:

```text
1234
1235
1236
```

You can also use:

```bash
ps -ef | grep sleep
```

---

# 6. Stop One Process Gracefully

Choose one PID.

For example:

```text
PID: 1234
```

Send the default termination signal:

```bash
kill 1234
```

This sends:

```text
SIGTERM (Signal 15)
```

`SIGTERM` requests that the process terminate gracefully.

Verify whether the process stopped:

```bash
ps -p 1234
```

If the process has terminated, you should not see the process in the output.

You can also check:

```bash
jobs
```

---

# 7. Stop Another Process Forcefully

Choose another PID.

For example:

```text
PID: 1235
```

Run:

```bash
kill -9 1235
```

This sends:

```text
SIGKILL (Signal 9)
```

`SIGKILL` immediately terminates the process.

The process cannot:

* Ignore the signal.
* Perform cleanup operations.
* Gracefully close resources.

Verify that it has stopped:

```bash
ps -p 1235
```

---

# 8. Compare Graceful vs Forceful Termination

| Method               | Command       | Signal         | Description                              |
| -------------------- | ------------- | -------------- | ---------------------------------------- |
| Graceful termination | `kill PID`    | `SIGTERM (15)` | Requests the process to terminate safely |
| Forceful termination | `kill -9 PID` | `SIGKILL (9)`  | Immediately terminates the process       |

---

# 9. Monitor the Changes

Run:

```bash
top
```

Observe what happens after terminating the processes.

You should notice:

* The terminated process disappears from the process list.
* The number of running processes changes.
* System resource usage may change.

Press:

```text
q
```

to exit `top`.

---

# 10. Record Your Observations

Complete the following table after performing the exercise.

| Process     | PID    | Initial State | CPU Usage      | Termination Method | Result |
| ----------- | ------ | ------------- | -------------- | ------------------ | ------ |
| `sleep 300` | ______ | ______        | ______         | `kill PID`         | ______ |
| `sleep 400` | ______ | ______        | ______         | `kill -9 PID`      | ______ |
| `sleep 500` | ______ | ______        | Not terminated | Still running      |        |

---

# 11. Example Observations

### Observation 1: Background Processes

Running:

```bash
sleep 300 &
```

creates a new process that runs independently in the background.

The shell immediately returns control to the user.

---

### Observation 2: Process IDs

Each running process has a unique:

```text
PID (Process ID)
```

The PID can be used to:

* Monitor a process.
* Identify a process.
* Send signals.
* Terminate a process.

---

### Observation 3: CPU Usage

The `sleep` processes usually consume approximately:

```text
0.0% CPU
```

This happens because the processes are waiting rather than performing computational work.

---

### Observation 4: Process State

The `sleep` processes usually have the state:

```text
S
```

This indicates that they are sleeping and waiting.

---

### Observation 5: Graceful Termination

Using:

```bash
kill PID
```

sends the `SIGTERM` signal.

This gives a process an opportunity to:

* Perform cleanup.
* Close files.
* Release resources.
* Shut down properly.

---

### Observation 6: Forceful Termination

Using:

```bash
kill -9 PID
```

sends the `SIGKILL` signal.

The process is immediately terminated by the operating system.

Because the process cannot perform cleanup, `SIGKILL` should generally be used only when normal termination fails.

---

# 12. Verify Remaining Processes

Check the remaining processes:

```bash
pgrep sleep
```

Or:

```bash
ps aux | grep sleep
```

You should find that only the process you did not terminate is still running.

---

# 13. Clean Up

After completing the exercise, terminate the remaining `sleep` process.

First, find its PID:

```bash
pgrep sleep
```

Then terminate it:

```bash
kill PID
```

Verify:

```bash
pgrep sleep
```

If no output appears, the `sleep` processes have been terminated.

---

# 14. DevSecOps Perspective

Process monitoring is important in DevSecOps because running processes provide information about the behavior of a system.

Monitoring processes can help detect:

* Unexpected applications.
* Malware.
* Cryptominers.
* Unauthorized services.
* Excessive CPU usage.
* Suspicious parent-child process relationships.
* Compromised applications.

For example:

```text
Web Server
    │
    └── bash
          │
          └── unknown_script
```

An unusual process tree may indicate suspicious activity.

Security monitoring and runtime security tools analyze processes to identify abnormal behavior.

---

# Key Takeaways

* A **process** is a running instance of a program.
* Every process has a unique **PID**.
* `&` runs a command in the background.
* `jobs` displays background jobs from the current shell.
* `top` provides real-time process monitoring.
* `ps` and `pgrep` can be used to find processes.
* `kill PID` sends `SIGTERM` for graceful termination.
* `kill -9 PID` sends `SIGKILL` for forceful termination.
* Process monitoring is important for both **system administration and security**.

---

# Commands Used

```bash
# Start background processes
sleep 300 &
sleep 400 &
sleep 500 &

# View shell jobs
jobs

# Monitor processes
top

# Find process IDs
pgrep sleep

# View processes
ps aux | grep sleep

# Gracefully terminate a process
kill PID

# Forcefully terminate a process
kill -9 PID

# Verify a specific process
ps -p PID
```

---

## Final Practical Challenge

Repeat this exercise using different processes.

For example:

```bash
yes > /dev/null &
```

⚠️ **Warning:** This process continuously consumes CPU.

Monitor it using:

```bash
top
```

Observe the CPU usage, then terminate it gracefully:

```bash
kill PID
```

This demonstrates how a CPU-intensive process can affect system performance.


* The terminated process disappears from the process list.
* The number of running processes changes.
* System resource usage may change.

Press:

```text
q
```

to exit `top`.

---

# 10. Record Your Observations

Complete the following table after performing the exercise.

| Process     | PID    | Initial State | CPU Usage      | Termination Method | Result |
| ----------- | ------ | ------------- | -------------- | ------------------ | ------ |
| `sleep 300` | 19039  |  ______        | ______         | `kill PID`         | ______ |
| `sleep 400` | 19040  | ______        | ______         | `kill -9 PID`      | ______ |
| `sleep 500` | 19041  | ______        | Not terminated | Still running      |        |

---

# 11. Example Observations

### Observation 1: Background Processes

Running:

```bash
sleep 300 &
```

creates a new process that runs independently in the background.

The shell immediately returns control to the user.

---

### Observation 2: Process IDs

Each running process has a unique:

```text
PID (Process ID)
```

The PID can be used to:

* Monitor a process.
* Identify a process.
* Send signals.
* Terminate a process.

---

### Observation 3: CPU Usage

The `sleep` processes usually consume approximately:

```text
0.0% CPU
```

This happens because the processes are waiting rather than performing computational work.

---

### Observation 4: Process State

The `sleep` processes usually have the state:

```text
S
```

This indicates that they are sleeping and waiting.

---

### Observation 5: Graceful Termination

Using:

```bash
kill PID
```

sends the `SIGTERM` signal.

This gives a process an opportunity to:

* Perform cleanup.
* Close files.
* Release resources.
* Shut down properly.

---

### Observation 6: Forceful Termination

Using:

```bash
kill -9 PID
```

sends the `SIGKILL` signal.

The process is immediately terminated by the operating system.

Because the process cannot perform cleanup, `SIGKILL` should generally be used only when normal termination fails.

---

# 12. Verify Remaining Processes

Check the remaining processes:

```bash
pgrep sleep
```

Or:

```bash
ps aux | grep sleep
```

You should find that only the process you did not terminate is still running.

---

# 13. Clean Up

After completing the exercise, terminate the remaining `sleep` process.

First, find its PID:

```bash
pgrep sleep
```

Then terminate it:

```bash
kill PID
```

Verify:

```bash
pgrep sleep
```

If no output appears, the `sleep` processes have been terminated.

---

# 14. DevSecOps Perspective

Process monitoring is important in DevSecOps because running processes provide information about the behavior of a system.

Monitoring processes can help detect:

* Unexpected applications.
* Malware.
* Cryptominers.
* Unauthorized services.
* Excessive CPU usage.
* Suspicious parent-child process relationships.
* Compromised applications.

For example:

```text
Web Server
    │
    └── bash
          │
          └── unknown_script
```

An unusual process tree may indicate suspicious activity.

Security monitoring and runtime security tools analyze processes to identify abnormal behavior.

---

# Key Takeaways

* A **process** is a running instance of a program.
* Every process has a unique **PID**.
* `&` runs a command in the background.
* `jobs` displays background jobs from the current shell.
* `top` provides real-time process monitoring.
* `ps` and `pgrep` can be used to find processes.
* `kill PID` sends `SIGTERM` for graceful termination.
* `kill -9 PID` sends `SIGKILL` for forceful termination.
* Process monitoring is important for both **system administration and security**.

---

# Commands Used

```bash
# Start background processes
sleep 300 &
sleep 400 &
sleep 500 &

# View shell jobs
jobs

# Monitor processes
top

# Find process IDs
pgrep sleep

# View processes
ps aux | grep sleep

# Gracefully terminate a process
kill PID

# Forcefully terminate a process
kill -9 PID

# Verify a specific process
ps -p PID
```

---

## Final Practical Challenge

Repeat this exercise using different processes.

For example:

```bash
yes > /dev/null &
```

⚠️ **Warning:** This process continuously consumes CPU.

Monitor it using:

```bash
top
```

Observe the CPU usage, then terminate it gracefully:

```bash
kill PID
```

This demonstrates how a CPU-intensive process can affect system performance.
