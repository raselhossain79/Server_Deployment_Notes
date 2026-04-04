# 🛡️ Phase 2: Firewall Setup (UFW)

## Goal

- Control which ports are open or closed
- Allow only necessary services
- Block everything else automatically

---

## What is UFW?

UFW (Uncomplicated Firewall) is a frontend for iptables — the default firewall tool on Ubuntu. It simplifies firewall management with easy-to-read commands.

---

## Installation

UFW usually comes pre-installed on Ubuntu. Check first:

```bash
sudo ufw status
```

If not installed:

```bash
sudo apt update
sudo apt install ufw -y
```

---

## Configuration

### Step 1 — Set Default Policies

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

> Block all incoming by default. Allow all outgoing. This is the safest starting point.

### Step 2 — Allow Required Ports

```bash
sudo ufw allow 2222/tcp   # SSH (custom port)
sudo ufw allow 80/tcp     # HTTP
sudo ufw allow 443/tcp    # HTTPS (for future SSL)
```

### Step 3 — Enable Firewall

```bash
sudo ufw enable
```

> ⚠️ Make sure SSH port is allowed before enabling — or you will get locked out.

### Step 4 — Verify Status

```bash
sudo ufw status verbose
```

---

## Allowed Ports

| Port | Service | Note |
|------|---------|------|
| 2222/tcp | SSH | Custom port (default 22 is blocked) |
| 80/tcp | HTTP | Web server |
| 443/tcp | HTTPS | SSL — not active yet |

All other ports (22, 23, 25, 3306 etc.) are automatically blocked.

---

## Nmap Scan Behavior

| Result | Meaning |
|--------|---------|
| Open | Port allowed + service is running |
| Filtered | Port blocked by firewall |
| Closed | Port allowed but no service listening |

---

## Useful Commands

```bash
# View rules with line numbers
sudo ufw status numbered

# Delete a rule by number
sudo ufw delete <number>

# Disable firewall
sudo ufw disable

# Reset all rules
sudo ufw reset
```

---

## Best Practices

- Always test SSH login after changing rules to avoid lockout
- Never open ports you don't actively need
- Keep firewall enabled at all times

---

## ☁️ Online VPS Extra Step

UFW alone is not enough on cloud servers. Ports must also be opened from the provider's dashboard.

| Provider | Where to open ports |
|----------|-------------------|
| Oracle Cloud | VCN → Security List → Ingress Rules |
| DigitalOcean | Networking → Firewalls |
| AWS | EC2 → Security Groups |
