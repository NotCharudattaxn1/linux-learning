# Linux Lesson 8: Environment Variables

> **Module:** Linux Fundamentals
> **Difficulty:** Beginner → Intermediate
> **Prerequisites:** Terminal Basics, Linux File System, File Permissions, Users & Groups, Process Management, Package Management
> **Estimated Study Time:** 2–3 Hours

---

# Learning Objectives

After completing this lesson, you should be able to:

- Explain what environment variables are.
- Understand how Linux uses environment variables.
- View, create, modify, and remove environment variables.
- Differentiate between temporary and permanent variables.
- Understand the `PATH` variable.
- Configure shell startup files.
- Apply environment variables in DevOps, Docker, Kubernetes, AWS, and cybersecurity.

---

# Introduction

Imagine every program on your computer asks the operating system questions like:

- Who is the current user?
- What is my home directory?
- Which shell am I using?
- Where should I search for executable programs?
- Which language should I display?

Linux answers these questions using **environment variables**.

An **environment variable** is a **named value stored by the operating system that programs can use while running.**

---

# What is an Environment Variable?

An environment variable is a **key-value pair**.

Example:

```
USER=max
HOME=/home/max
SHELL=/bin/bash
PATH=/usr/local/bin:/usr/bin:/bin
```

Think of them as configuration settings available to every process.

---

# Real-World Analogy

Imagine you start a new job.

The company provides:

- Employee ID
- Department
- Office Location
- Email Address

Whenever you need information, you don't ask HR every time—you already have it.

Environment variables work the same way for programs.

---

# Why Environment Variables Matter

Applications use them to determine:

- Current user
- Home directory
- Language settings
- Temporary file locations
- Executable search paths
- API endpoints
- Secret keys (with care)
- Application configuration

Without them, many applications would require manual configuration every time they started.

---

# Viewing Environment Variables

Display all variables:

```bash
printenv
```

or

```bash
env
```

Both display the current environment.

---

# Viewing a Specific Variable

Syntax:

```bash
echo $VARIABLE_NAME
```

Examples:

```bash
echo $USER
echo $HOME
echo $PATH
echo $SHELL
```

---

# Important Environment Variables

## USER

Displays the current logged-in user.

```bash
echo $USER
```

Example:

```
max
```

---

## HOME

Displays the user's home directory.

```bash
echo $HOME
```

Example:

```
/home/max
```

---

## SHELL

Displays the default shell.

```bash
echo $SHELL
```

Example:

```
/bin/bash
```

---

## HOSTNAME

Displays the system hostname.

```bash
echo $HOSTNAME
```

---

## PWD

Displays the current working directory.

```bash
echo $PWD
```

---

## PATH

One of the most important Linux environment variables.

Example:

```bash
echo $PATH
```

Output:

```
/usr/local/bin:/usr/bin:/bin:/usr/sbin
```

---

# Understanding PATH

Suppose you type:

```bash
python3
```

How does Linux know where `python3` is located?

Linux checks each directory listed in the `PATH` variable **from left to right** until it finds the executable.

Example:

```
PATH

↓

/usr/local/bin

↓

/usr/bin

↓

/bin

↓

Executable Found

↓

Run Program
```

If the executable is not found in any directory listed in `PATH`, Linux reports:

```
command not found
```

---

# Why PATH is Important

Without `PATH`, you would need to type the **full path** to every executable.

Instead of:

```bash
python3
```

you would need:

```bash
/usr/bin/python3
```

`PATH` makes command execution convenient.

---

# Creating an Environment Variable

Syntax:

```bash
VARIABLE=value
```

Example:

```bash
COURSE=Linux
```

However, this variable is **local to the shell** and not inherited by child processes.

---

# Exporting an Environment Variable

To make it available to child processes:

```bash
export COURSE=Linux
```

Verify:

```bash
echo $COURSE
```

Output:

```
Linux
```

---

# Temporary Variables

Variables created in the terminal exist only for the current shell session.

Example:

```bash
export PROJECT=DevSecOps
```

Close the terminal.

Reopen it.

```
PROJECT
```

no longer exists.

---

# Permanent Variables

To keep variables after restarting the terminal, add them to your shell configuration.

For Bash:

```bash
nano ~/.bashrc
```

Example:

```bash
export PROJECT=DevSecOps
```

Save the file.

Reload:

```bash
source ~/.bashrc
```

Now the variable is available in future shell sessions.

---

# Removing Variables

Remove an environment variable:

```bash
unset PROJECT
```

Verify:

```bash
echo $PROJECT
```

No output means the variable has been removed from the current shell.

---

# How Environment Variables Work Internally

```
Login
   │
   ▼
Shell Starts
   │
   ▼
Reads .bashrc
   │
   ▼
Loads Environment Variables
   │
   ▼
Applications Inherit Variables
```

Every child process inherits the environment of its parent unless overridden.

---

# Shell Configuration Files

Common files:

| File | Purpose |
|------|---------|
| ~/.bashrc | Interactive Bash configuration |
| ~/.profile | Login shell configuration |
| /etc/environment | System-wide environment variables |
| /etc/profile | Global shell configuration |

