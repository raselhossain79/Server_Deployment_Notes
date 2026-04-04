🔐 SSH Hardening Guide (Ubuntu Server)

📌 Overview

This guide documents the complete process of securing SSH access on an Ubuntu Server. The goal is to eliminate weak authentication methods and reduce attack surface for real-world deployment.

---

🎯 Objectives

- Disable password-based authentication
- Enable SSH key-based authentication
- Block root login
- Change default SSH port
- Configure firewall for secure access

---

🧱 Prerequisites

- Ubuntu Server installed
- SSH access from a client (e.g., Kali Linux)
- A non-root user with sudo privileges

---

🟢 Step 1: Generate SSH Key (Client Side)

ssh-keygen

- Press Enter to use default location
- Optional: Set passphrase for extra security

📁 Generated files:

- "~/.ssh/id_ed25519" → Private key (keep secret)
- "~/.ssh/id_ed25519.pub" → Public key (to share)

---

🟢 Step 2: Copy Public Key to Server

ssh-copy-id username@server_ip

Example:

ssh-copy-id rashed@192.168.1.105

✔ This automatically adds your public key to:

~/.ssh/authorized_keys

---

🟢 Step 3: Test Key-Based Login

ssh username@server_ip

✔ Should login without password

---

🟢 Step 4: Disable Password Authentication

Edit SSH config:

sudo nano /etc/ssh/sshd_config

Add at the end of file:

PasswordAuthentication no
KbdInteractiveAuthentication no

---

⚠️ Fix Override Issue (Important)

Check for additional config files:

ls /etc/ssh/sshd_config.d/

Edit file (e.g.):

sudo nano /etc/ssh/sshd_config.d/50-cloud-init.conf

Change:

PasswordAuthentication no

---

🔍 Verify Effective Config

sudo sshd -T | grep -i passwordauthentication

✔ Expected:

passwordauthentication no

---

🟢 Step 5: Restart SSH Service

sudo systemctl restart ssh

---

🟢 Step 6: Disable Root Login

Edit config:

sudo nano /etc/ssh/sshd_config

Add:

PermitRootLogin no

---

🧪 Test Root Access

ssh root@server_ip -p PORT

✔ Expected:

Permission denied

---

🟢 Step 7: Change Default SSH Port

Edit config:

sudo nano /etc/ssh/sshd_config

Change:

Port 2222

---

🔁 Restart SSH

sudo systemctl restart ssh

---

🔌 Connect Using New Port

ssh username@server_ip -p 2222

---

🟢 Step 8: Configure Firewall (UFW)

Install UFW

sudo apt install ufw -y

Allow Required Ports

sudo ufw allow 2222   # SSH
sudo ufw allow 80     # HTTP
sudo ufw allow 443    # HTTPS

Enable Firewall

sudo ufw enable

Check Status

sudo ufw status

---

🧠 Security Summary

Feature| Status
SSH Key Authentication| ✅ Enabled
Password Login| ❌ Disabled
Root Login| ❌ Disabled
Custom SSH Port| ✅ Configured
Firewall| ✅ Enabled

---

🔐 Key Concepts

- SSH Key Authentication is more secure than passwords
- Port change reduces automated attacks
- Firewall controls network access
- Root login disabled prevents full system compromise

---

⚠️ Important Notes

- Never disable password login before setting up SSH keys
- Always test new SSH session before closing current one
- Keep a backup of your private key
- Do not share your private key

---

🚀 Next Steps

- Install Fail2Ban (Brute-force protection)
- Setup HTTPS (SSL certificate)
- Deploy real website with domain

---

🧾 Conclusion

This setup ensures a secure SSH environment suitable for real-world deployments and forms the foundation for further server security and DevOps practices.

---
