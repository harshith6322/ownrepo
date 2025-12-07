---

# 🧠 **Networking Commands for DevOps & DevSecOps Engineers**

---

## 🧩 **1. Connectivity Testing Tools**

These help check **if a host or service is reachable** — first step in debugging.

### 🔹 `ping`

Checks basic connectivity between two systems using ICMP packets.

**Syntax:**

```bash
ping google.com
```

**Useful Flags:**

| Flag           | Description              | Example                |
| -------------- | ------------------------ | ---------------------- |
| `-c <count>`   | Number of packets        | `ping -c 4 google.com` |
| `-i <sec>`     | Interval between packets | `ping -i 2 8.8.8.8`    |
| `-W <timeout>` | Timeout per request      | `ping -W 2 google.com` |

---

### 🔹 `traceroute`

Traces the route packets take to reach a destination (layer 3 path).

**Syntax:**

```bash
traceroute google.com
```

**Useful Flags:**

| Flag        | Description                   | Example                    |
| ----------- | ----------------------------- | -------------------------- |
| `-n`        | Show IPs instead of DNS names | `traceroute -n google.com` |
| `-m <hops>` | Max hops to probe             | `traceroute -m 20 8.8.8.8` |

---

### 🔹 `tracepath`

Similar to `traceroute` but **doesn’t require root privileges**.

**Syntax:**

```bash
tracepath google.com
```

👉 Preferred for quick route tracing when `traceroute` isn’t installed.

---

---

## 🌍 **2. DNS & Domain Investigation Tools**

Used to debug **DNS resolution issues**, **domain mapping**, and **record lookups**.

### 🔹 `nslookup`

Queries DNS to find IP addresses of domains or vice versa.

**Examples:**

```bash
nslookup google.com
nslookup -type=MX gmail.com
```

**Common Flags:**

| Flag         | Description      |
| ------------ | ---------------- |
| `-type=A`    | IPv4 address     |
| `-type=AAAA` | IPv6 address     |
| `-type=MX`   | Mail server      |
| `-type=TXT`  | Text/SPF records |

---

### 🔹 `dig` (Domain Information Groper)

Advanced DNS query tool — more details than nslookup.

**Examples:**

```bash
dig google.com
dig A google.com
dig +short google.com
dig @8.8.8.8 google.com
```

**Useful Flags:**

| Flag      | Description                    |
| --------- | ------------------------------ |
| `+short`  | Clean short output             |
| `+trace`  | Trace full DNS resolution path |
| `@server` | Use specific DNS server        |

---

---

## 🔐 **3. Network Port and Socket Inspection**

These tools are essential for **checking open ports**, **active connections**, and **listening services**.

### 🔹 `ss` (Socket Statistics)

Modern replacement for `netstat`.

**Examples:**

```bash
ss -tuln
ss -tnp
```

**Useful Flags:**

| Flag | Description                     |
| ---- | ------------------------------- |
| `-t` | TCP sockets                     |
| `-u` | UDP sockets                     |
| `-l` | Listening ports                 |
| `-n` | Show numeric addresses (no DNS) |
| `-p` | Show process using the port     |

✅ Example Output:

```bash
Netid  State  Local Address:Port   Peer Address:Port  Process
tcp    LISTEN 0.0.0.0:22           0.0.0.0:*          sshd
```



---

## 🌐 **4. Network Troubleshooting & Web Testing**

Used to test **web endpoints, APIs, file downloads, and proxy behaviors**.

### 🔹 `curl`

Transfers data to/from a server using supported protocols (HTTP, HTTPS, FTP, etc.)

**Examples:**

```bash
curl https://google.com
curl -I https://example.com      # Show headers only
curl -v https://example.com      # Verbose output
curl -O https://file.com/file.zip # Save file
```

**Useful Flags:**

| Flag                 | Description              |
| -------------------- | ------------------------ |
| `-I`                 | Fetch only headers       |
| `-L`                 | Follow redirects         |
| `-v`                 | Verbose mode             |
| `-O`                 | Save file with same name |
| `-u user:pass`       | Basic auth               |
| `-H "Header: value"` | Add custom header        |
| `-d`                 | Send POST data           |

---

### 🔹 `wget`

Used for **downloading files** from web servers (non-interactive).

**Examples:**

```bash
wget https://example.com/file.zip
wget -O output.html https://example.com
```

