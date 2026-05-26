# 🔒 Security — UFW + Fail2ban

Два інструменти які закривають більшість векторів атак на Pi.

---

## Overview / Огляд

| Інструмент | Що робить |
|---|---|
| **UFW** | Файрвол. Блокує всі вхідні з'єднання крім дозволених |
| **Fail2ban** | Банить IP після кількох невдалих спроб входу по SSH |

---

## UFW — Firewall

### Install / Встановити

```bash
sudo apt install ufw -y
```

### Configure / Налаштувати

```bash
# Блокувати всі вхідні з'єднання за замовчуванням
sudo ufw default deny incoming

# Дозволити всі вихідні (оновлення, Tailscale і т.д.)
sudo ufw default allow outgoing

# Відкрити SSH
sudo ufw allow ssh

# Відкрити VNC (якщо використовуєш)
sudo ufw allow 5901/tcp

# Увімкнути файрвол
sudo ufw enable
```

> ⚠️ Обов'язково відкрий SSH (`allow ssh`) **перед** увімкненням файрвола — інакше заблокуєш сам себе.

### Check status / Перевірити статус

```bash
sudo ufw status verbose
```

Виведе щось на зразок:
```
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
5901/tcp                   ALLOW IN    Anywhere
```

### Why not 80/443? / Чому не відкриваємо 80/443?

Порти 80 і 443 потрібні коли Pi **роздає** сайт назовні. Якщо Pi не є веб-сервером — відкривати їх не потрібно. Вихідні з'єднання (apt, tailscale, curl) вже покриті правилом `allow outgoing`.

---

## Fail2ban

### Install / Встановити

```bash
sudo apt install fail2ban -y
```

### Enable / Увімкнути

```bash
sudo systemctl enable fail2ban --now
```

### Check status / Перевірити статус

```bash
sudo systemctl status fail2ban
```

### How it works / Як працює

Fail2ban моніторить `/var/log/auth.log`. Якщо з одного IP надходить забагато невдалих спроб входу по SSH — IP баниться через UFW на певний час.

За замовчуванням: **5 невдалих спроб за 10 хвилин → бан на 10 хвилин**.

### Check banned IPs / Переглянути забанені IP

```bash
sudo fail2ban-client status sshd
```

---

## Additional recommendations / Додаткові рекомендації

**Найважливіше — 2FA на Tailscale акаунті.**
Tailscale акаунт = доступ до всієї твоєї мережі. Захисти його двофакторною автентифікацією на [login.tailscale.com](https://login.tailscale.com).

**Сильний пароль на Pi.**
Стандартний юзер `pi` з простим паролем — перша ціль при атаці.

**Тримай систему оновленою:**
```bash
sudo apt update && sudo apt upgrade -y
```

---

*Next: [Bonus — VNC on Pi →](setup-vnc.md)*
