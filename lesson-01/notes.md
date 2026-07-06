# Linux Learning – Lesson 1

## Setting Up Linux & Understanding the Terminal

**Module:** Linux Fundamentals

**Goal:** Become comfortable with the Linux terminal and understand your working environment.

---

# Learning Objectives

By the end of this lesson, you should be able to:

* Understand what WSL is.
* Open the Ubuntu terminal.
* Know your current user.
* Know your current directory.
* List files and directories.
* Understand hidden files.
* Create files and directories.
* Understand the Linux terminal prompt.

---

# 1. What is WSL?

**WSL (Windows Subsystem for Linux)** allows you to run a real Linux environment directly on Windows without installing a separate operating system.

Advantages:

* No dual boot required.
* Works alongside Windows.
* Used by many professional developers.
* Perfect for Docker, Git, Python, and DevOps.

---

# 2. Updating Ubuntu

After installing Ubuntu, update your system.

```bash
sudo apt update
sudo apt upgrade -y
```

These commands refresh the package list and install the latest available updates.

---

# 3. Understanding the Terminal Prompt

Example:

```text
max@DESKTOP:~$
```

Meaning:

* **max** → Current user
* **DESKTOP** → Computer name (hostname)
* **~** → Home directory
* **$** → Normal user

If you see:

```text
#
```

you are operating as the **root** user.

---

# 4. Important Commands

## whoami

```bash
whoami
```

Purpose:

Displays the current logged-in user.

Example output:

```text
max
```

---

## pwd

```bash
pwd
```

Purpose:

Displays your current working directory.

Example:

```text
/home/max
```

---

## ls

```bash
ls
```

Purpose:

Lists all visible files and folders in the current directory.

---

## ls -l

```bash
ls -l
```

Purpose:

Displays files in a detailed format including:

* Permissions
* Owner
* Group
* File size
* Date
* File name

---

## ls -la

```bash
ls -la
```

Purpose:

Shows all files, including hidden files.

Hidden files begin with a dot (`.`).

Examples:

```text
.bashrc
.profile
```

---

## touch

```bash
touch hello.txt
```

Purpose:

Creates an empty file.

---

## mkdir

```bash
mkdir projects
```

Purpose:

Creates a new directory.

---

## cd

```bash
cd projects
```

Purpose:

Changes your current directory.

---

# 5. Important Linux Concepts

## Home Directory

Every user has a personal workspace.

Example:

```text
/home/max
```

This is where your personal files are stored.

---

## Hidden Files

Files beginning with a dot (`.`) are hidden by default.

Examples:

```text
.bashrc
.profile
```

These files usually store configuration settings.

---

## Root User

The root user has full administrative privileges.

Use administrative access only when necessary.

Example:

```bash
sudo -i
```

Exit the root shell using:

```bash
exit
```

---

# 6. Command Summary

| Command   | Purpose                               |
| --------- | ------------------------------------- |
| `whoami`  | Displays the current user             |
| `pwd`     | Shows the current directory           |
| `ls`      | Lists visible files                   |
| `ls -l`   | Lists files with detailed information |
| `ls -la`  | Lists all files including hidden ones |
| `touch`   | Creates an empty file                 |
| `mkdir`   | Creates a directory                   |
| `cd`      | Changes directory                     |
| `sudo -i` | Opens a root shell                    |
| `exit`    | Leaves the current shell              |

---

# Practical Exercise

Run the following commands in order:

```bash
whoami
pwd
ls
ls -la
mkdir projects
touch hello.txt
ls
cd projects
pwd
```

Observe how the output changes after each command.

---

# Mini Challenge

Complete the following tasks without looking at the notes.

1. Create a folder named `linux-practice`.
2. Move into the folder.
3. Create a file named `notes.txt`.
4. Display your current directory.
5. List all files, including hidden files.

---

# Key Takeaways

* Linux uses a terminal to execute commands.
* Every user has a home directory.
* Hidden files start with a dot (`.`).
* `pwd` tells you where you are.
* `ls` shows files.
* `touch` creates files.
* `mkdir` creates directories.
* `cd` changes directories.
* Use `sudo` only when administrative privileges are required.

---

# Interview Questions

1. What is WSL?
2. What does `pwd` do?
3. What is the difference between `ls` and `ls -la`?
4. What is the purpose of `touch`?
5. Why should you use `sudo` instead of working as the root user all the time?
