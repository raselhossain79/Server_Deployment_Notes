
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
