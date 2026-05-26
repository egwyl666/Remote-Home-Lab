# 🖥 Bonus: VNC on Raspberry Pi

VNC дає доступ до графічного інтерфейсу (GUI) Raspberry Pi. Це опціональна фіча — для більшості завдань достатньо SSH. Але якщо потрібен повноцінний робочий стіл Pi — VNC стане в нагоді.

---

## When do you need this? / Коли це потрібно?

- Хочеш бачити робочий стіл Pi (XFCE, GNOME тощо)
- Запускаєш GUI-додатки на Pi
- Дебагінг візуальних речей

Якщо тобі потрібен лише термінал — використовуй SSH. Це швидше і простіше.

---

## Requirements / Що потрібно

- Встановлений десктоп на Pi (XFCE, GNOME тощо)
- `tightvncserver` або інший VNC сервер

Перевірити чи є десктоп:
```bash
dpkg -l | grep -E "xfce|gnome|kde|lxde"
```

---

## Installation / Встановлення

TightVNC зазвичай вже встановлений на Kali. Перевірити:
```bash
dpkg -l | grep tightvncserver
```

Якщо немає:
```bash
sudo apt install tightvncserver -y
```

---

## Setup / Налаштування

### 1. Встановити пароль

```bash
vncpasswd
```

Мінімум 6 символів. На питання про view-only пароль — `n`.

### 2. Створити скрипт запуску

```bash
mkdir -p ~/.vnc && cat > ~/.vnc/xstartup << 'EOF'
#!/bin/bash
xrdb $HOME/.Xresources 2>/dev/null
startxfce4 &
EOF
chmod +x ~/.vnc/xstartup
```

> Замість `startxfce4` підстав свій десктоп якщо інший: `startgnome`, `startkde` тощо.

### 3. Запустити VNC сервер

```bash
vncserver :1 -geometry 1920x1080 -depth 24
```

Перевірити що слухає порт:
```bash
ss -tlnp | grep vnc
# Має бути: 0.0.0.0:5901
```

---

## Autostart / Автозапуск

Щоб VNC стартував автоматично після перезавантаження:

```bash
sudo nano /etc/systemd/system/vncserver@.service
```

Вставити:
```ini
[Unit]
Description=TightVNC server
After=syslog.target network.target

[Service]
Type=simple
User=pi
ExecStartPre=-/usr/bin/vncserver -kill :%i > /dev/null 2>&1
ExecStart=/bin/sh -c '/usr/bin/vncserver :%i -geometry 1920x1080 -depth 24 & sleep 2 && tail -f /home/pi/.vnc/kali-raspberry-pi:%i.log'
ExecStop=/usr/bin/vncserver -kill :%i

[Install]
WantedBy=multi-user.target
```

Активувати:
```bash
sudo systemctl daemon-reload
sudo systemctl enable vncserver@1 --now
sudo systemctl status vncserver@1
```

---

## Connect / Підключення

Підключатись на `TAILSCALE_IP:5901`.

| Пристрій | Клієнт |
|---|---|
| iPhone / Android | RealVNC Viewer |
| MacBook | Finder → Go → Connect to Server → `vnc://100.x.x.x:5901` |
| Windows | RealVNC Viewer або TigerVNC |

---

## Troubleshooting

**Не підключається:**
```bash
# Перевір що порт відкритий у файрволі
sudo ufw allow 5901/tcp

# Перевір що VNC слухає
ss -tlnp | grep 5901
```

**Чорний екран після підключення:**
Перевір `~/.vnc/xstartup` — скрипт має бути виконуваним і вказувати на правильний десктоп.

```bash
chmod +x ~/.vnc/xstartup
cat ~/.vnc/xstartup
```

**Перезапустити VNC вручну:**
```bash
vncserver -kill :1
vncserver :1 -geometry 1920x1080 -depth 24
```

---

*← [Back to Security](security.md) | [Back to README](../README.md)*
