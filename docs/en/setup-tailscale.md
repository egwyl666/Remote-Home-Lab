# 🔵 Tailscale Setup

Tailscale is a mesh VPN built on WireGuard. It connects all your devices into one private network — no static IP, no open ports, no complex configuration.

---

## How it works

```
iPhone  ──┐
MacBook ──┼──► Tailscale Network (100.x.x.x) ◄──┬── Raspberry Pi
PC      ──┘                                       └── any other device
```

Every device gets a permanent IP in the `100.x.x.x` range. Traffic between them is encrypted via WireGuard.

---

## Installation

### Raspberry Pi (Linux)

```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

> ⚠️ If you get a key conflict error during `apt update` (VSCode repository) — see [Troubleshooting](#troubleshooting) below.

Authenticate:
```bash
sudo tailscale up
```

Tailscale will print a link — open it in a browser and log in (Google / GitHub / email).

Check your IP:
```bash
tailscale ip
# Output: 100.72.14.55
```

Check status:
```bash
tailscale status
```

### Windows / macOS / iOS / Android

Download from [tailscale.com/download](https://tailscale.com/download) and log in with the **same account**.

---

## Autostart

Tailscale automatically registers itself in systemd on Linux. Verify:

```bash
sudo systemctl is-enabled tailscaled
# enabled — you're good
```

---

## Troubleshooting

### apt key conflict (VSCode + Tailscale)

Symptom:
```
E: Conflicting values set for option Signed-By regarding source https://packages.microsoft.com/repos/code/
```

Cause: VSCode was added to apt twice — via both a `.list` and a `.sources` file, each pointing to a different key path.

Fix:
```bash
# Disable the old .list file
sudo mv /etc/apt/sources.list.d/vscode.list /etc/apt/sources.list.d/vscode.list.bak

# Copy the key to where the .sources file expects it
sudo cp /etc/apt/keyrings/packages.microsoft.gpg /usr/share/keyrings/microsoft.gpg

# Retry Tailscale installation
curl -fsSL https://tailscale.com/install.sh | sh
```

---

## Security

- Enable **2FA on your Tailscale account** — this is the single most important step, since your account = access to your entire network
- Tailscale Admin Console: [login.tailscale.com](https://login.tailscale.com) — view all devices and revoke access

---

*Next: [Wake on LAN →](setup-wol.md)*
