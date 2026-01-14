Perfect 👍 If you use **Linux every single day**, mastering these shortcuts will **save you hours**.
Below is a **battle-tested Linux shortcut cheat sheet** used by **DevOps, SREs, SysAdmins** daily.

---

# 🔥 Linux Keyboard Shortcuts (You SHOULD Memorize)

## 🧠 Terminal Navigation (MOST USED)

| Shortcut    | Action                               |
| ----------- | ------------------------------------ |
| `Ctrl + A`  | Move cursor to **start of line**     |
| `Ctrl + E`  | Move cursor to **end of line**       |
| `Alt + B`   | Move back **one word**               |
| `Alt + F`   | Move forward **one word**            |
| `Ctrl + XX` | Toggle between cursor and line start |

---

## 🔍 Search & History (EXTREMELY POWERFUL)

| Shortcut   | Action                                 |
| ---------- | -------------------------------------- |
| `Ctrl + R` | **Reverse search** command history     |
| `Ctrl + S` | Forward search (may need `stty -ixon`) |
| `Ctrl + P` | Previous command                       |
| `Ctrl + N` | Next command                           |
| `history`  | Show command history                   |
| `!!`       | Run **last command**                   |
| `!n`       | Run command number `n`                 |
| `!ssh`     | Run last command starting with `ssh`   |

📌 **DevOps tip**: `Ctrl + R + keyword` = 🚀 fastest way to work

---

## ✏️ Editing Commands

| Shortcut   | Action                         |
| ---------- | ------------------------------ |
| `Ctrl + U` | Delete from cursor → start     |
| `Ctrl + K` | Delete from cursor → end       |
| `Ctrl + W` | Delete **last word**           |
| `Alt + D`  | Delete next word               |
| `Ctrl + Y` | Paste (yank) last deleted text |

---

## 🧨 Process & Job Control

| Shortcut   | Action                                 |
| ---------- | -------------------------------------- |
| `Ctrl + C` | Kill running process                   |
| `Ctrl + Z` | Suspend process                        |
| `bg`       | Resume suspended process in background |
| `fg`       | Bring process to foreground            |
| `Ctrl + D` | Exit terminal / send EOF               |

---

## 🧾 Screen & Terminal Tricks

| Shortcut   | Action                             |
| ---------- | ---------------------------------- |
| `Ctrl + L` | Clear screen (better than `clear`) |
| `reset`    | Fix broken terminal                |
| `exit`     | Close terminal                     |
| `Tab`      | Auto-complete                      |
| `Tab Tab`  | Show all completions               |

---

## 🧠 Bash Power Moves (ADVANCED)

| Shortcut            | Action                                         |
| ------------------- | ---------------------------------------------- |
| `Alt + .`           | Insert **last argument** from previous command |
| `!!:s/old/new`      | Replace word in previous command               |
| `!$`                | Last argument                                  |
| `!*`                | All arguments                                  |
| `Ctrl + X Ctrl + E` | Open command in editor                         |

Example:

```bash
mkdir myapp
cd !$     # expands to: cd myapp
```

---

## 🖥️ File Navigation (CLI)

| Shortcut      | Action                     |
| ------------- | -------------------------- |
| `cd -`        | Jump to previous directory |
| `pushd /path` | Save directory             |
| `popd`        | Return back                |

---

## 🌍 Environment / Bash

| Command              | Action                     |
| -------------------- | -------------------------- |
| `alias ll='ls -alF'` | Create shortcut            |
| `unalias ll`         | Remove shortcut            |
| `source ~/.bashrc`   | Reload config              |
| `env`                | Show environment variables |

---

## ⚡ MUST-KNOW DevOps Combos

```bash
Ctrl + R + docker   # search docker commands
Ctrl + A + sudo     # jump to start → add sudo
Ctrl + E            # jump end → execute
```

---

## 🧩 Enable `Ctrl + S` (optional but useful)

```bash
stty -ixon
```

---

## 🎯 TOP 10 YOU SHOULD MASTER FIRST

If you remember only these, you're already **advanced**:

1️⃣ Ctrl + A
2️⃣ Ctrl + E
3️⃣ Ctrl + R
4️⃣ Ctrl + W
5️⃣ Ctrl + U
6️⃣ Ctrl + K
7️⃣ Ctrl + L
8️⃣ Ctrl + C
9️⃣ Ctrl + Z
🔟 Alt + .

---

## 🔥 Want more?

I can give you:

- ✅ **One-page printable cheat sheet**
- ✅ **DevOps daily workflows using shortcuts**
- ✅ **Vim + Bash combo shortcuts**
- ✅ **Tmux shortcuts (VERY important)**

Tell me what you want next 🚀