---

# Useful Commands

Display environment:

```bash
env
```

Display variables:

```bash
printenv
```

Display one variable:

```bash
echo $PATH
```

Create variable:

```bash
export APP=Demo
```

Remove variable:

```bash
unset APP
```

Reload Bash configuration:

```bash
source ~/.bashrc
```

---

# Cloud (AWS) Connection

Applications on EC2 instances commonly use environment variables for configuration.

Examples:

```
DATABASE_HOST
DATABASE_PORT
APP_ENV
LOG_LEVEL
```

This avoids hardcoding configuration into application code.

---

# Docker Connection

Docker containers commonly receive configuration through environment variables.

Example:

```bash
docker run -e APP_ENV=production myapp
```

This allows the same image to run in different environments (development, testing, production) without modification.

---

# Kubernetes Connection

Kubernetes injects configuration into Pods using environment variables.

Examples:

- Database host
- API endpoint
- Feature flags
- Service URLs

Sensitive values are typically provided through **Secrets**, while non-sensitive configuration often comes from **ConfigMaps**.

---

# DevSecOps Connection

Environment variables are widely used in CI/CD pipelines.

Examples:

```
GITHUB_TOKEN
AWS_REGION
DOCKER_USERNAME
BUILD_NUMBER
```

**Important:** While environment variables are convenient, sensitive information should be handled carefully. Many CI/CD platforms provide secure secret storage that exposes values as environment variables only at runtime.

---

# Cybersecurity Insight

Never commit secrets directly into source code.

Instead of:

```python
password = "MySecretPassword"
```

Prefer:

```python
import os

password = os.getenv("DB_PASSWORD")
```

This reduces the risk of accidentally exposing credentials in version control.

---

# Best Practices

- Use meaningful variable names.
- Keep `PATH` organized.
- Store long-term variables in configuration files.
- Use environment variables for application configuration.
- Keep sensitive values out of source code.
- Review environment variables when troubleshooting configuration issues.

---

# Common Mistakes

❌ Accidentally overwriting the `PATH` variable.

❌ Forgetting to use `export`.

❌ Storing sensitive credentials in plain text files.

❌ Expecting temporary variables to persist after restarting the shell.

❌ Editing system-wide configuration files without understanding their impact.

---

# Troubleshooting

### Variable Not Available

Check:

```bash
echo $VARIABLE
```

If empty:

- Was it exported?
- Was the shell restarted?
- Was the configuration file reloaded?

---

### Command Not Found

Check:

```bash
echo $PATH
```

Ensure the executable's directory is included.

---

### Changes Not Taking Effect

Reload:

```bash
source ~/.bashrc
```

or start a new shell session.

---

# Interview Questions

## Beginner

1. What is an environment variable?
2. What is the purpose of the `PATH` variable?
3. How do you display an environment variable?
4. What does `export` do?
5. What is the difference between `env` and `printenv`?

---

## Intermediate

6. Difference between temporary and permanent environment variables.
7. How do child processes receive environment variables?
8. Why should applications avoid hardcoded configuration?
9. What does `source ~/.bashrc` do?
10. How can you remove an environment variable?

---

## Advanced

11. Why are environment variables widely used in Docker?
12. How does Kubernetes use environment variables?
13. Why is `PATH` a potential security concern if misconfigured?
14. How are environment variables commonly used in CI/CD pipelines?

---

# Hands-on Lab

```bash
# View all variables
printenv

# View specific variables
echo $USER
echo $HOME
echo $PATH
echo $SHELL

# Create a temporary variable
export COURSE=Linux

# Verify
echo $COURSE

# Remove it
unset COURSE

# Edit Bash configuration
nano ~/.bashrc

# Add:
export PROJECT=DevSecOps

# Reload
source ~/.bashrc

# Verify
echo $PROJECT
```

---

# Mini Project

Create a personalized shell environment.

Tasks:

1. Add a custom variable called `LEARNING_PATH`.
2. Reload your shell.
3. Verify the variable.
4. Create another variable called `FAVORITE_EDITOR`.
5. Document each step and explain why environment variables are useful.

---

# Quick Revision

✔ Environment Variable = Key-value pair used by processes.

✔ `env` / `printenv` = View environment.

✔ `echo $VARIABLE` = Display a variable.

✔ `export` = Make a variable available to child processes.

✔ `unset` = Remove a variable.

✔ `PATH` = Directories searched for executable commands.

✔ `.bashrc` = User-specific Bash configuration.

✔ `source ~/.bashrc` = Reload configuration without restarting the shell.

---

# Key Takeaways

- Environment variables allow Linux and applications to share configuration dynamically.
- `PATH` determines where Linux searches for executable programs.
- Temporary variables exist only for the current shell session.
- Permanent variables are stored in shell configuration files.
- Environment variables are widely used in cloud computing, containers, Kubernetes, and CI/CD pipelines.
- Proper management of environment variables improves flexibility, portability, and security.
