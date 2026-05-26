# ⚡ Wake on LAN

Wake on LAN (WoL) — технологія яка дозволяє вмикати комп'ютер віддалено, надсилаючи "магічний пакет" на його MAC-адресу через локальну мережу.

---

## How it works / Як це працює

```
Твій пристрій (будь-де)
        │
        ▼ SSH через Tailscale
        │
Raspberry Pi (в локалці)
        │
        ▼ wakeonlan XX:XX:XX:XX:XX:XX
        │
      Роутер
        │
        ▼ Magic Packet (broadcast)
        │
      PC  ──► ВМИКАЄТЬСЯ 🟢
```

> ⚠️ WoL працює **тільки через Ethernet**. По WiFi ненадійно або взагалі не працює.

---

## Step 1 — Enable WoL in BIOS / Увімкнути WoL в BIOS

1. Перезавантаж ПК і зайди в BIOS (`Del`, `F2`, або `F12` залежно від материнської плати)
2. Знайди розділ **Power Management** або **Advanced**
3. Увімкни **Wake on LAN** або **Power On By PCI-E/PCI**
4. Збережи і вийди

---

## Step 2 — Enable WoL in Windows / Увімкнути WoL в Windows

1. `Win + X` → Device Manager
2. Знайди свій Ethernet адаптер → правий клік → Properties
3. Вкладка **Power Management** → постав галку **Allow this device to wake the computer**
4. Вкладка **Advanced** → знайди **Wake on Magic Packet** → встав `Enabled`

---

## Step 3 — Find MAC address / Знайти MAC-адресу

На Windows:
```cmd
ipconfig /all
```
або
```cmd
getmac /fo list /v
```

Шукай **Physical Address** у рядку твого Ethernet адаптера. Виглядає так:
```
Physical Address. . . . . . . . . : A1-B2-C3-D4-E5-F6
```

Запиши цю адресу — вона знадобиться на Pi.

---

## Step 4 — Install wakeonlan on Pi / Встановити wakeonlan на Pi

```bash
sudo apt install wakeonlan -y
```

---

## Step 5 — Wake your PC / Вмикаємо ПК

```bash
wakeonlan XX:XX:XX:XX:XX:XX
```

Замість `XX:XX:XX:XX:XX:XX` підстав свою MAC-адресу. Через 5–15 секунд ПК повинен увімкнутись.

---

## Tips / Поради

**Перевір що ПК і Pi в одній підмережі:**
```bash
# На Pi
hostname -I
# Наприклад: 192.168.50.150

# ПК має бути в тій самій мережі: 192.168.50.xxx
```

**Якщо не вмикається:**
- Переконайся що кабель Ethernet підключений
- Перевір BIOS — WoL має бути увімкнений
- Деякі роутери блокують broadcast пакети — спробуй вказати IP роутера:
```bash
wakeonlan -i 192.168.50.255 XX:XX:XX:XX:XX:XX
```

---

*Next: [RDP Setup →](setup-rdp.md)*
