
# ☁️ Online VPS — Real World Deployment

> Production environment running Ubuntu Server on a cloud VPS.
> All phases follow the same structure as Local VM with a few extra steps.

---

## 🌐 Environment Details

| Item | Details |
|------|---------|
| Platform | Cloud Provider (Oracle / DigitalOcean / Linode etc.) |
| OS | Ubuntu Server 22.04 |
| Purpose | Real-world live deployment |
| Network | Public IP (internet accessible) |

---

## ⚠️ VPS-Specific Differences

| Phase | Extra Step |
|-------|-----------|
| Phase 0 | Create instance from provider dashboard, assign SSH key |
| Phase 2 | Open ports from provider Security Group in addition to UFW |
| Phase 5 | Point domain DNS record to VPS public IP |

---

## 📂 Phases

| Phase | Topic | Extra (VPS Only) |
|-------|-------|------------------|
| 0 | VPS Setup + Ubuntu + Apache + SSH | Create instance from provider dashboard, assign SSH key |
| 1 | SSH Hardening | — |
| 2 | UFW + Provider Firewall | Open ports from provider Security Group in addition to UFW |
| 3 | Server Basic Security | — |
| 4 | Fail2Ban (Brute-force Protection) | — |
| 5 | Real Domain Deployment | Point domain DNS record to VPS public IP |
| 6 | HTTPS / SSL (Certbot) | — |
| 7 | Clean Production Setup | — |

---

> 💡 *Practice on Local VM first — then replicate here.*










---

# ☁️ Online VPS — Full Roadmap

## Platform: Oracle Cloud Free Tier | Web Server: Nginx

---

## 🟢 PHASE 0: Oracle Cloud Setup + Baseline

### Step 1 — Create Oracle Cloud Account
- Go to cloud.oracle.com
- Sign up for Free Tier (Always Free)
- Requires: email, phone number, credit card (not charged)

### Step 2 — Create Ubuntu Instance
- Go to Compute → Instances → Create Instance
- Choose: Ubuntu 22.04
- Shape: VM.Standard.E2.1.Micro (Always Free)
- Add your SSH public key (same key from Kali)
- Create instance

### Step 3 — Configure Security Group (VCN)
- Go to VCN → Security List → Ingress Rules
- Open ports: 22 (temporary), 80, 443
- This is an extra step compared to Local VM

### Step 4 — Connect via SSH
```bash
ssh -i ~/.ssh/id_ed25519 ubuntu@<public-ip>
```

### Step 5 — System Update
```bash
sudo apt update && sudo apt upgrade -y
```

### Step 6 — Install Nginx
```bash
sudo apt install nginx -y
```

Verify Nginx:
```bash
sudo systemctl status nginx
```

Test locally:
```bash
curl http://localhost
```

> ✅ Should return Nginx default welcome page.

### Step 7 — Deploy HTML + Test
```bash
sudo nano /var/www/html/index.html
```

Test in browser: `http://<public-ip>`

---

## 🔐 PHASE 1: SSH Hardening
> Same as Local VM — no differences

- Reuse same SSH key from Kali
- Disable password authentication
- Disable root login
- Change SSH port (e.g., 2222)
- ⚠️ Update Oracle Security Group — add port 2222, remove port 22
- Restart SSH + test new port

```bash
ssh -i ~/.ssh/id_ed25519 ubuntu@<public-ip> -p 2222
```

---

## 🛡️ PHASE 2: Firewall (UFW + Oracle Security Group)
> Extra step compared to Local VM

