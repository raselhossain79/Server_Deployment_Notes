# 🔐 Phase 1: SSH Hardening

## Goal

- Enable SSH key-based authentication
- Disable password-based authentication
- Block root login
- Change default SSH port
- Configure firewall for secure access

---

## Prerequisites

- Ubuntu Server installed and running
- SSH access from client machine (e.g., Kali Linux)
- A non-root user with sudo privileges

---

## What is SSH Hardening?

By default, SSH allows password login and root access — both are major security risks. SSH hardening replaces weak authentication with key-based login, disables root access, and moves SSH off the well-known port 22 to reduce automated attacks.

---

## Step 1 — Generate SSH Key (Client Side)

Run this on your **client machine** (not the server):

```bash
ssh-keygen
OR
ssh-keygen -t ed25519
```

- Press Enter to use default location
- Optional: set a passphrase for extra security

Generated files:

| File | Description |
|------|-------------|
| `~/.ssh/id_ed25519` | Private key — never share this |
| `~/.ssh/id_ed25519.pub` | Public key — this goes to the server |

---

## Step 2 — Copy Public Key to Server

```bash
ssh-copy-id username@server_ip
```

This automatically appends your public key to `~/.ssh/authorized_keys` on the server.

---

## Step 3 — Test Key-Based Login

```bash
ssh username@server_ip
```

> ✅ Should log in without asking for a password.
> Do not proceed to the next step until this works.

---

## Step 4 — Disable Password Authentication

Edit the SSH config file:

```bash
sudo nano /etc/ssh/sshd_config
```

Add or update:

```
PasswordAuthentication no
KbdInteractiveAuthentication no
```

### ⚠️ Fix Override Issue

On cloud or fresh Ubuntu installs, a secondary config file may override your settings. Check for it:

```bash
ls /etc/ssh/sshd_config.d/
```

If a file exists (e.g., `50-cloud-init.conf`), edit it:

```bash
sudo nano /etc/ssh/sshd_config.d/50-cloud-init.conf
```

Set:

```
PasswordAuthentication no
```

### Verify the Change is Applied

```bash
sudo sshd -T | grep -i passwordauthentication
```

Expected output:

```
passwordauthentication no
```

---

## Step 5 — Disable Root Login

```bash
sudo nano /etc/ssh/sshd_config
```

Set:

```
PermitRootLogin no
```

Test after restarting SSH:

```bash
ssh root@server_ip -p 2222
```

Expected:

```
Permission denied (publickey)
```

---

## Step 6 — Change Default SSH Port

```bash
sudo nano /etc/ssh/sshd_config
```

Set:

```
Port 2222
```

Connect using new port:

```bash
ssh username@server_ip -p 2222
```

> ⚠️ Allow the new port in UFW **before** restarting SSH — or you will get locked out.

---

## Step 7 — Restart SSH Service

Apply all changes:

```bash
sudo systemctl restart ssh
```

---

## Step 8 — Configure Firewall (UFW)

```bash
# Allow new SSH port
sudo ufw allow 2222/tcp

# Allow web traffic
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Enable firewall
sudo ufw enable

# Verify
sudo ufw status verbose
```

> See [Phase 2: Firewall Setup](../phase-2-firewall-ufw/) for full UFW documentation.

---

## Security Summary

| Feature | Status |
|---------|--------|
| SSH Key Authentication | ✅ Enabled |
| Password Login | ❌ Disabled |
| Root Login | ❌ Disabled |
| Custom SSH Port (2222) | ✅ Configured |
| Firewall (UFW) | ✅ Enabled |

---

## Key Concepts

- **SSH Key Authentication** — cryptographically stronger than any password
- **Port change** — moves SSH off port 22, reducing automated brute-force attempts
- **Root login disabled** — even if someone gets in, they can't have full system control
- **Override config files** — cloud Ubuntu images often have extra config files that override `sshd_config` — always check

---

## ⚠️ Important Warnings

- Never disable password login **before** verifying key-based login works
- Always keep your **current SSH session open** while testing a new one
- Never share or lose your private key — keep a backup
- If locked out, you will need console/VNC access through your provider dashboard

---

