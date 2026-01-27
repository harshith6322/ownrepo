Perfect. This is the **ONE full SSH note** — trimmed, practical, from **basic → advanced**, only things that **actually matter in real DevOps + interviews**.
No repetition. No noise. Bookmark-worthy 🔖

---

# 🔐 SSH — FULL NOTES (BASIC → ADVANCED, USEFUL ONLY)

---

## 1️⃣ What SSH Is (minimum)

- Secure remote login (encrypted)
- Default port: **22**
- Auth methods:
  - **Public/Private key** (recommended)
  - Password (legacy / insecure)

---

## 2️⃣ SSH KEY AUTH — CORE FLOW (VERY IMPORTANT)

```text
ssh-keygen → generate key (local)
ssh-copy-id → copy public key to server
ssh user@ip → passwordless login
```

Server stores public key in:

```bash
~/.ssh/authorized_keys
```

---

## 3️⃣ `ssh-keygen` — ONLY USEFUL COMMANDS

### Generate modern key

```bash
ssh-keygen -t ed25519 -f ~/.ssh/prod_key -C "prod"
```

### Extract public key from private (pro)

```bash
ssh-keygen -y -f ~/.ssh/prod_key > prod_key.pub
```

### Change passphrase

```bash
ssh-keygen -p -f ~/.ssh/prod_key
```

### Fingerprint (audit/interview)

```bash
ssh-keygen -lf ~/.ssh/prod_key.pub
```

### Remove broken known host

```bash
ssh-keygen -R ip
```

👉 **ed25519 > rsa** (faster, safer)

---

## 4️⃣ SSH LOGIN — PRACTICAL VARIANTS

```bash
ssh user@ip
ssh -i key.pem user@ip
ssh -p 2222 user@ip
ssh -vvv user@ip     # debugging
```

Force public key only:

```bash
ssh -o PreferredAuthentications=publickey user@ip
```

---

## 5️⃣ SSH AGENT — MUST KNOW

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/prod_key
ssh-add -l
ssh-add -D
```

✔ No repeated passphrase
✔ Used in CI/CD, Git, automation

---

## 6️⃣ SSH CONFIG — PRODUCTIVITY BOOST

```bash
~/.ssh/config
```

```ini
Host prod
  HostName 13.x.x.x
  User ubuntu
  IdentityFile ~/.ssh/prod_key
  Port 22
```

Connect:

```bash
ssh prod
```

🔥 Seniors always use this.

---

## 7️⃣ SCP — SECURE COPY (ESSENTIAL)

### Upload

```bash
scp file.txt user@ip:/path/
```

### Download

```bash
scp user@ip:/path/file.txt .
```

### Directory

```bash
scp -r folder user@ip:/path/
```

### With key & port

```bash
scp -i key.pem -P 2222 file user@ip:/path/
```

---

## 8️⃣ BETTER THAN SCP — `rsync` (REAL WORLD)

```bash
rsync -avz -e "ssh -i key.pem" folder user@ip:/path/
```

✔ Faster
✔ Resume support
✔ Less bandwidth

---

## 9️⃣ PORT FORWARDING / TUNNELING 🔥

### Local port forward (MOST USED)

```bash
ssh -L 8080:localhost:80 user@ip
```

Access remote service locally.

---

### Remote port forward

```bash
ssh -R 9000:localhost:3000 user@ip
```

Expose local app to server.

---

### Dynamic (SOCKS proxy)

```bash
ssh -D 1080 user@ip
```

Acts like a VPN.

---

### Background tunnel

```bash
ssh -f -N -L 3306:localhost:3306 user@ip
```

---

## 1️⃣0️⃣ JUMP HOST / BASTION

```bash
ssh -J user@bastion_ip user@private_ip
```

SSH config version:

```ini
Host private
  HostName 10.0.1.10
  User ubuntu
  ProxyJump bastion
```

---

## 1️⃣1️⃣ FILE PERMISSIONS (MOST COMMON FAILURE)

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
chmod 600 ~/.ssh/authorized_keys
```

❌ Wrong permissions = `Permission denied (publickey)`

---

## 1️⃣2️⃣ ENABLE / DISABLE AUTH METHODS

### Disable password (PROD)

```ini
PasswordAuthentication no
PubkeyAuthentication yes
```

### Enable password (testing only)

```ini
PasswordAuthentication yes
PubkeyAuthentication no
```

Restart:

```bash
sudo systemctl restart sshd
```

---

## 1️⃣3️⃣ SSH TROUBLESHOOTING — REAL COMMANDS

```bash
ssh -vvv user@ip
ssh-add -l
ls -l ~/.ssh
journalctl -u sshd
cat /var/log/auth.log
```

Fix host key mismatch:

```bash
ssh-keygen -R ip
```

---

## 1️⃣4️⃣ SECURITY HARDENING (PRO STUFF)

```ini
PermitRootLogin no
AllowUsers ubuntu devops
```

Restrict key usage:

```ini
no-port-forwarding,no-pty
```

(in `authorized_keys`)

---

## 1️⃣5️⃣ SSH IN CI/CD (REAL WORLD)

```bash
ssh -o StrictHostKeyChecking=no user@ip "deploy.sh"
```

Used in GitHub Actions / Jenkins.

---

## 🔥 FINAL PRO COMMAND LIST

```bash
ssh -vvv
ssh -L / -R / -D
ssh -J
ssh-keygen -y
ssh-keygen -lf
ssh-add
scp
rsync -e ssh
```

---

## 🧠 ONE-LINE SUMMARY

> **SSH is not about login — it’s about secure automation.**

You now have **complete SSH knowledge from beginner → senior DevOps level**.

If you want next:

- AWS EC2 SSH hardening checklist
- SSH interview scenarios (failure-based)
- SSH + Kubernetes workflows

Say it 🔥
