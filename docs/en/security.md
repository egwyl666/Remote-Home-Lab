# 🔒 Security — UFW + Fail2ban

Two tools that cover most attack vectors on your Pi.

---

## Overview

| Tool | What it does |
|---|---|
| **UFW** | Firewall. Blocks all incoming connections except those explicitly allowed |
| **Fail2ban** | Bans IPs after multiple failed SSH login attempts |

---

## UFW — Firewall

### Install

```bash
sudo apt install ufw -y
```

### Configure

```bash
# Block all incoming connections by default
sudo ufw default deny incoming

# Allow all outgoing (updates, Tailscale, etc.)
sudo ufw default allow outgoing

# Allow SSH
sudo ufw allow ssh

# Allow VNC (if you use it)
sudo ufw allow 5901/tcp

# Enable the firewall
sudo ufw enable
```

> ⚠️ Always open SSH (`allow ssh`) **before** enabling the firewall — otherwise you'll lock yourself out.

### Check status

```bash
sudo ufw status verbose
```

Output will look like:
```
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
5901/tcp                   ALLOW IN    Anywhere
```

### Why not 80/443?

Ports 80 and 443 are for when your Pi is **serving** a website to the outside world. If Pi is not a web server, there's no reason to open them. Outgoing connections (apt, tailscale, curl) are already covered by the `allow outgoing` rule.

---

## Fail2ban

### Install

```bash
sudo apt install fail2ban -y
```

### Enable

```bash
sudo systemctl enable fail2ban --now
```

### Check status

```bash
sudo systemctl status fail2ban
```

### How it works

Fail2ban monitors `/var/log/auth.log`. If too many failed SSH login attempts come from a single IP — it bans that IP via UFW for a set period.

Default behavior: **5 failed attempts in 10 minutes → 10-minute ban**.

### Check banned IPs

```bash
sudo fail2ban-client status sshd
```

---

## Additional recommendations

**Most important — 2FA on your Tailscale account.**
Your Tailscale account = access to your entire network. Protect it with two-factor authentication at [login.tailscale.com](https://login.tailscale.com).

**Strong password on Pi.**
The default `pi` user with a weak password is the first target in any attack.

**Keep the system updated:**
```bash
sudo apt update && sudo apt upgrade -y
```

---

*Next: [Bonus — VNC on Pi →](setup-vnc.md)*
