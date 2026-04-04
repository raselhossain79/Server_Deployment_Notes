# 🚀 Phase 0: Baseline Setup

## Goal

- Create a virtual server
- Install Ubuntu Server
- Enable and access SSH
- Install Apache web server
- Deploy a basic HTML website

---

## Environment

| Item | Details |
|------|---------|
| Platform | VMware Workstation |
| OS | Ubuntu Server (CLI-based) |
| Client | Kali Linux |

---

## Step 1 — Install Ubuntu Server

### Download ISO

- Get the Ubuntu Server ISO from [ubuntu.com](https://ubuntu.com/download/server)

### Create Virtual Machine in VMware

- Select the ISO
- Allocate resources:

| Resource | Recommended |
|----------|-------------|
| RAM | 2GB |
| Storage | 20GB |

### During Installation

- Select language and keyboard layout
- Set up a username and password
- ✅ Make sure to check **Install OpenSSH Server** during installation

---

## Step 2 — Update System

First thing after installation — always update:

```bash
sudo apt update && sudo apt upgrade -y
```

> Keeps the system patched and prevents known vulnerabilities from the start.

---

## Step 3 — Enable SSH & Connect

After installation, SSH should be running automatically. Verify on the server:

```bash
sudo systemctl status ssh
```

If not running, start it:

```bash
sudo systemctl enable ssh
sudo systemctl start ssh
```

Check server IP:

```bash
ip a
```

Now connect from your client machine (Kali Linux):

```bash
ssh username@server_ip
```

> ✅ Remote access established. All further steps are done through this SSH session.

---

## Step 4 — Install Apache

Update packages and install Apache:

```bash
sudo apt update
sudo apt install apache2 -y
```

Verify Apache is running:

```bash
sudo systemctl status apache2
```

---

## Step 5 — Deploy HTML Website

### Option A — Create directly on server

```bash
sudo nano /var/www/html/index.html
```

Example content:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Server</title>
</head>
<body>
    <h1>Hello from my Ubuntu Server</h1>
</body>
</html>
```

### Option B — Transfer file from client using SCP

Run this on your **client machine**:

```bash
scp file.html username@server_ip:/var/www/html/
```

### Fix File Permission (if needed)

If the website is not loading after upload, fix ownership:

```bash
sudo chown www-data:www-data /var/www/html/index.html
sudo chmod 644 /var/www/html/index.html
```

> Apache runs as `www-data` user — files must be readable by it.

---

## Step 6 — Test in Browser

Open in browser:

```
http://server_ip
```

> ✅ Website is live and accessible.

---

## Challenges Faced

| Problem | Solution |
|---------|---------|
| SSH not connecting | Verified service status and server IP |
| Apache not showing page | Checked service status with `systemctl` |
| Paste issue in CLI | Used keyboard shortcuts |

---

## Apache Useful Commands

```bash
# Start / Stop / Restart
sudo systemctl start apache2
sudo systemctl stop apache2
sudo systemctl restart apache2

# Check status
sudo systemctl status apache2

# Check config for errors
sudo apache2ctl configtest
```

---

## Apache Logs (Troubleshooting)

```bash
# Access log — who visited the site
sudo tail -f /var/log/apache2/access.log

# Error log — what went wrong
sudo tail -f /var/log/apache2/error.log
```

> If website is not loading, always check error log first.

---

## Key Concepts

- **Virtual Machine** — a software-based computer running inside your real machine
- **SSH** — secure remote access to a server via terminal
- **SCP** — secure file transfer over SSH
- **Apache** — web server software that serves HTML files over HTTP
- **`/var/www/html/`** — Apache's default web root directory

---

## Outcome

- ✅ Ubuntu Server installed and running
- ✅ SSH remote access working
- ✅ Apache web server installed
- ✅ HTML website deployed and accessible

---
