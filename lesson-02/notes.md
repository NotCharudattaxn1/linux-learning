# Linux Fundamentals – Lesson 2
## Navigating the Linux File System

**Module:** Linux Fundamentals

**Prerequisites:**
- Lesson 1 completed
- Ubuntu (WSL) installed
- Comfortable using the terminal

---

# Learning Objectives

By the end of this lesson, you will be able to:

- Navigate the Linux file system
- Understand absolute and relative paths
- Change directories
- Create and remove directories
- Copy and move files
- Rename files
- Delete files safely

---

# 1. Understanding the Linux File System

Linux has a single root directory.

```
/
```

Everything starts from `/`.

Think of it as the trunk of a tree.

```
/
├── home
│   └── max
│       ├── projects
│       ├── Downloads
│       └── Documents
├── etc
├── var
├── usr
└── tmp
```

Unlike Windows, Linux doesn't use `C:\` or `D:\`.

---

# 2. Where Am I?

Command:

```bash
pwd
```

Example:

```text
/home/max
```

`pwd` stands for **Print Working Directory**.

Always know where you are before performing file operations.

---

# 3. Changing Directories

Command:

```bash
cd
```

Move into a folder:

```bash
cd projects
```

Move back one directory:

```bash
cd ..
```

Go directly to your home directory:

```bash
cd ~
```

Go to the root directory:

```bash
cd /
```

Return to the previous directory:

```bash
cd -
```

---

# 4. Absolute vs Relative Paths

## Absolute Path

Starts from the root (`/`).

Example:

```text
/home/max/projects/linux-learning
```

Works regardless of your current location.

---

## Relative Path

Starts from your current directory.

If you're already inside:

```text
/home/max
```

Then:

```bash
cd projects
```

is enough.

---

# 5. Creating Directories

Create one directory:

```bash
mkdir linux-learning
```

Create multiple directories:

```bash
mkdir notes scripts projects
```

Create nested directories:

```bash
mkdir -p projects/linux/day2
```

The `-p` option creates parent directories if they don't already exist.

---

# 6. Removing Directories

Remove an empty directory:

```bash
rmdir notes
```

If the directory contains files:

```bash
rm -r notes
```

Be careful with recursive deletion.

---

# 7. Copying Files

Copy one file:

```bash
cp notes.txt backup.txt
```

Copy a directory:

```bash
cp -r lesson-01 lesson-01-backup
```

The `-r` option means **recursive**, allowing directories and their contents to be copied.

---

# 8. Moving and Renaming

Move a file:

```bash
mv notes.txt projects/
```

Rename a file:

```bash
mv old.txt new.txt
```

Linux uses the same `mv` command for both moving and renaming.

---

# 9. Removing Files

Delete a file:

```bash
rm hello.txt
```

Delete multiple files:

```bash
rm file1.txt file2.txt
```

Delete a directory and all its contents:

```bash
rm -r directory_name
```

⚠️ Double-check before pressing Enter.

---

# Real-World Example

Suppose you have:

```
projects/
│
├── password-generator
└── linux-learning
```

You want to rename:

```
password-generator
```

to

```
python-password-generator
```

Use:

```bash
mv password-generator python-password-generator
```

No separate rename command is needed.

---

# Hands-on Lab

Complete these commands one by one.

```bash
cd ~

mkdir linux-practice

cd linux-practice

mkdir lesson2

cd lesson2

touch notes.txt

ls

pwd

cp notes.txt backup.txt

ls

mv backup.txt copy.txt

ls

mkdir test

rmdir test

cd ..

ls
```

Understand the output after every command.

---

# Mini Challenge

Without looking at the notes:

1. Create a directory called `practice`.
2. Inside it create another directory called `linux`.
3. Inside `linux`, create a file called `commands.txt`.
4. Rename it to `linux_commands.txt`.
5. Copy it as `backup.txt`.
6. Delete `backup.txt`.
7. Return to your home directory.

---

# Common Mistakes

❌ Forgetting where you are before deleting files.

Always check:

```bash
pwd
```

---

❌ Using:

```bash
rm -r
```

without checking the target directory.

---

❌ Confusing absolute and relative paths.

Remember:

Absolute → Starts with `/`

Relative → Starts from your current directory.

---

# Interview Questions

1. What is the difference between an absolute path and a relative path?

2. What does `pwd` do?

3. What is the difference between `cp` and `mv`?

4. Why is `rm -r` considered dangerous?

5. What does the `-p` option in `mkdir -p` do?

---

# Key Takeaways

- Linux has one root directory (`/`).
- `pwd` shows your current location.
- `cd` changes directories.
- `mkdir` creates directories.
- `cp` copies files.
- `mv` moves or renames files.
- `rm` deletes files.
- `rmdir` removes empty directories.
- Always verify your location before deleting anything.
