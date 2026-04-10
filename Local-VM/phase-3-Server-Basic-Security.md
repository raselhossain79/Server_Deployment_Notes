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

- `apt update` — fetches the latest package list from repositories
- `apt upgrade` — installs the latest versions of all installed packages
- Running both together ensures the system has the latest security patches

### Automatic Security Updates

Manually updating every day is not practical. `unattended-upgrades` automatically downloads and installs security patches in the background without any manual action.

```bash
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

The second command opens an interactive prompt — select **Yes** to enable automatic updates. After this, critical security patches will be applied automatically even if you forget to update manually.

---

## Step 2 — Disable Unused Services

Every running service is a potential entry point for an attacker. If a service has a vulnerability and it is running on your server — even if you never use it — an attacker can exploit it. Disabling unused services reduces this risk.

List all running services:

```bash
sudo systemctl list-units --type=service --state=running
```

Disable any service you don't need:

```bash
sudo systemctl stop <service-name>     # stop immediately
sudo systemctl disable <service-name>  # prevent from starting on boot
```

> Both commands are needed — `stop` kills it now, `disable` keeps it off after reboot.

Common services to disable on a server:

| Service | Description | Why disable |
|---------|-------------|-------------|
| `bluetooth` | Bluetooth support | Server has no Bluetooth hardware |
| `cups` | Printing service | Server does not need to print |
| `avahi-daemon` | Network auto-discovery (mDNS) | Exposes server info on local network, not needed |
| `snapd` | Snap package manager | Not needed if you don't use snap packages |

Verify a service is disabled:

```bash
sudo systemctl status <service-name>
```

Expected output should show `inactive (dead)`.

---

## Step 3 — Strong User Password

Set or update password for a user:

```bash
sudo passwd username
```

Use a strong password:
- Minimum 12 characters
- Mix of uppercase, lowercase, numbers, symbols

### Enforce Password Policy

Without a policy, users can set weak passwords like `1234` or `password`. `libpam-pwquality` enforces minimum password strength at the system level — weak passwords will be rejected automatically.

```bash
sudo apt install libpam-pwquality -y
sudo nano /etc/security/pwquality.conf
```

Recommended settings:

```
minlen = 12    # minimum 12 characters
dcredit = -1   # at least 1 digit required
ucredit = -1   # at least 1 uppercase letter required
lcredit = -1   # at least 1 lowercase letter required
ocredit = -1   # at least 1 special character required
```

> The `-1` value means "at least 1 of this type is required." Setting `dcredit = -2` would require at least 2 digits.

---

## Step 4 — Separate Non-Root User

Root has unlimited power — it can delete any file, change any config, and destroy the entire system with one command. Working directly as root means one mistake or one compromised session = full system compromise.

A separate user with sudo access gives you the same power when needed, but limits accidental or malicious damage.

```bash
# Create new user
sudo adduser username

# Add to sudo group
sudo usermod -aG sudo username

# Verify
groups username
```

- `adduser` — creates user with home directory and prompts for password
- `usermod -aG sudo` — adds user to sudo group (`-a` = append, `-G` = group)
- `groups` — confirms the user is in the sudo group

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

### Lock Root Account Completely

Disabling SSH root login still leaves root accessible locally (from the server's console). Locking the root account makes it completely inaccessible from everywhere.

```bash
sudo passwd -l root
```

- `-l` flag = lock. This disables the root password entirely.
- Even if someone gets physical access to the server, they cannot log in as root.
- Your sudo user still works normally — `sudo` does not need the root password.

> To unlock if ever needed: `sudo passwd -u root`

---

## Step 6 — Log Monitoring

Logs are the server's activity records. Every login attempt, command, error, and system event gets written to a log file. Monitoring logs helps detect attacks, unauthorized access, and suspicious behavior.

### Auth Log — track login attempts

`/var/log/auth.log` records all authentication events — SSH logins, sudo usage, password changes.

```bash
# Watch in real time
sudo tail -f /var/log/auth.log

# Find failed login attempts
sudo grep "Failed password" /var/log/auth.log

# Find successful logins
sudo grep "Accepted" /var/log/auth.log
```

### Syslog — general system activity

`/var/log/syslog` records general system events — service starts/stops, kernel messages, application logs.

```bash
sudo tail -f /var/log/syslog
```

> **Difference:** `auth.log` = who tried to log in. `syslog` = what the system did.

### Last logins

```bash
last       # list of successful logins with IP and time
lastb      # list of failed login attempts
```

> If you see unknown IPs in `last` — someone else logged in. If `lastb` shows hundreds of attempts — your server is being brute-forced.

---

## Step 7 — Sudo Access Control

### Why control sudo access?

If too many users have sudo, any one of them being compromised gives an attacker full system control. Sudo access should be given only to users who actually need it.

Review who has sudo access:

```bash
getent group sudo
```

Remove sudo from a user if not needed:

```bash
sudo deluser username sudo
```

### The sudoers file

The sudoers file controls exactly who can run what with sudo. It is the master access control list for sudo.

```bash
sudo visudo
```

> Always use `visudo` — never edit `/etc/sudoers` directly with nano or vim. `visudo` validates the syntax before saving. A syntax error in sudoers can lock everyone out of sudo permanently.

---

## Security Summary

| Feature | Status |
|---------|--------|
| System Updated | ✅ Done |
| Auto Security Updates | ✅ Done |
| Unused Services Disabled | ✅ Done |
| Strong Password Enforced | ✅ Done |
| Non-Root User Created | ✅ Done |
| Root Login Disabled | ✅ Done |
| Root Account Locked | ✅ Done |
| Log Monitoring Active | ✅ Done |

---

## ⚪ Optional (Recommended)

### Regular Backups

`rsync` copies files from one location to another — only transferring files that have changed, making it fast and efficient.

```bash
sudo apt install rsync -y

# Backup web directory
rsync -av /var/www/html/ /backup/html/
```

- `-a` = archive mode — preserves permissions, timestamps, symlinks
- `-v` = verbose — shows what is being copied

For automated backups, add this to cron:

```bash
crontab -e
```

Add:

```
0 2 * * * rsync -av /var/www/html/ /backup/html/
```

This runs the backup every day at 2 AM automatically.

### Resource Monitoring

Monitor server performance to detect unusual activity — high CPU or memory usage can indicate a running attack or cryptominer.

```bash
# Interactive process monitor
sudo apt install htop -y
htop

# Disk usage — check if disk is filling up
df -h

# Memory usage
free -h
```

### User Account Review

Over time, old or unused accounts accumulate. These are security risks — an attacker can use them to get in.

```bash
# List all users
cat /etc/passwd

# List only users with login access
# nologin = system accounts that cannot log in (www-data, daemon etc.)
# -v nologin = exclude those, show only real user accounts
grep -v nologin /etc/passwd
```

Disable unused accounts (without deleting):

```bash
sudo usermod --expiredate 1 username
```

> Setting expiredate to `1` (Jan 1, 1970) effectively disables the account immediately.

---

## Key Concepts

- **Attack surface** — the total number of ways an attacker can get in; smaller is better
- **Principle of least privilege** — users and services should only have the access they actually need
- **Log monitoring** — `auth.log` is the first place to check after any suspicious activity
- **Root lock** — even on a local server, locking root adds an extra layer of protection
- **visudo** — the only safe way to edit sudoers; validates syntax before saving

---

## ⚠️ Important Notes

- Always keep at least one sudo user active before locking root
- Test sudo access before locking root account
- Check logs regularly — do not wait for something to go wrong
- Never give sudo to a user who does not need it

---
