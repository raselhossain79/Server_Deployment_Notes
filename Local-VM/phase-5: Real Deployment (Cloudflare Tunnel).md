# 🌍 Phase 5: Real Deployment (Cloudflare Tunnel)

## Goal

- Make local Apache server publicly accessible over the internet
- No port forwarding required
- No public IP required
- HTTPS automatically included

---

## What is Cloudflare Tunnel?

Normally, to make a local server public you need a public IP and port forwarding. Most ISPs use **CG-NAT** which means you don't get a real public IP — making port forwarding impossible.

Cloudflare Tunnel solves this by creating an outbound connection from your server to Cloudflare's network. Cloudflare then acts as a middle layer between the internet and your local server.

```
User → Cloudflare Edge → Tunnel → Your Local Server (Apache)
```

Your server's real IP is never exposed. The connection is initiated from your server outward — no incoming ports need to be opened.

---

## Prerequisites

- Ubuntu Server running
- Apache installed and working locally
- SSH hardening done (Phase 1)
- UFW firewall configured (Phase 2)
- Fail2Ban active (Phase 4)

---

## Step 1 — Verify Apache is Running

```bash
sudo systemctl status apache2
```

Test locally:

```bash
curl http://localhost
```

> Apache must be running before starting the tunnel — the tunnel just forwards traffic to it.

---

## Step 2 — Install Cloudflared

Download and install the official Cloudflare tunnel client:

```bash
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
sudo apt --fix-broken install -y
```

Verify installation:

```bash
cloudflared --version
```

---

## Step 3 — Start Quick Tunnel

```bash
cloudflared tunnel --url http://localhost:80
```

After a few seconds, a temporary public URL will be generated:

```
https://random-name.trycloudflare.com
```

Open this URL in any browser from anywhere in the world — your local Apache site will load.

---

## How it Works Internally

```
cloudflared starts
       ↓
Creates outbound connection to Cloudflare network
       ↓
Cloudflare assigns a temporary public URL
       ↓
User visits URL → Cloudflare → Tunnel → Apache (localhost:80)
```

- No inbound ports opened
- Your server IP stays hidden
- HTTPS is provided automatically by Cloudflare

---

## Quick Tunnel Limitations

| Feature | Quick Tunnel |
|---------|-------------|
| URL | Random, temporary |
| Persistence | Stops when terminal closes |
| HTTPS | ✅ Automatic |
| Custom domain | ❌ Not available |
| Cost | Free |

> Quick Tunnel is for testing and learning. For a permanent setup with a custom domain, a named tunnel with a Cloudflare account is needed — that comes with Online VPS setup.

---

## Key Concepts

**Reverse Proxy:**
Cloudflare sits between the user and your server. The user never connects to your server directly — all traffic goes through Cloudflare first.

**Secure Tunneling:**
The connection is outbound-only from your server. No firewall rules need to be changed. Your server's real IP is never exposed to the public.

**CG-NAT:**
Carrier-Grade NAT — when an ISP shares one public IP among many users. Makes traditional port forwarding impossible, which is why Cloudflare Tunnel is needed.

---

## What Was Achieved

- ✅ Local Apache server exposed to the internet
- ✅ Live website accessible from anywhere
- ✅ No port forwarding or public IP needed
- ✅ HTTPS automatically active
- ✅ Server IP remained hidden

---

## ⚠️ Important Notes

- Quick Tunnel URL changes every time you restart it
- Keep the terminal open — closing it stops the tunnel
- UFW does not need changes — tunnel uses outbound connection only
- This is for learning and testing — for production use a named tunnel with a registered domain

---

## Next Steps

- [Phase 6: HTTPS / SSL (Certbot)](../phase-6-https-ssl/)
- [Online VPS Setup](../../online-vps/) — for permanent deployment with custom domain
