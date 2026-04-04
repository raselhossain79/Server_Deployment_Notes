# 🖥️ Linux Server Deployment Notes

> Personal documentation of hands-on Linux server setup, security hardening,
> and real-world web deployment — practiced on both a local VM and a live VPS.

---

## 👤 About This Repo

This is my personal field notes repo where I document every step of
setting up and securing a production-ready Linux web server.

Not copy-pasted theory — actual commands I ran, errors I hit,
and fixes I found.

---

## 🎯 Goal

- ✅ Set up a secure Ubuntu server from scratch
- ✅ Deploy a real website (live domain + HTTPS)
- ✅ Understand every step — not just follow blindly
- ✅ Build practical cybersecurity skills through real deployment
- ✅ Practice on local VM first, then replicate on a real online VPS

---

## 🌍 Environments

| Environment | Platform | Purpose |
|-------------|----------|---------|
| 🖥️ Local VM | VirtualBox / VMware (Ubuntu) | Practice & learning |
| ☁️ Online VPS | Cloud Provider (Ubuntu) | Real-world live deployment |

> Same roadmap, same phases — documented separately
> to capture environment-specific differences.

---

## 🗺️ Roadmap

### 🖥️ Local VM (`local-vm/`)

| Phase | Topic |
|-------|-------|
| 0 | Baseline Setup (Ubuntu + Apache + SSH) |
| 1 | SSH Hardening |
| 2 | UFW Firewall |
| 3 | Server Basic Security |
| 4 | Fail2Ban (Brute-force Protection) |
| 5 | Real Domain Deployment |
| 6 | HTTPS / SSL (Certbot) |
| 7 | Clean Production Setup |

### ☁️ Online VPS (`online-vps/`)

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

## 🛠️ Tools & Stack

![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04-E95420?style=flat&logo=ubuntu&logoColor=white)
![Apache](https://img.shields.io/badge/Apache-HTTP_Server-D22128?style=flat&logo=apache&logoColor=white)
![OpenSSH](https://img.shields.io/badge/OpenSSH-Hardened-4A90D9?style=flat)
![UFW](https://img.shields.io/badge/UFW-Firewall-333?style=flat)
![Let's Encrypt](https://img.shields.io/badge/Let's_Encrypt-SSL-003A70?style=flat)

---

## 📂 Structure

```
server-deployment-notes/
│
├── local-vm/                   ← Local Ubuntu VM (practice)
│   ├── phase-0-baseline/
│   ├── phase-1-ssh-hardening/
│   ├── phase-2-firewall-ufw/
│   ├── phase-3-server-basics/
│   ├── phase-4-fail2ban/
│   ├── phase-5-real-deployment/
│   ├── phase-6-https-ssl/
│   └── phase-7-production-setup/
│
├── online-vps/                 ← Online Cloud VPS (real deployment)
│   ├── phase-0-baseline/
│   ├── phase-1-ssh-hardening/
│   ├── phase-2-firewall-ufw/
│   ├── phase-3-server-basics/
│   ├── phase-4-fail2ban/
│   ├── phase-5-real-deployment/
│   ├── phase-6-https-ssl/
│   └── phase-7-production-setup/
│
└── cheatsheets/
    ├── linux-commands.md
    ├── ufw-commands.md
    └── ssh-commands.md
```

---

## 🔗 Connect

- GitHub: [github.com/raselhossain79](https://github.com/raselhossain79)

---

> 💡 *"Real skill comes from doing, not just reading."*
