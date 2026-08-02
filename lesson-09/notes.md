# Linux Lesson 9: Networking Commands

> **Module:** Linux Fundamentals
> **Difficulty:** Beginner → Intermediate
> **Prerequisites:** Linux Lessons 1–8
> **Estimated Study Time:** 3–4 Hours

---

# Learning Objectives

After completing this lesson, you should be able to:

- Understand how Linux communicates over a network.
- Inspect network interfaces and IP addresses.
- Test connectivity between systems.
- Diagnose DNS and routing issues.
- Transfer data from the command line.
- Understand networking tools used by Linux administrators, DevOps engineers, and cybersecurity professionals.

---

# Introduction

Every Linux server connected to the internet depends on networking.

Examples:

- Web Servers
- Cloud Servers
- Docker Containers
- Kubernetes Pods
- SSH Connections
- APIs
- Databases

Without networking, these systems cannot communicate.

Linux provides powerful command-line tools to inspect, troubleshoot, and monitor networks.

---

# Network Interface

A **network interface** is the connection between your computer and a network.

Examples:

```
eth0
ens33
wlan0
lo
```

Common interfaces:

| Interface | Purpose |
|------------|----------|
| lo | Loopback Interface |
| eth0 | Ethernet |
| wlan0 | Wireless Network |
| ens33 | Predictable Ethernet Interface |

---

# Loopback Interface

The loopback interface represents your own computer.

Address:

```
127.0.0.1
```

Hostname:

```
localhost
```

Applications use this address to communicate with services running on the same machine.

---

# Viewing IP Addresses

Modern command:

```bash
ip addr
```

or

```bash
ip a
```

Example:

```
2: ens33
inet 192.168.1.50/24
```

This displays:

- IPv4 Address
- IPv6 Address
- Interface Status
- MAC Address

---

# Viewing Routing Information

Command:

```bash
ip route
```

Example:

```
default via 192.168.1.1 dev ens33
```

The **default gateway** is the router that forwards traffic outside your local network.

---

# Checking Connectivity

## ping

The most commonly used networking command.

Syntax:

```bash
ping google.com
```

or

```bash
ping 8.8.8.8
```

Example:

```
64 bytes from google.com
```

Purpose:

- Test network connectivity.
- Measure latency.
- Verify whether a host is reachable.

Stop with:

```
Ctrl + C
```

---

# Testing DNS

Example:

```bash
ping google.com
```

If:

```
ping 8.8.8.8
```

works,

but

```
ping google.com
```

doesn't,

the problem is likely DNS.

---

# hostname

Displays the computer's hostname.

```bash
hostname
```

View detailed information:

```bash
hostnamectl
```

Shows:

- Hostname
- Operating System
- Kernel Version
- Architecture

---

# curl

Transfers data from servers.

Example:

```bash
curl https://example.com
```

Displays the webpage source.

Check only headers:

```bash
curl -I https://example.com
```

Common Uses:

- Testing APIs
- Downloading data
- Checking HTTP responses

---

# wget

Downloads files.

Example:

```bash
wget https://example.com/file.zip
```

Unlike curl, wget is designed primarily for downloading.

---

# ss

Modern replacement for `netstat`.

Display active connections:

```bash
ss
```

Display listening ports:

```bash
ss -tuln
```

Meaning:

```
t = TCP
u = UDP
l = Listening
n = Numeric Addresses
```

Useful for checking which services are accepting connections.

---

# traceroute

Shows the path packets take across the network.

Install:

```bash
sudo apt install traceroute
```

Run:

```bash
traceroute google.com
```

Useful when diagnosing routing problems.

---

# nslookup

Queries DNS servers.

Example:

```bash
nslookup google.com
```

Displays:

- IP Address
- DNS Server
- Domain Information

---

# dig

Advanced DNS troubleshooting tool.

Install:

```bash
sudo apt install dnsutils
```

Example:

```bash
dig google.com
```

Often used by network engineers and administrators.

---

# Common Networking Workflow

```
No Internet
      │
      ▼
Check IP Address
(ip addr)
      │
      ▼
Ping Gateway
      │
      ▼
Ping 8.8.8.8
      │
      ▼
Ping google.com
      │
      ▼
Check DNS
(nslookup/dig)
      │
      ▼
Check Routing
(ip route)
```

---

# Important Commands

| Command | Purpose |
|----------|----------|
| ip addr | Display IP addresses |
| ip route | Display routing table |
| ping | Test connectivity |
| hostname | Display hostname |
| hostnamectl | Detailed system information |
| curl | Transfer data from servers |
| wget | Download files |
| ss | Display network connections |
| traceroute | Trace packet path |
| nslookup | DNS lookup |
| dig | Advanced DNS queries |

