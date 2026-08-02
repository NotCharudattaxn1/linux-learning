# Linux Lesson 10: Bash Scripting Fundamentals

> **Module:** Linux Fundamentals
> **Difficulty:** Beginner → Intermediate
> **Prerequisites:** Linux Lessons 1–9
> **Estimated Study Time:** 4–5 Hours

---

# Learning Objectives

After completing this lesson, you should be able to:

- Understand what Bash is.
- Write and execute Bash scripts.
- Use variables.
- Accept user input.
- Work with command-line arguments.
- Use conditional statements.
- Create loops.
- Write reusable functions.
- Understand exit codes.
- Build automation scripts.

---

# Introduction

Imagine you need to perform the same task every day.

For example:

- Create backups.
- Update Linux packages.
- Restart web servers.
- Create user accounts.
- Check disk space.
- Monitor CPU usage.

Typing these commands repeatedly wastes time.

Instead, we automate them using **Bash scripts**.

---

# What is Bash?

Bash stands for:

**Bourne Again Shell**

It is:

- A command interpreter.
- A scripting language.
- The default shell on many Linux distributions.

Bash can:

- Execute Linux commands.
- Perform calculations.
- Read files.
- Automate repetitive tasks.
- Control system administration tasks.

---

# What is a Shell Script?

A shell script is simply a text file containing Linux commands executed in sequence.

Example:

```
backup.sh
```

Contents:

```bash
echo "Starting Backup..."
mkdir backup
cp report.txt backup/
echo "Backup Complete"
```

Instead of typing commands one by one, Linux executes them automatically.

---

# Creating Your First Script

Create a file:

```bash
nano hello.sh
```

Write:

```bash
#!/bin/bash

echo "Hello Linux!"
```

Save.

---

# Understanding the Shebang

First line:

```bash
#!/bin/bash
```

This tells Linux:

> Execute this script using the Bash interpreter.

Without it, Linux may not know which interpreter should run the file.

---

# Making a Script Executable

Scripts need execute permission.

```bash
chmod +x hello.sh
```

Verify:

```bash
ls -l
```

You should see:

```
-rwxr-xr-x
```

---

# Running Scripts

Method 1

```bash
./hello.sh
```

Method 2

```bash
bash hello.sh
```

---

# Comments

Single-line comment:

```bash
# This is a comment
```

Comments improve readability and maintenance.

---

# Variables

Create variables:

```bash
name="Max"
```

Use variables:

```bash
echo $name
```

Output:

```
Max
```

---

# User Input

Read input:

```bash
#!/bin/bash

echo "Enter your name"

read name

echo "Hello $name"
```

---

# Command-Line Arguments

Run:

```bash
./hello.sh Max
```

Inside the script:

```bash
echo $1
```

Output:

```
Max
```

Useful arguments:

| Variable | Meaning |
|----------|----------|
| $0 | Script name |
| $1 | First argument |
| $2 | Second argument |
| $# | Number of arguments |
| $@ | All arguments |

---

# Exit Status

Every Linux command returns an exit code.

```
0
```

means success.

Any non-zero value indicates an error.

Check it:

```bash
echo $?
```

Example:

```bash
mkdir test

echo $?
```

---

# Conditional Statements

Basic syntax:

```bash
if condition
then
    commands
fi
```

Example:

```bash
number=10

if [ $number -gt 5 ]
then
    echo "Greater"
fi
```

---

# Comparison Operators

## Numbers

| Operator | Meaning |
|----------|----------|
| -eq | Equal |
| -ne | Not Equal |
| -gt | Greater Than |
| -lt | Less Than |
| -ge | Greater or Equal |
| -le | Less or Equal |

---

## Strings

| Operator | Meaning |
|----------|----------|
| = | Equal |
| != | Not Equal |
| -z | Empty |
| -n | Not Empty |

---

# if-else

```bash
if [ $age -ge 18 ]
then
    echo "Adult"
else
    echo "Minor"
fi
```

---

# Nested if

```bash
if [ $marks -ge 90 ]
then
    echo "Excellent"
elif [ $marks -ge 75 ]
then
    echo "Good"
else
    echo "Needs Improvement"
fi
```

---

# Loops

Automation becomes powerful with loops.

---

## for Loop

```bash
for i in 1 2 3 4 5
do
    echo $i
done
```

Output:

```
1
2
3
4
5
```

---

## while Loop

```bash
count=1

while [ $count -le 5 ]
do
    echo $count
    count=$((count+1))
done
```

---

# Functions

Functions prevent duplicate code.

Example:

```bash
greet()
{
    echo "Welcome!"
}

greet
```

---

# Useful Commands Inside Scripts

Current directory:

```bash
pwd
```

Current user:

```bash
whoami
```

Current date:

```bash
date
```

Hostname:

```bash
hostname
```

Current directory contents:

```bash
ls
```

---

# Arithmetic

```bash
a=10
b=20

sum=$((a+b))

echo $sum
```