**UFW (same as before):**
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2222/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
sudo ufw status verbose
```

**Oracle Security Group (extra — must do):**
- Remove port 22 rule
- Add port 2222, 80, 443 in Ingress Rules

> ⚠️ Both UFW and Oracle Security Group must allow the port — if either blocks it, the port will not work.

```
Internet → Oracle Security Group → UFW → Server
```

---

## 🧠 PHASE 3: Server Hardening
> Same as Local VM — no differences

- System update + auto updates
- Disable unused services
- Strong password + password policy
- Non-root user
- Lock root account
- Log monitoring
- Sudo access control

---

## 🛡️ PHASE 4: Fail2Ban
> Same as Local VM — no differences

- Install + enable
- Configure SSH jail (port 2222)
- Test + verify ban behavior

---

## 🌍 PHASE 5: Real Domain Deployment
> Different from Local VM — real domain needed

### Step 1 — Buy a Domain
- Namecheap, GoDaddy, or Cloudflare Registrar
- Cheap options: `.xyz`, `.online`, `.me` (1-2 USD/year)

### Step 2 — Point Domain to VPS IP
In domain DNS settings, add:

| Type | Name | Value |
|------|------|-------|
| A | @ | VPS public IP |
| A | www | VPS public IP |

DNS propagation takes 5-30 minutes.

### Step 3 — Configure Nginx Server Block
> Nginx uses "server blocks" — equivalent to Apache's virtual hosts

```bash
sudo nano /etc/nginx/sites-available/yourdomain.conf
```

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Enable the site:
```bash
sudo ln -s /etc/nginx/sites-available/yourdomain.conf /etc/nginx/sites-enabled/
sudo nginx -t        # test config for errors
sudo systemctl restart nginx
```

### Step 4 — Test
- Open browser: `http://yourdomain.com` ✅

---

## 🔒 PHASE 6: HTTPS / SSL (Certbot + Nginx)
> Only possible on Online VPS — requires real domain

### Step 1 — Install Certbot
```bash
sudo apt install certbot python3-certbot-nginx -y
```

> Note: For Nginx use `python3-certbot-nginx` — not `python3-certbot-apache`

### Step 2 — Generate SSL Certificate
```bash
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

Certbot will:
- Verify domain ownership
- Generate SSL certificate
- Automatically update Nginx config for HTTPS
- Redirect HTTP → HTTPS automatically

### Step 3 — Auto Renewal
```bash
sudo certbot renew --dry-run
```

Certbot adds a cron job automatically — certificate renews every 90 days.

### Step 4 — Test
- Open browser: `https://yourdomain.com`
- 🔒 padlock should appear ✅

---

## ⚙️ PHASE 7: Clean Production Setup

### Step 1 — Proper Folder Permission
```bash
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

### Step 2 — Remove Nginx Default Site
```bash
sudo rm /etc/nginx/sites-enabled/default
sudo systemctl restart nginx
```

### Step 3 — Nginx Security Headers
```bash
sudo nano /etc/nginx/nginx.conf
```

Add inside `http {}` block:
```nginx
server_tokens off;           # hide Nginx version
add_header X-Frame-Options "SAMEORIGIN";
add_header X-Content-Type-Options "nosniff";
add_header X-XSS-Protection "1; mode=block";
```

Test and restart:
```bash
sudo nginx -t
sudo systemctl restart nginx
```

### Step 4 — Backup System
```bash
rsync -av /var/www/html/ /backup/html/
```

### Step 5 — Final Verification
```bash
sudo systemctl status nginx
sudo systemctl status fail2ban
sudo ufw status verbose
sudo fail2ban-client status sshd
sudo certbot certificates
```

---

## ✅ Final Outcome

| Item | Status |
|------|--------|
| Ubuntu VPS live | ✅ |
| Nginx web server | ✅ |
| SSH hardened | ✅ |
| UFW + Oracle Security Group | ✅ |
| Server hardened | ✅ |
| Fail2Ban active | ✅ |
| Real domain connected | ✅ |
| HTTPS / SSL active | ✅ |
| Security headers set | ✅ |
| Production ready | ✅ |

---

## 🔑 Apache vs Nginx — Key Differences You Will Notice

| Item | Apache (Local VM) | Nginx (Online VPS) |
|------|------------------|-------------------|
| Config folder | `/etc/apache2/` | `/etc/nginx/` |
| Site config | `sites-available/` + `a2ensite` | `sites-available/` + symlink |
| Virtual host | `<VirtualHost>` block | `server {}` block |
| Config test | `apache2ctl configtest` | `nginx -t` |
| Certbot plugin | `python3-certbot-apache` | `python3-certbot-nginx` |
| Default web root | `/var/www/html/` | `/var/www/html/` |
| Restart | `systemctl restart apache2` | `systemctl restart nginx` |
