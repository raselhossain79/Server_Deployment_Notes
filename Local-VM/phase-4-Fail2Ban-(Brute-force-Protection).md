# 🛡️ Phase 4: Fail2Ban Setup

## Goal

- Protect server from brute-force attacks
- Automatically ban malicious IPs
- Monitor and manage active bans

---

## What is Fail2Ban?

Fail2Ban is a lightweight intrusion prevention tool. It monitors server log files for suspicious patterns (like repeated failed logins), then automatically creates firewall rules to block the attacker's IP.

```
Log file → Pattern match → Trigger → Block IP via firewall
```

It works alongside UFW — Fail2Ban does not replace the firewall, it uses it.

---

## Prerequisites

- Ubuntu Server running
- SSH hardened (Phase 1 complete)
- UFW firewall enabled (Phase 2 complete)

> **Note:** UFW is not required for Fail2Ban to work. Fail2Ban uses `iptables` by default, which is built into the Linux kernel and always available. UFW is just a frontend for iptables.

| Situation | Fail2Ban works? |
|-----------|----------------|
| UFW installed | ✅ Yes |
| No UFW, iptables only | ✅ Yes |
| No firewall installed at all | ✅ Yes (iptables is always present) |

---

## Installation

```bash
sudo apt update
sudo apt install fail2ban -y
```

Enable and start the service:

```bash
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

Verify it is running:

```bash
sudo systemctl status fail2ban
```

---

## Configuration

### Why jail.local?

Fail2Ban has two config files:

| File | Purpose |
|------|---------|
| `/etc/fail2ban/jail.conf` | Default config — do not edit |
| `/etc/fail2ban/jail.local` | Custom config — edit this |

`jail.conf` gets overwritten on updates. `jail.local` overrides it safely.

Create your custom config:

```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

### Configure SSH Protection

```bash
sudo nano /etc/fail2ban/jail.local
```

Find the `[sshd]` section and update:

```ini
[sshd]
enabled  = true
port     = 2222
maxretry = 3
findtime = 600
bantime  = 600
```

### Configuration Options Explained

| Option | Value | Meaning |
|--------|-------|---------|
| `enabled` | true | Activate SSH jail |
| `port` | 2222 | Your custom SSH port |
| `maxretry` | 3 | Max failed attempts before ban |
| `findtime` | 600 | Time window in seconds (10 min) |
| `bantime` | 600 | Ban duration in seconds (10 min) |

> For production servers, consider `bantime = 3600` (1 hour) or higher.

---

## How Banning Works

```
3 failed attempts within 10 minutes → IP gets banned for 10 minutes
```

### ⚠️ Common Misunderstanding

```
❌ maxretry = 3  →  3 total failed attempts → ban
✅ maxretry = 3  →  1 initial attempt + 3 retries = 4 total failures → ban
```

> `maxretry` means **maximum retries allowed after the first attempt** — not total attempts.

### ⚠️ Log Entry Behavior (Important)

Fail2Ban counts **log entries**, not actual login attempts.

One login attempt can generate multiple log entries:

```
Failed password for user from x.x.x.x
Failed password for user from x.x.x.x
```

This means banning may appear slightly earlier or later than expected — this is normal behavior.

---

## Apply Changes

Restart Fail2Ban after any config change:

```bash
sudo systemctl restart fail2ban
```

---

## Monitoring & Management

Check all active jails:

```bash
sudo fail2ban-client status
```

Check SSH jail details:

```bash
sudo fail2ban-client status sshd
```

Watch auth log in real time:

```bash
sudo tail -f /var/log/auth.log
```

---

## Testing

Simulate a brute-force attack from your client:

```bash
ssh user@server_ip -p 2222
# Enter wrong password 3+ times
```

Then check if the IP is banned:

```bash
sudo fail2ban-client status sshd
```

Unban an IP manually (e.g., if you locked yourself out):

```bash
sudo fail2ban-client set sshd unbanip YOUR_IP
```

---

## Real-World Use Cases

Fail2Ban can protect more than just SSH:

| Service | What it blocks |
|---------|---------------|
| SSH | Repeated login failures |
| Apache / Nginx | Scanner traffic, 404 floods |
| Admin panels | Login brute-force |
| FTP | Repeated auth failures |
| Mail server | Email login brute-force |

---

## Security Summary

| Feature | Status |
|---------|--------|
| Fail2Ban Installed | ✅ Done |
| SSH Jail Configured | ✅ Done |
| Custom Port Set | ✅ Done |
| Log Monitoring Active | ✅ Done |

---

## Key Concepts

- **Jail** — a set of rules for one specific service (SSH, Apache etc.)
- **Ban** — a temporary firewall block on an IP address
- **Filter** — the regex pattern Fail2Ban uses to detect failures in logs
- **findtime** — the sliding time window; old failures outside this window don't count
- **bantime = -1** — permanent ban (use carefully)

---

## ⚠️ Important Notes

- Always whitelist your own IP to avoid locking yourself out
- If locked out, use provider console/VNC to unban
- Fail2Ban is a reactive tool — it bans after detecting, not before

Whitelist your IP:

```bash
sudo nano /etc/fail2ban/jail.local
```

Add under `[DEFAULT]`:

```ini
ignoreip = 127.0.0.1/8 YOUR_IP
```

---
