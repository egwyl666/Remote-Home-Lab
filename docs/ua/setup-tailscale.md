# 🔵 Tailscale Setup

Tailscale — це mesh VPN на базі WireGuard. Він з'єднує всі твої пристрої в одну приватну мережу без статичного IP, без відкритих портів, без складної конфігурації.

---

## How it works / Як це працює

```
iPhone  ──┐
MacBook ──┼──► Tailscale Network (100.x.x.x) ◄──┬── Raspberry Pi
PC      ──┘                                       └── any other device
```

Кожен пристрій отримує постійний IP у діапазоні `100.x.x.x`. Трафік між ними зашифрований через WireGuard.

---

## Installation / Встановлення

### Raspberry Pi (Linux)

```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

> ⚠️ Якщо під час `apt update` виникає конфлікт ключів Microsoft (VSCode репозиторій) — дивись секцію [Troubleshooting](#troubleshooting) нижче.

Авторизація:
```bash
sudo tailscale up
```

Tailscale виведе посилання — відкрий його в браузері і залогінься (Google / GitHub / email).

Перевір IP:
```bash
tailscale ip
# Виведе щось на зразок: 100.72.14.55
```

Перевір статус:
```bash
tailscale status
```

### Windows / macOS / iOS / Android

Завантажити з [tailscale.com/download](https://tailscale.com/download) і залогінитись в **той самий акаунт**.

---

## Autostart / Автозапуск

Tailscale автоматично прописується в systemd при встановленні на Linux. Перевірити:

```bash
sudo systemctl is-enabled tailscaled
# enabled — все добре
```

---

## Troubleshooting

### Конфлікт ключів apt (VSCode + Tailscale)

Симптом:
```
E: Conflicting values set for option Signed-By regarding source https://packages.microsoft.com/repos/code/
```

Причина: VSCode додано в apt двічі — через `.list` і `.sources` файли з різними шляхами до ключа.

Вирішення:
```bash
# Відключити старий .list файл
sudo mv /etc/apt/sources.list.d/vscode.list /etc/apt/sources.list.d/vscode.list.bak

# Скопіювати ключ туди куди дивиться .sources файл
sudo cp /etc/apt/keyrings/packages.microsoft.gpg /usr/share/keyrings/microsoft.gpg

# Повторити встановлення Tailscale
curl -fsSL https://tailscale.com/install.sh | sh
```

---

## Security / Безпека

- Увімкни **2FA на Tailscale акаунті** — це найважливіший крок, бо акаунт = доступ до всієї мережі
- Tailscale Admin Console: [login.tailscale.com](https://login.tailscale.com) — тут можна бачити всі пристрої і відкликати доступ

---

*Next: [Wake on LAN →](setup-wol.md)*
