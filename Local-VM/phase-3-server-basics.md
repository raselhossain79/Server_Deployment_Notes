# 🧠 Phase 3: Server Basic Security

## Goal

- Keep the system patched and up to date
- Minimize attack surface by disabling unused services
- Enforce strong access control
- Monitor system activity through logs

---

## What is Server Basic Security?

After securing SSH and setting up a firewall, the next step is hardening the server itself. This phase focuses on reducing the number of ways an attacker could get in — by removing unnecessary services, enforcing strong credentials, and keeping the system updated.

---

## Step 1 — System Update & Upgrade

Always keep the system updated to patch known vulnerabilities:

```bash
sudo apt update && sudo apt upgrade -y
```

Set up automatic security updates:

```bash
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

---

## Step 2 — Disable Unused Services

List all running services:

```bash
sudo systemctl list-units --type=service --state=running
```

Disable any service you don't need:

```bash
sudo systemctl stop <service-name>
sudo systemctl disable <service-name>
```

Common services to disable if not needed:

| Service | Description |
|---------|-------------|
| `bluetooth` | Bluetooth support |
| `cups` | Printing service |
| `avahi-daemon` | Network discovery |
| `snapd` | Snap package manager |

Verify a service is disabled:

```bash
sudo systemctl status <service-name>
```

---

## Step 3 — Strong User Password

Set or update password for a user:

```bash
sudo passwd username
```

Use a strong password:
- Minimum 12 characters
- Mix of uppercase, lowercase, numbers, symbols

Enforce password policy:

```bash
sudo apt install libpam-pwquality -y
sudo nano /etc/security/pwquality.conf
```

Recommended settings:

```
minlen = 12
dcredit = -1
ucredit = -1
lcredit = -1
ocredit = -1
```

---

## Step 4 — Separate Non-Root User

Never work as root directly. Create a dedicated user with sudo access:

```bash
# Create new user
sudo adduser username

# Add to sudo group
sudo usermod -aG sudo username

# Verify
groups username
```

---

## Step 5 — Disable Root Login

Prevent direct root SSH login (also covered in Phase 1):

```bash
sudo nano /etc/ssh/sshd_config
```

Set:

```
PermitRootLogin no
```

Restart SSH:

```bash
sudo systemctl restart ssh
```

Also lock the root account directly:

```bash
sudo passwd -l root
```

> This makes root completely inaccessible even locally.

---

## Step 6 — Log Monitoring

### Auth Log — track login attempts

```bash
sudo tail -f /var/log/auth.log
```

Check for failed login attempts:

```bash
sudo grep "Failed password" /var/log/auth.log
```

Check for successful logins:

```bash
sudo grep "Accepted" /var/log/auth.log
```

### Syslog — general system activity

```bash
sudo tail -f /var/log/syslog
```

### Last logins

```bash
last
lastb    # failed attempts
```

---

## Step 7 — Sudo Access Control

Review who has sudo access:

```bash
getent group sudo
```

Remove sudo from a user if not needed:

```bash
sudo deluser username sudo
```

Check sudoers file (do not edit directly):

```bash
sudo visudo
```

---

## Security Summary

| Feature | Status |
|---------|--------|
| System Updated | ✅ Done |
| Unused Services Disabled | ✅ Done |
| Strong Password Enforced | ✅ Done |
| Non-Root User Created | ✅ Done |
| Root Login Disabled | ✅ Done |
| Log Monitoring Active | ✅ Done |
| Auto Security Updates | ✅ Done |

---

## ⚪ Optional (Recommended)

### Regular Backups

```bash
sudo apt install rsync -y

# Backup web directory
rsync -av /var/www/html/ /backup/html/
```

### Resource Monitoring

```bash
# Install htop
sudo apt install htop -y
htop

# Disk usage
df -h

# Memory usage
free -h
```

### User Account Review

Periodically review all user accounts:

```bash
# List all users
cat /etc/passwd

# List users with login access
grep -v nologin /etc/passwd
```

Disable unused accounts:

```bash
sudo usermod --expiredate 1 username
```

---

## Key Concepts

- **Attack surface** — the total number of ways an attacker can get in; smaller is better
- **Principle of least privilege** — users and services should only have the access they actually need
- **Log monitoring** — `auth.log` is the first place to check after any suspicious activity
- **Root lock** — even on a local server, locking root adds an extra layer of protection

---

## ⚠️ Important Notes

- Always keep at least one sudo user active before locking root
- Test sudo access before locking root account
- Check logs regularly — do not wait for something to go wrong

---
