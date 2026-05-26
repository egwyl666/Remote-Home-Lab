# ⚡ Wake on LAN

Wake on LAN (WoL) is a technology that lets you power on a computer remotely by sending a "magic packet" to its MAC address over the local network.

---

## How it works

```
Your device (anywhere)
        │
        ▼ SSH via Tailscale
        │
Raspberry Pi (on your LAN)
        │
        ▼ wakeonlan XX:XX:XX:XX:XX:XX
        │
      Router
        │
        ▼ Magic Packet (broadcast)
        │
      PC  ──► POWERS ON 🟢
```

> ⚠️ WoL only works reliably over **Ethernet**. WiFi is unreliable or won't work at all.

---

## Step 1 — Enable WoL in BIOS

1. Reboot your PC and enter BIOS (`Del`, `F2`, or `F12` depending on your motherboard)
2. Find the **Power Management** or **Advanced** section
3. Enable **Wake on LAN** or **Power On By PCI-E/PCI**
4. Save and exit

---

## Step 2 — Enable WoL in Windows

1. `Win + X` → Device Manager
2. Find your Ethernet adapter → right-click → Properties
3. **Power Management** tab → check **Allow this device to wake the computer**
4. **Advanced** tab → find **Wake on Magic Packet** → set to `Enabled`

---

## Step 3 — Find your MAC address

On Windows:
```cmd
ipconfig /all
```
or
```cmd
getmac /fo list /v
```

Look for **Physical Address** on your Ethernet adapter. It looks like:
```
Physical Address. . . . . . . . . : A1-B2-C3-D4-E5-F6
```

Save this — you'll need it on the Pi.

---

## Step 4 — Install wakeonlan on Pi

```bash
sudo apt install wakeonlan -y
```

---

## Step 5 — Wake your PC

```bash
wakeonlan XX:XX:XX:XX:XX:XX
```

Replace `XX:XX:XX:XX:XX:XX` with your MAC address. Your PC should power on within 5–15 seconds.

---

## Tips

**Verify Pi and PC are on the same subnet:**
```bash
# On Pi
hostname -I
# Example: 192.168.50.150

# PC should be on the same network: 192.168.50.xxx
```

**If it doesn't wake up:**
- Make sure the Ethernet cable is plugged in
- Double-check BIOS — WoL must be enabled
- Some routers block broadcast packets — try specifying the broadcast address:
```bash
wakeonlan -i 192.168.50.255 XX:XX:XX:XX:XX:XX
```

---

*Next: [RDP Setup →](setup-rdp.md)*
