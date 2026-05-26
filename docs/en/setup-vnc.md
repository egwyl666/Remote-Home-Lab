# 🖥 Bonus: VNC on Raspberry Pi

VNC gives you access to the graphical desktop (GUI) of your Raspberry Pi. This is an optional feature — for most tasks SSH is enough. But if you need a full desktop on Pi, VNC is the way to go.

---

## When do you need this?

- You want to see Pi's desktop (XFCE, GNOME, etc.)
- You're running GUI applications on Pi
- You need to debug visual things

If you only need a terminal — use SSH. It's faster and simpler.

---

## Requirements

- A desktop environment installed on Pi (XFCE, GNOME, etc.)
- `tightvncserver` or another VNC server

Check if a desktop is installed:
```bash
dpkg -l | grep -E "xfce|gnome|kde|lxde"
```

---

## Installation

TightVNC is usually pre-installed on Kali. Check:
```bash
dpkg -l | grep tightvncserver
```

If not installed:
```bash
sudo apt install tightvncserver -y
```

---

## Setup

### 1. Set a password

```bash
vncpasswd
```

Minimum 6 characters. When asked about a view-only password — type `n`.

### 2. Create a startup script

```bash
mkdir -p ~/.vnc && cat > ~/.vnc/xstartup << 'EOF'
#!/bin/bash
xrdb $HOME/.Xresources 2>/dev/null
startxfce4 &
EOF
chmod +x ~/.vnc/xstartup
```

> Replace `startxfce4` with your desktop if different: `startgnome`, `startkde`, etc.

### 3. Start the VNC server

```bash
vncserver :1 -geometry 1920x1080 -depth 24
```

Verify it's listening:
```bash
ss -tlnp | grep vnc
# Should show: 0.0.0.0:5901
```

---

## Autostart

To start VNC automatically after every reboot:

```bash
sudo nano /etc/systemd/system/vncserver@.service
```

Paste:
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

Enable it:
```bash
sudo systemctl daemon-reload
sudo systemctl enable vncserver@1 --now
sudo systemctl status vncserver@1
```

---

## Connect

Connect to `TAILSCALE_IP:5901`.

| Device | Client |
|---|---|
| iPhone / Android | RealVNC Viewer |
| MacBook | Finder → Go → Connect to Server → `vnc://100.x.x.x:5901` |
| Windows | RealVNC Viewer or TigerVNC |

---

## Troubleshooting

**Can't connect:**
```bash
# Make sure the port is open in the firewall
sudo ufw allow 5901/tcp

# Check VNC is listening
ss -tlnp | grep 5901
```

**Black screen after connecting:**
Check `~/.vnc/xstartup` — the script must be executable and point to the correct desktop.

```bash
chmod +x ~/.vnc/xstartup
cat ~/.vnc/xstartup
```

**Restart VNC manually:**
```bash
vncserver -kill :1
vncserver :1 -geometry 1920x1080 -depth 24
```

---

*← [Back to Security](security.md) | [Back to README](../../README.md)*
