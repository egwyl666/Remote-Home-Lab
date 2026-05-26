# 🖥 RDP Setup (Windows)

RDP (Remote Desktop Protocol) is Windows' built-in protocol for remote desktop access. Fast, stable, and free.

---

## How it works

```
Your device
      │
      ▼ RDP via Tailscale IP (100.x.x.x)
      │
   Windows PC ──► full desktop access
```

Thanks to Tailscale, RDP is never exposed to the internet — it's only accessible to devices on your Tailscale network.

---

## Step 1 — Enable RDP on Windows

```
Win + R → sysdm.cpl → Enter
```

1. Go to the **Remote** tab
2. Select **Allow remote connections to this computer**
3. Click **OK**

Or via Settings:
```
Settings → System → Remote Desktop → Enable Remote Desktop → ON
```

---

## Step 2 — Find your Tailscale IP

On Windows, open a terminal:
```cmd
tailscale ip
```

Or click the Tailscale icon in the system tray.

Write down the IP in the form `100.x.x.x` — you'll need it to connect.

---

## Step 3 — Connect

### From MacBook

Install [Microsoft Remote Desktop](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466) from the App Store.

Add a new PC:
- **PC name:** `100.x.x.x` (Tailscale IP of your Windows PC)
- **User account:** your Windows username + password

### From iPhone / Android

Install **Microsoft RDP** from the App Store / Google Play.

Same process — enter the Tailscale IP and Windows credentials.

### From Windows

```
Win + R → mstsc → Enter
```

Enter the Tailscale IP and connect.

### From Linux

```bash
remmina
# or
xfreerdp /v:100.x.x.x /u:YOUR_USERNAME
```

---

## Tips

**Credentials:**
- Username — your Windows account name. Check it with:
```cmd
whoami
```
- Password — the password for that account. If you sign in with a Microsoft account, use that password.

**Multiple monitors:**
Most RDP clients have a **Use all monitors** or **Multiple displays** option — enable it in the connection settings.

**Slow connection:**
Lower the display quality in your RDP client settings — makes a big difference on a weak internet connection.

---

## Security note

RDP is **not exposed to the internet** — it's only reachable via Tailscale. This is far safer than the classic approach of forwarding port 3389.

---

*Next: [Security →](security.md)*