Output:

```
30
```

---

# File Testing

Does a file exist?

```bash
if [ -f report.txt ]
then
    echo "Exists"
fi
```

Directory exists?

```bash
if [ -d backup ]
then
    echo "Directory Exists"
fi
```

---

# Script Flow

```
Start
   │
   ▼
Read Variables
   │
   ▼
Read Input
   │
   ▼
Conditions
   │
   ▼
Loops
   │
   ▼
Functions
   │
   ▼
Execute Commands
   │
   ▼
Exit
```

---

# Debugging Scripts

Run normally:

```bash
./script.sh
```

Debug mode:

```bash
bash -x script.sh
```

Displays every executed command.

---

# Cloud (AWS) Connection

System administrators automate:

- EC2 updates.
- User creation.
- Service monitoring.
- Log cleanup.
- Backups.

Almost all automation starts with shell scripts.

---

# Docker Connection

Docker images often execute startup scripts.

Example:

```Dockerfile
ENTRYPOINT ["./start.sh"]
```

Scripts:

- Initialize databases.
- Configure environments.
- Launch applications.

---

# Kubernetes Connection

Init Containers frequently run Bash scripts before the main application starts.

Examples:

- Wait for databases.
- Download configuration.
- Prepare directories.

---

# DevSecOps Connection

Shell scripting automates:

- Security scans.
- Backups.
- Log collection.
- Patch management.
- Deployment verification.
- CI/CD pipelines.

Examples:

```
Run Trivy Scan

↓

Collect Logs

↓

Restart Service

↓

Notify Team
```

---

# Cybersecurity Insight

Attackers also use Bash.

Examples:

- Reconnaissance.
- Privilege escalation.
- Persistence.
- Data exfiltration.

Defenders use Bash to:

- Audit systems.
- Detect changes.
- Collect forensic evidence.
- Automate incident response.

---

# Best Practices

- Use meaningful variable names.
- Add comments.
- Check exit codes.
- Validate user input.
- Handle errors.
- Make scripts idempotent where possible.
- Test scripts in a safe environment before running them on production systems.

---

# Common Mistakes

❌ Forgetting the shebang.

❌ Not making the script executable.

❌ Missing quotation marks around variables that may contain spaces.

❌ Ignoring command failures.

❌ Hardcoding sensitive credentials.

---

# Troubleshooting

### Permission Denied

```
chmod +x script.sh
```

---

### Command Not Found

Verify:

- PATH
- Typographical errors
- Installed packages

---

### Variable Empty

Check:

```bash
echo $variable
```

Confirm that it was assigned and exported if needed.

---

### Script Stops Unexpectedly

Inspect:

```bash
echo $?
```

or run:

```bash
bash -x script.sh
```

---

# Interview Questions

## Beginner

1. What is Bash?
2. What is a shell script?
3. What is the purpose of `#!/bin/bash`?
4. How do you execute a Bash script?
5. What is `$1`?

---

## Intermediate

6. Difference between `$@` and `$#`.
7. Difference between `for` and `while`.
8. Why are functions useful?
9. What is an exit code?
10. What does `chmod +x` do?

---

## Advanced

11. Why is Bash widely used in DevOps?
12. How would you debug a failing shell script?
13. How are Bash scripts used in CI/CD?
14. Why should scripts avoid hardcoded credentials?

---

# Hands-on Lab

```bash
# Create a script
nano hello.sh

# Add a shebang and greeting
#!/bin/bash
echo "Hello Linux"

# Make executable
chmod +x hello.sh

# Run it
./hello.sh

# Create a script with user input
nano greet.sh

#!/bin/bash
echo "Enter your name:"
read name
echo "Welcome, $name!"

chmod +x greet.sh
./greet.sh
```

---

# Mini Project

Build a **System Information Script**.

Requirements:

- Display current user.
- Display hostname.
- Display current date.
- Display uptime.
- Display disk usage.
- Display memory usage.
- Display IP address.
- Save the output to a file called `system_report.txt`.

**Challenge:** Add error checking so the script exits with a meaningful message if any command fails.

---

# Quick Revision

✔ Bash = Bourne Again Shell.

✔ Script = File containing Linux commands.

✔ `#!/bin/bash` = Interpreter declaration.

✔ `chmod +x` = Make executable.

✔ `read` = Accept user input.

✔ `$1` = First argument.

✔ `if` = Conditional execution.

✔ `for` / `while` = Loops.

✔ Functions = Reusable blocks of code.

✔ `echo $?` = Previous command's exit status.

---

# Key Takeaways

- Bash scripting automates repetitive Linux tasks.
- Variables, conditions, loops, and functions are the core building blocks of shell scripts.
- Scripts are widely used in Linux administration, cloud environments, Docker, Kubernetes, and CI/CD pipelines.
- Writing reliable scripts requires proper error handling, validation, and testing.
- Bash is a foundational automation skill for DevOps and DevSecOps engineers.