---

# Cloud (AWS) Connection

Every EC2 instance relies on networking.

Administrators commonly use:

```bash
ip addr
ping
curl
ss
```

to verify:

- IP configuration
- Internet access
- Open ports
- Running services

---

# Docker Connection

Docker creates virtual networks.

Useful commands:

```bash
ss
ip addr
ping
```

help diagnose:

- Container connectivity
- Port mapping
- Network isolation

---

# Kubernetes Connection

Pods communicate over a virtual network.

Commands like:

```
curl
ping (when available)
ss
```

are useful for verifying service communication and debugging networking issues.

---

# DevSecOps Connection

Networking commands are essential for:

- Health checks
- Monitoring
- CI/CD pipeline testing
- Service verification
- Security validation

Example:

A deployment pipeline may use `curl` to confirm that an application is responding after deployment.

---

# Cybersecurity Insight

Attackers often:

- Scan for open ports.
- Probe services.
- Enumerate networks.

Defenders use many of the same networking tools to verify system health and investigate suspicious activity.

Understanding normal network behavior makes abnormal behavior easier to detect.

---

# Best Practices

- Verify connectivity before assuming an application is broken.
- Check DNS when hostnames fail to resolve.
- Use `ss` to confirm expected services are listening.
- Prefer modern commands like `ip` and `ss` over older alternatives when possible.

---

# Common Mistakes

❌ Assuming the internet is down without testing the gateway.

❌ Confusing DNS issues with connectivity issues.

❌ Ignoring routing configuration.

❌ Forgetting that local firewalls can block traffic.

❌ Assuming an application is running just because the process exists.

---

# Troubleshooting

### No Internet

Check:

```bash
ip addr
```

---

### Can't Reach Websites

Test:

```bash
ping 8.8.8.8
```

If successful:

```
ping google.com
```

If only the IP works, investigate DNS.

---

### Website Not Responding

Check:

```bash
ss -tuln
```

Confirm that the service is listening on the expected port.

---

### Download Problems

Test:

```bash
curl -I https://example.com
```

Verify the HTTP response and server availability.

---

# Interview Questions

## Beginner

1. What is a network interface?
2. What is the loopback address?
3. What does `ping` do?
4. What is the purpose of `curl`?
5. What is `wget` used for?

---

## Intermediate

6. Difference between `curl` and `wget`.
7. Difference between `ping google.com` and `ping 8.8.8.8`.
8. What does `ss -tuln` display?
9. What is a default gateway?
10. Why is DNS important?

---

## Advanced

11. Why has `ss` largely replaced `netstat`?
12. How would you troubleshoot a server that cannot access the internet?
13. How can `curl` be used in a CI/CD pipeline?
14. Why is networking knowledge essential for Kubernetes?

---

# Hands-on Lab

```bash
# View network interfaces
ip addr

# View routing table
ip route

# Check hostname
hostname

hostnamectl

# Test connectivity
ping google.com

ping 8.8.8.8

# Test website
curl https://example.com

# View headers
curl -I https://example.com

# Download a file
wget https://example.com

# Display listening ports
ss -tuln

# DNS lookup
nslookup google.com

# Advanced DNS lookup
dig google.com

# Trace network path
traceroute google.com
```

---

# Mini Project

Perform a complete network health check.

Tasks:

1. Display your IP address.
2. Identify the default gateway.
3. Verify internet connectivity.
4. Test DNS resolution.
5. Download a small file.
6. List listening ports.
7. Document each command, its output, and what you learned.

---

# Quick Revision

✔ `ip addr` → Show IP addresses.

✔ `ip route` → Show routing table.

✔ `ping` → Test connectivity.

✔ `hostname` → Show system hostname.

✔ `curl` → Transfer data.

✔ `wget` → Download files.

✔ `ss` → Show sockets and listening ports.

✔ `traceroute` → Trace packet path.

✔ `nslookup` / `dig` → Query DNS.

---

# Key Takeaways

- Linux provides powerful networking tools for diagnosing connectivity and service issues.
- Understanding IP addresses, routing, DNS, and listening ports is essential for server administration.
- Commands like `ping`, `curl`, `ss`, and `ip` are used daily by Linux administrators, DevOps engineers, and cloud professionals.
- Networking knowledge is fundamental for AWS, Docker, Kubernetes, CI/CD, and cybersecurity.
