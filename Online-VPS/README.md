
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

## Platform: Oracle Cloud Free Tier

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
- This is extra step compared to Local VM

### Step 4 — Connect via SSH
```bash
ssh -i ~/.ssh/id_ed25519 ubuntu@<public-ip>
```

### Step 5 — System Update
```bash
sudo apt update && sudo apt upgrade -y
```

### Step 6 — Install Apache
```bash
sudo apt install apache2 -y
```

### Step 7 — Deploy HTML + Test
- Upload HTML via SCP
- Test: `http://<public-ip>`

---

## 🔐 PHASE 1: SSH Hardening
> Same as Local VM — no differences

- Generate/reuse SSH key
- Disable password authentication
- Disable root login
- Change SSH port (e.g., 2222)
- ⚠️ Update Oracle Security Group for new SSH port
- Restart SSH + test

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
```

**Oracle Security Group (extra):**
- Remove old port 22 rule
- Add port 2222, 80, 443 in Ingress Rules
- Both layers must match — otherwise ports won't work

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
- Test + verify

---

## 🌍 PHASE 5: Real Domain Deployment
> Different from Local VM — real domain needed here

### Step 1 — Buy a Domain
- Namecheap, GoDaddy, or Cloudflare Registrar
- Cheap options: `.xyz`, `.online`, `.me`

### Step 2 — Point Domain to VPS IP
- Go to domain DNS settings
- Add A Record:
  - Name: `@` (root domain)
  - Value: VPS public IP
  - TTL: Auto
- Add A Record for www:
  - Name: `www`
  - Value: VPS public IP

### Step 3 — Configure Apache Virtual Host
```bash
sudo nano /etc/apache2/sites-available/yourdomain.conf
```

```apache
<VirtualHost *:80>
    ServerName yourdomain.com
    ServerAlias www.yourdomain.com
    DocumentRoot /var/www/html
</VirtualHost>
```

```bash
sudo a2ensite yourdomain.conf
sudo systemctl restart apache2
```

### Step 4 — Test
- Open browser: `http://yourdomain.com`
- Site should load ✅

---

## 🔒 PHASE 6: HTTPS / SSL (Certbot)
> Only possible on Online VPS — not on Local VM

### Step 1 — Install Certbot
```bash
sudo apt install certbot python3-certbot-apache -y
```

### Step 2 — Generate SSL Certificate
```bash
sudo certbot --apache -d yourdomain.com -d www.yourdomain.com
```

### Step 3 — Auto Renewal Setup
```bash
sudo certbot renew --dry-run
```

Certbot automatically adds a cron job for renewal every 90 days.

### Step 4 — Test
- Open browser: `https://yourdomain.com`
- 🔒 padlock should appear ✅

---

## ⚙️ PHASE 7: Clean Production Setup
> Final polish — professional level

### Step 1 — Proper Folder Permission
```bash
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

### Step 2 — Disable Apache Default Site
```bash
sudo a2dissite 000-default.conf
sudo systemctl restart apache2
```

### Step 3 — Security Headers (Apache)
```bash
sudo nano /etc/apache2/conf-available/security.conf
```

Add:
```
ServerTokens Prod
ServerSignature Off
```

### Step 4 — Backup System
```bash
# Backup web directory
rsync -av /var/www/html/ /backup/html/
```

### Step 5 — Final Check
```bash
# Check all services running
sudo systemctl status apache2
sudo systemctl status fail2ban
sudo ufw status verbose
sudo fail2ban-client status sshd
```

---

## ✅ Final Outcome

| Item | Status |
|------|--------|
| Ubuntu VPS live | ✅ |
| SSH hardened | ✅ |
| Firewall active (UFW + Oracle) | ✅ |
| Server hardened | ✅ |
| Fail2Ban active | ✅ |
| Real domain connected | ✅ |
| HTTPS / SSL active | ✅ |
| Production ready | ✅ |

---

## 🔑 Key Differences: Local VM vs Online VPS

| Step | Local VM | Online VPS |
|------|----------|------------|
| OS Install | VMware ISO | Oracle dashboard one-click |
| SSH Key | Generated on Kali | Same key, assigned during instance creation |
| Public Access | Cloudflare Tunnel | Direct via public IP |
| Firewall | UFW only | UFW + Oracle Security Group |
| SSL | Not possible | Certbot + Let's Encrypt |
| Domain | Not needed | Required for SSL |