**Useful Flags:**

| Flag                | Description                       |
| ------------------- | --------------------------------- |
| `-O`                | Output to file                    |
| `-c`                | Continue interrupted download     |
| `--limit-rate=200k` | Limit download speed              |
| `--spider`          | Check if URL exists (no download) |

---

---

## 🧭 **5. Network Configuration & Interface Info**

Useful to check **IP addresses**, **routes**, and **network interfaces**.

### 🔹 `ip` command (modern replacement for ifconfig)

**Examples:**

```bash
ip addr show
ip route show
ip link show
```

**Useful Flags:**

| Command | Description             |
| ------- | ----------------------- |
| `ip a`  | Show all interfaces     |
| `ip r`  | Show routing table      |
| `ip l`  | Show link-layer devices |

---

### 🔹 `ifconfig` (deprecated but still used)

**Examples:**

```bash
ifconfig
ifconfig eth0 down
ifconfig eth0 up
```

---

---

## 🧱 **6. Network Connectivity & Port Testing**

### 🔹 `nc` (netcat)

Swiss-army knife for network testing and debugging.

**Examples:**

```bash
nc -zv google.com 443           # Check if port open
nc -l 8080                      # Listen on port
nc -v -w2 8.8.8.8 53            # Test DNS port
```

**Flags:**

| Flag | Description              |
| ---- | ------------------------ |
| `-z` | Scan mode (no data sent) |
| `-v` | Verbose                  |
| `-w` | Timeout                  |

---

---

## 🧾 **7. Packet Capture & Analysis**

For **deep inspection** and **DevSecOps security analysis**.

### 🔹 `tcpdump`

Captures and inspects network packets.

**Examples:**

```bash
sudo tcpdump -i eth0
sudo tcpdump -i eth0 port 80
sudo tcpdump -n host 8.8.8.8
```

**Useful Flags:**

| Flag           | Description            |
| -------------- | ---------------------- |
| `-i <iface>`   | Interface to capture   |
| `-n`           | Don’t resolve names    |
| `-A`           | Print in ASCII         |
| `-w file.pcap` | Save capture to file   |
| `-r file.pcap` | Read from capture file |

---

---

## 🔒 **8. DevSecOps-Specific Networking Checks**

### 🔹 `nmap`

Scans networks and detects open ports, services, OS, etc. (commonly used in DevSecOps audits).

**Examples:**

```bash
nmap localhost
nmap -sS 192.168.1.0/24
nmap -p 22,80,443 example.com
```

**Useful Flags:**

| Flag  | Description                              |
| ----- | ---------------------------------------- |
| `-sS` | Stealth SYN scan                         |
| `-p`  | Scan specific ports                      |
| `-A`  | Aggressive scan (OS + version detection) |
| `-Pn` | Skip ping discovery                      |

---

### 🔹 `whois`

Displays ownership and registration info about domains.

**Example:**

```bash
whois google.com
```

---

### 🔹 `curl --head --insecure`

For **testing endpoints** ignoring SSL certs (used in DevSecOps when checking TLS misconfigs).

**Example:**

```bash
curl -I --insecure https://internal-api.example.com
```

---

## 🧩 **9. Network Debugging + Logs**

### 🔹 `journalctl -u network`

Shows logs for network services (on systemd systems).

### 🔹 `dmesg | grep eth`

Kernel-level logs for interface issues.

---

## ⚙️ **10. Quick Categorization Summary**

| Category            | Commands                           |
| ------------------- | ---------------------------------- |
| 🧩 Connectivity     | `ping`, `traceroute`, `tracepath`  |
| 🌍 DNS              | `nslookup`, `dig`                  |
| 🔐 Sockets/Ports    | `ss`, `netstat`                    |
| 🌐 HTTP/API         | `curl`, `wget`                     |
| 🧭 Interface/Routes | `ip`, `ifconfig`                   |
| 🔌 Port Testing     | `telnet`, `nc`                     |
| 🧾 Packet Analysis  | `tcpdump`, `nmap`                  |
| 🔒 Security/Recon   | `whois`, `nmap`, `curl --insecure` |
| 🪵 Logs             | `journalctl`, `dmesg`              |

---

Would you like me to create a **one-page cheat sheet (PDF)** summarizing all these commands + flags + use cases — like a DevOps networking quick reference you can keep while troubleshooting servers or clusters?
