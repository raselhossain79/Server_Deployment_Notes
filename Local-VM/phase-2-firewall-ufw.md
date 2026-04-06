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

**What this does:**
- `deny incoming` — bans all outside connections by default. No port is accessible unless you explicitly allow it.
- `allow outgoing` — server can freely make outbound connections (apt update, curl etc.)

> This is called the **Whitelist approach** — instead of blocking bad things one by one, you block everything and only open what you need.

### Step 2 — Allow Required Ports

```bash
sudo ufw allow 2222/tcp   # SSH (custom port)
sudo ufw allow 80/tcp     # HTTP
sudo ufw allow 443/tcp    # HTTPS (for future SSL)
```

**Why these three:**
- `2222` — without this, you cannot SSH into your own server after enabling the firewall
- `80` — without this, no one can visit your website over HTTP
- `443` — without this, HTTPS will not work after SSL setup

All other ports — 22, 23, 25, 3306 etc. — are automatically blocked because of `default deny incoming`. No need to deny them one by one.

### Step 3 — Enable Firewall

```bash
sudo ufw enable
```

> ⚠️ Always allow SSH port **before** running this — otherwise you will be locked out of your own server with no way back in (except provider console).

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

---

## Nmap Scan Behavior

When you scan this server with Nmap, ports appear in three states:

| Result | Meaning | Example |
|--------|---------|---------|
| Open | Port allowed + service is running | 2222, 80 |
| Filtered | Port blocked by firewall | 22, 23 |
| Closed | Port allowed but no service listening | 443 (before SSL) |

> Filtered means UFW is actively blocking that port — attacker gets no response at all.

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

On a cloud VPS, UFW alone is not enough. Cloud providers have their own **network-level firewall** (called Security Group or Firewall Rules) that sits in front of your server — before traffic even reaches UFW.

So even if UFW allows port 80, the provider's firewall can still block it.

```
Internet → Provider Firewall → UFW → Server
```

Both layers must allow the port for it to work.

| Provider | Where to open ports |
|----------|-------------------|
| Oracle Cloud | VCN → Security List → Ingress Rules |
| DigitalOcean | Networking → Firewalls |
| AWS | EC2 → Security Groups |
