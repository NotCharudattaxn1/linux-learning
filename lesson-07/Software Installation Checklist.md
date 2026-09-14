# Software Installation Checklist – Git and Curl

## Objective

In this practical exercise, you will learn how to:

1. Update package lists.
2. Install Git.
3. Install Curl.
4. Verify both installations.
5. Understand the purpose of each command.

---

# Prerequisites

This guide assumes you are using a Debian-based Linux distribution such as:

* Ubuntu
* Linux Mint
* Debian

The commands use the `apt` package manager.

---

# 1. Update Package Lists

Before installing software, update the local package information.

## Command

```bash
sudo apt update
```

## Purpose

This command downloads the latest information about available packages from the configured software repositories.

### Breaking it down

* `sudo` → Runs the command with administrator privileges.
* `apt` → The package management tool.
* `update` → Updates the local package lists.

### Important

```bash
sudo apt update
```

**does not upgrade installed software.**

It only refreshes the information about:

* Available packages
* Available versions
* Package repositories

---

# 2. Install Git

Git is a distributed version control system used to track changes in files and source code.

## Command

```bash
sudo apt install git
```

You may be asked:

```text
Do you want to continue? [Y/n]
```

Press:

```text
Y
```

and then press **Enter**.

## Purpose

This command downloads and installs Git along with any required dependencies.

### Command breakdown

* `sudo` → Runs the installation with administrator privileges.
* `apt` → Package manager.
* `install` → Installs a package.
* `git` → The package to install.

---

# 3. Verify Git Installation

After installation, verify that Git is available.

## Command

```bash
git --version
```

Example output:

```text
git version 2.x.x
```

The exact version may differ.

## Purpose

This command confirms that:

* Git is installed.
* The `git` command is available.
* The installed Git version can be identified.

---

# 4. Install Curl

Curl is a command-line tool used to transfer data between systems.

It commonly supports protocols such as:

* HTTP
* HTTPS
* FTP

Curl is frequently used in:

* API testing
* Automation
* Shell scripts
* DevOps pipelines
* Downloading resources

## Command

```bash
sudo apt install curl
```

## Purpose

This command downloads and installs the Curl package.

### Command breakdown

* `sudo` → Administrator privileges.
* `apt` → Package manager.
* `install` → Installs software.
* `curl` → The package being installed.

---

# 5. Verify Curl Installation

Run:

```bash
curl --version
```

Example output:

```text
curl 8.x.x
```

The exact version will depend on your Linux distribution and repositories.

## Purpose

This command confirms that Curl is installed and displays information about the installed version.

---

# 6. Complete Installation Checklist

## Step 1 — Update Package Lists

```bash
sudo apt update
```

**Purpose:** Refreshes information about available packages.

* [ ] Completed

---

## Step 2 — Install Git

```bash
sudo apt install git
```

**Purpose:** Installs the Git version control system.

* [ ] Completed

---

## Step 3 — Verify Git

```bash
git --version
```

**Purpose:** Confirms that Git was installed successfully.

* [ ] Completed

---

## Step 4 — Install Curl

```bash
sudo apt install curl
```

**Purpose:** Installs the Curl command-line data transfer tool.

* [ ] Completed

---

## Step 5 — Verify Curl

```bash
curl --version
```

**Purpose:** Confirms that Curl was installed successfully.

* [ ] Completed

---

# 7. Verify Package Installation Using APT

You can also verify whether a package is installed using:

```bash
apt list --installed | grep git
```

For Curl:

```bash
apt list --installed | grep curl
```

## Purpose

These commands search the list of installed packages.

### Breaking it down

```text
apt list --installed
```

Lists installed packages.

The pipe:

```text
|
```

sends the output of one command to another command.

```text
grep git
```

searches for lines containing the word `git`.

Therefore:

```bash
apt list --installed | grep git
```

means:

> List installed packages and search for packages containing `git`.

---

# 8. Check the Location of Commands

You can find where Git and Curl are installed.

## Git

```bash
which git
```

Example:

```text
/usr/bin/git
```

## Curl

```bash
which curl
```

Example:

```text
/usr/bin/curl
```

## Purpose

The `which` command displays the location of an executable found in your system's `PATH`.

---

# 9. Summary of Commands

| Command                 | Purpose                        |
| ----------------------- | ------------------------------ |
| `sudo apt update`       | Updates package lists          |
| `sudo apt install git`  | Installs Git                   |
| `git --version`         | Displays Git version           |
| `sudo apt install curl` | Installs Curl                  |
| `curl --version`        | Displays Curl version          |
| `which git`             | Shows Git executable location  |
| `which curl`            | Shows Curl executable location |

---

# 10. Important Linux Package Management Concept

There is a difference between:

```bash
sudo apt update
```

and:

```bash
sudo apt upgrade
```

## `apt update`

```text
Updates package information.
```

It checks repositories for newer versions.

## `apt upgrade`

```text
Upgrades installed packages.
```

It installs newer versions of packages that are already installed.

### Typical workflow

```bash
sudo apt update
sudo apt upgrade
```

---

# 11. DevSecOps Perspective

Package management is important in DevOps and DevSecOps.

Installing software is not simply about running:

```bash
sudo apt install package-name
```

You should also understand:

* Where the package comes from.
* Which repository provides it.
* What dependencies are installed.
* Whether the package is up to date.
* Whether the package has known vulnerabilities.

For example:

```text
Repository
     │
     ▼
Package Manager (APT)
     │
     ▼
Package
     │
     ▼
Installation
     │
     ▼
Verification
```

In production environments, uncontrolled package installation can introduce:

* Security vulnerabilities
* Dependency problems
* Supply chain risks
* Configuration inconsistencies

---

# Key Takeaways

* `apt` is a package manager used on Debian-based Linux systems.
* `sudo apt update` refreshes package information.
* `sudo apt install <package>` installs software.
* `git --version` verifies Git.
* `curl --version` verifies Curl.
* `which <command>` shows the executable's location.
* Always verify that important software was installed correctly.

---

# Final Practical Exercise

Run the following commands:

```bash
# Update package lists
sudo apt update

# Install Git
sudo apt install git

# Verify Git
git --version

# Install Curl
sudo apt install curl

# Verify Curl
curl --version

# Find executable locations
which git
which curl
```

Record the versions installed on your system:

```text
Git Version: 2.53.0

Curl Version: 8.18.0

Git Location: /usr/bin/git

Curl Location: /usr/bin/curl
```

---

## Next Step

After completing this exercise, you should understand the basic workflow:

```text
Update Package Lists
        ↓
Install Package
        ↓
Verify Installation
        ↓
Check Executable
        ↓
Ready to Use
```

This workflow is fundamental for Linux system administration and will later be useful when working with **Docker, CI/CD pipelines, servers, cloud environments, and DevSecOps tooling**.
