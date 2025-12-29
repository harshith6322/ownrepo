Below is **clean, interview-ready + practical notes on `tmux` (Linux Terminal Multiplexer)** — **zero → hero**, aligned with your **DevOps / Linux mastery goal**, Harshith.

![Image](https://opensource.com/sites/default/files/uploads/multiplex-tmux.png)

![Image](https://linuxhandbook.com/content/images/2024/03/tmux-windows-sessions-and-panes.webp)

![Image](https://linuxhandbook.com/content/images/2022/04/TMUX_MULTIPLE_SPLIT.png)

![Image](https://arcolinux.com/wp-content/uploads/2020/02/tmux-status-03.png)

---

# 📘 TMUX (Terminal Multiplexer) – Complete Notes

## 1️⃣ What is `tmux`?

`tmux` lets you:

- Run **multiple terminal sessions** in one window
- **Detach** sessions and keep them running
- **Reattach** from anywhere (SSH-friendly)
- Split terminals into **panes**, **windows**, **sessions**

👉 Extremely useful for **servers, DevOps, long-running jobs**

---

## 2️⃣ Why tmux is Important (DevOps POV)

| Use case            | Why tmux helps                          |
| ------------------- | --------------------------------------- |
| SSH disconnects     | Session stays alive                     |
| Kubernetes / Docker | Run logs, exec, monitoring side by side |
| CI/CD debugging     | Multiple panes for logs, builds         |
| System monitoring   | htop, logs, shell simultaneously        |

---

## 3️⃣ tmux Core Concepts (VERY IMPORTANT)

### 🧠 tmux hierarchy

```
Session
 ├── Window 1
 │    ├── Pane 1
 │    └── Pane 2
 └── Window 2
```

| Concept | Meaning          |
| ------- | ---------------- |
| Session | Entire workspace |
| Window  | Like a tab       |
| Pane    | Split screen     |

---

## 4️⃣ Installation

```bash
sudo apt update
sudo apt install tmux -y
```

Verify:

```bash
tmux -V
```

---

## 5️⃣ Starting tmux

### Start new session

```bash
tmux
```

### Start named session

```bash
tmux new -s devops
```

---

## 6️⃣ tmux Prefix Key 🔑

By default:

```
Ctrl + b
```

👉 Every tmux command starts **after prefix**

Example:

```
Ctrl + b , then c
```

---

## 7️⃣ Session Commands

| Action         | Command                       |
| -------------- | ----------------------------- |
| List sessions  | `tmux ls`                     |
| Attach session | `tmux attach -t devops`       |
| Detach session | `Ctrl + b` → `d`              |
| Kill session   | `tmux kill-session -t devops` |

💡 Detaching ≠ stopping

---

## 8️⃣ Window Management 🪟

| Action          | Shortcut         |
| --------------- | ---------------- |
| New window      | `Ctrl + b` → `c` |
| Next window     | `Ctrl + b` → `n` |
| Previous window | `Ctrl + b` → `p` |
| List windows    | `Ctrl + b` → `w` |
| Rename window   | `Ctrl + b` → `,` |
| Close window    | `exit`           |

---

## 9️⃣ Pane Management 🔲 (MOST USED)

### Split panes

| Split type | Shortcut         |
| ---------- | ---------------- |
| Vertical   | `Ctrl + b` → `%` |
| Horizontal | `Ctrl + b` → `"` |

### Move between panes

```
Ctrl + b → Arrow keys
```

### Resize panes

```
Ctrl + b → hold Ctrl + Arrow
```

### Close pane

```bash
exit
```

---

## 🔟 Copy Mode (Scroll Like a Pro)

Enter copy mode:

```
Ctrl + b → [
```

Then:

- Arrow keys / PgUp / PgDn
- Press `q` to quit

💡 Works even after scroll buffer is full

---

## 1️⃣1️⃣ Kill / Cleanup Commands

| Action        | Shortcut           |
| ------------- | ------------------ |
| Kill pane     | `Ctrl + b → x`     |
| Kill window   | `Ctrl + b → &`     |
| Kill all tmux | `tmux kill-server` |

---

## 1️⃣2️⃣ Rename Session

```bash
Ctrl + b → $
```

---

## 1️⃣3️⃣ Detach & Resume (Interview Favorite)

Detach:

```
Ctrl + b → d
```

Resume:

```bash
tmux attach
```

👉 Survive SSH disconnections 🔥

---

## 1️⃣4️⃣ tmux Configuration (`~/.tmux.conf`)

Example **basic power config**:

```bash
# Enable mouse
set -g mouse on

# Start window numbering from 1
set -g base-index 1

# Use Vim keys
setw -g mode-keys vi

# Fast reload config
bind r source-file ~/.tmux.conf \; display "Config Reloaded"
```

Reload without restarting:

```
Ctrl + b → r
```

---

## 1️⃣5️⃣ Practical DevOps Workflow Example

```text
Window 1:
 ├─ Pane 1 → kubectl get pods -w
 ├─ Pane 2 → kubectl logs -f pod

Window 2:
 └─ htop

Window 3:
 └─ docker stats
```

🔥 This is **real production tmux usage**

---

## 1️⃣6️⃣ tmux vs Screen (Interview Question)

| tmux                | screen  |
| ------------------- | ------- |
| Modern              | Older   |
| Better panes        | Limited |
| Easy configs        | Hard    |
| Actively maintained | Legacy  |

---

## 1️⃣7️⃣ Common tmux Interview Questions

**Q: What happens if SSH disconnects?**
👉 tmux session continues running

**Q: tmux vs background (`&`)?**
👉 tmux is interactive + persistent

**Q: Can multiple users attach same session?**
👉 Yes (collaboration/debugging)

---

## 1️⃣8️⃣ MUST REMEMBER Cheatsheet 📌

```
Ctrl + b c     → new window
Ctrl + b %     → vertical split
Ctrl + b "     → horizontal split
Ctrl + b d     → detach
tmux attach    → reattach
tmux ls        → list sessions
```

---

## 1️⃣9️⃣ Next Level (Tell me if you want)

I can give you:

- tmux **+ Kubernetes workflows**
- tmux **+ SSH + cloud servers**
- tmux **copy-paste + clipboard**
- tmux **advanced config (power user)**

Just tell me 👍
