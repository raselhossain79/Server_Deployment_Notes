
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

| Phase | Topic |
|-------|-------|
| [Phase 0](../phase-0-baseline/) | VPS Instance Setup + Ubuntu + Apache + SSH |
| [Phase 1](../phase-1-ssh-hardening/) | SSH Hardening |
| [Phase 2](../phase-2-firewall-ufw/) | UFW + Provider Security Group |
| [Phase 3](../phase-3-server-basics/) | Server Basic Security |
| [Phase 4](../phase-4-fail2ban/) | Fail2Ban (Brute-force Protection) |
| [Phase 5](../phase-5-real-deployment/) | Real Domain Deployment |
| [Phase 6](../phase-6-https-ssl/) | HTTPS / SSL (Certbot) |
| [Phase 7](../phase-7-production-setup/) | Clean Production Setup |

---

> 💡 *Practice on Local VM first — then replicate here.*
