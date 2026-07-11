# Linux Lesson 3: Linux File System

## Objective

Understand the Linux file system hierarchy, navigate directories confidently, and distinguish between absolute and relative paths.

---

# What is a File System?

A file system is the method Linux uses to organize and manage files and directories.

Linux follows a hierarchical (tree-like) structure where everything starts from the **root directory (`/`)**.

```
/
├── home
├── etc
├── usr
├── var
├── bin
├── tmp
├── dev
└── root
```

---

# Root Directory (`/`)

The root directory is the highest level in the Linux file system.

All files and directories originate from this location.

Example:

```
/home/max/Documents
```

---

# Difference Between `/` and `/root`

| `/` | `/root` |
|------|----------|
| Root directory of the filesystem | Home directory of the root (administrator) user |
| Accessible as the starting point for the filesystem | Primarily used by the root user |

---

# Important Directories

## `/home`

Stores personal files of normal users.

Example:

```
/home/max
```

---

## `/etc`

Contains system configuration files.

Examples:
- Network configuration
- User account configuration
- DNS settings

---

## `/usr`

Contains installed applications, libraries, and documentation.

---

## `/var`

Stores variable data.

Examples:
- Log files
- Cache
- Mail
- Databases

---

## `/tmp`

Stores temporary files.

Many Linux systems automatically clean this directory.

---

## `/bin`

Contains essential command-line programs such as:

- ls
- cp
- mv
- cat

---

## `/dev`

Represents hardware devices as files.

Examples:
- Hard drives
- USB devices
- Terminal devices

---

# Absolute Path

An absolute path always begins with `/`.

Example:

```
/home/max/Documents/report.txt
```

It always points to the same location regardless of your current working directory.

---

# Relative Path

A relative path starts from the current working directory.

Example:

Current directory:

```
/home/max
```

Relative path:

```
Documents/report.txt
```

---

# Special Directory Symbols

## Current Directory

```
.
```

Example:

```bash
cp file.txt .
```

Copies the file into the current directory.

---

## Parent Directory

```
..
```

Example:

```bash
cd ..
```

Moves one directory up.

---

## Home Directory

```
~
```

Example:

```bash
cd ~
```

Returns to the current user's home directory.

---

# Commands Used

```bash
pwd
ls
cd
mkdir
touch
cp
mv
rm
rmdir
```

---

# Best Practices

- Prefer absolute paths when writing scripts.
- Use relative paths when working inside a project.
- Verify your current directory with `pwd` before making changes.
- Avoid running destructive commands (`rm`) from the wrong directory.

---

# Common Mistakes

- Confusing `/` with `/root`.
- Forgetting the current working directory.
- Using `rm` without confirming the location.
- Assuming `/usr` stores user documents.

---

# Real-world Usage

- System administrators edit configuration files in `/etc`.
- Developers store projects in their home directory.
- Logs are commonly inspected in `/var/log`.
- Scripts often use relative paths for portability.

---

# Interview Questions

1. What is the Linux root directory?
2. Explain the difference between `/` and `/root`.
3. What is an absolute path?
4. What is a relative path?
5. What does `~` represent?
6. What is the purpose of `/etc`?
7. Why are logs stored in `/var`?
8. Why should you check your current directory before using `rm`?

---

# Hands-on Lab

1. Navigate to your home directory.
2. Create a directory named `practice`.
3. Create subdirectories:
   - linux
   - python
4. Create:
   - linux/commands.txt
   - python/test.py
5. Return to the home directory using `cd ~`.
6. Verify your location using `pwd`.

---

# Summary

- Linux uses a hierarchical filesystem.
- Everything begins at the root directory (`/`).
- `/root` is the administrator's home directory.
- Absolute paths always begin with `/`.
- Relative paths depend on the current working directory.
- `.` represents the current directory.
- `..` represents the parent directory.
- `~` represents the current user's home directory.
