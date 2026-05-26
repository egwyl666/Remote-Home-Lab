# 🖥 RDP Setup (Windows)

RDP (Remote Desktop Protocol) — вбудований в Windows протокол для віддаленого доступу до робочого столу. Швидкий, стабільний, безкоштовний.

---

## How it works / Як це працює

```
Твій пристрій
      │
      ▼ RDP через Tailscale IP (100.x.x.x)
      │
   Windows PC ──► повний доступ до робочого столу
```

Завдяки Tailscale — RDP не торчить в інтернет. Він доступний лише пристроям у твоїй Tailscale мережі.

---

## Step 1 — Enable RDP on Windows / Увімкнути RDP

```
Win + R → sysdm.cpl → Enter
```

1. Вкладка **Remote**
2. Вибери **Allow remote connections to this computer**
3. Натисни **OK**

Або через Settings:
```
Settings → System → Remote Desktop → Enable Remote Desktop → ON
```

---

## Step 2 — Find your Tailscale IP / Знайти Tailscale IP

На Windows відкрий термінал:
```cmd
tailscale ip
```

Або знайди в системному треї — клікни на іконку Tailscale.

Запиши IP виду `100.x.x.x` — він знадобиться для підключення.

---

## Step 3 — Connect / Підключитись

### From MacBook / З MacBook

Встановити [Microsoft Remote Desktop](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466) з App Store.

Додати новий ПК:
- **PC name:** `100.x.x.x` (Tailscale IP твого Windows ПК)
- **User account:** ім'я облікового запису Windows + пароль

### From iPhone / Android

Встановити **Microsoft RDP** з App Store / Google Play.

Підключення аналогічне — вказати Tailscale IP і креди Windows.

### From Windows

```
Win + R → mstsc → Enter
```

Ввести Tailscale IP і підключитись.

### From Linux

```bash
remmina
# або
xfreerdp /v:100.x.x.x /u:YOUR_USERNAME
```

---

## Tips / Поради

**Credentials / Креди:**
- Username — ім'я облікового запису Windows. Перевірити:
```cmd
whoami
```
- Password — пароль від цього облікового запису. Якщо входиш через Microsoft акаунт — використовуй пароль від нього.

**Multiple monitors / Кілька моніторів:**
У більшості RDP клієнтів є опція **Use all monitors** або **Multiple displays** — вмикай в налаштуваннях підключення.

**Slow connection / Повільне з'єднання:**
Знизь якість зображення в налаштуваннях RDP клієнта — суттєво збільшує швидкість на слабкому інтернеті.

---

## Security note / Безпека

RDP **не відкритий в інтернет** — він доступний лише через Tailscale. Це значно безпечніше ніж класичний RDP з пробросом порту 3389.

---

*Next: [Security →](security.md)*
