# 🌐 Networking Quick Reference

**DevOps · Networking · Cybersecurity**

> **Tags:** `🔵 D` = DevOps &nbsp;|&nbsp; `🟢 N` = Networking &nbsp;|&nbsp; `🔴 S` = Security

---

## Table of Contents

- [1. Ping & Trace](#1-ping--trace)
- [2. DNS](#2-dns)
- [3. API & Web Testing](#3-api--web-testing)
- [4. Ports & Sockets](#4-ports--sockets)
- [5. IP & Firewall](#5-ip--firewall)
- [6. Network Scan](#6-network-scan)
- [7. Other Tools](#7-other-tools)
- [8. Network Devices](#8-network-devices)
- [9. Office Network Topology](#9-office-network-topology)
- [10. Quick Reference Cheatsheet](#10-quick-reference-cheatsheet)

---

## 1. Ping & Trace

---

### `ping` · `🔵 D` `🟢 N` `🔴 S`

> Test ICMP reachability. First tool in any connectivity issue.

| Flag      | Description                                 |
| --------- | ------------------------------------------- |
| `-c 4`    | Stop after 4 packets                        |
| `-i 0.2`  | Interval between packets (seconds)          |
| `-s 1400` | Packet size — use to test MTU fragmentation |
| `-W 2`    | Wait timeout per reply                      |
| `-f`      | Flood ping — max speed (root only)          |
| `-I eth0` | Send through a specific interface           |
| `-4 / -6` | Force IPv4 or IPv6                          |

```bash
ping -c 4 google.com
ping -s 1400 -c 3 8.8.8.8          # MTU test
sudo ping -f -c 100 192.168.1.1    # stress test
ping -c 5 -i 0.2 192.168.1.1      # rapid test
```

- `🔵 D` Health checks in CI/CD pipelines, Kubernetes pod reachability
- `🟢 N` Basic L3 connectivity test, latency baseline
- `🔴 S` Host discovery, firewall rule verification

---

### `traceroute` · `🔵 D` `🟢 N` `🔴 S`

> Shows every L3 hop to destination. UDP by default — use TCP to bypass firewalls.

| Flag     | Description                          |
| -------- | ------------------------------------ |
| `-n`     | Skip DNS — numeric IPs only (faster) |
| `-T`     | TCP mode — bypasses ICMP/UDP blocks  |
| `-p 443` | Target port (use with `-T`)          |
| `-m 15`  | Max hops (default 30)                |
| `-q 1`   | 1 probe per hop — faster output      |

```bash
traceroute google.com
traceroute -n -T -p 443 google.com
sudo traceroute -T -p 80 example.com
traceroute -m 20 -q 1 -n 8.8.8.8
```

- `🔵 D` Debug why a service can't reach an endpoint
- `🟢 N` Map routing path, find where packets die
- `🔴 S` Trace packet path through security devices

---

### `tracepath` · `🟢 N`

> Like traceroute but **no root required**. Also discovers path MTU.

```bash
tracepath google.com
tracepath -n 8.8.8.8
```

---

### `mtr` · `🔵 D` `🟢 N` `🔴 S`

> My Traceroute = live ping + traceroute. Shows per-hop packet loss and RTT in real time. Best tool for intermittent issues.

| Flag               | Description                      |
| ------------------ | -------------------------------- |
| `--report`         | Run N cycles then print a report |
| `--no-dns`         | Numeric only                     |
| `--tcp --port 443` | TCP probe mode                   |
| `-r -c 20`         | Report mode, 20 cycles           |

```bash
mtr google.com
mtr --report --no-dns 8.8.8.8
sudo mtr --tcp --port 443 github.com
mtr -r -c 20 -n 8.8.8.8 > mtr_report.txt
```

- `🔵 D` Identify flapping links, find lossy hops
- `🟢 N` Best single tool for intermittent network problems
- `🔴 S` Verify traffic path goes through expected security devices

---

## 2. DNS

---

### `dig` · `🔵 D` `🟢 N` `🔴 S`

> Advanced DNS query tool. Industry standard for DNS debugging. Shows full response.

| Flag             | Description                          |
| ---------------- | ------------------------------------ |
| `+short`         | Clean answer only                    |
| `+trace`         | Full resolution path from root NS    |
| `@8.8.8.8`       | Use a specific resolver              |
| `-x <IP>`        | Reverse lookup — IP → hostname (PTR) |
| `-t MX`          | Specify record type                  |
| `+noall +answer` | Answer section only                  |
| `+tcp`           | Force TCP (large/DNSSEC responses)   |

```bash
dig google.com A
dig +short google.com
dig @1.1.1.1 google.com MX
dig +trace google.com
dig -x 8.8.8.8
dig +noall +answer google.com TXT
dig +noall +answer google.com AAAA
```

- `🔵 D` Debug DNS propagation after deployment
- `🟢 N` Check all record types, trace full resolution chain
- `🔴 S` DNS recon, DNSSEC check, zone transfer test (`dig axfr`)

---

### `nslookup` · `🔵 D` `🟢 N` `🔴 S`

> Simple DNS tool. Works on Windows too — good for cross-platform comparison.

| Flag         | Description               |
| ------------ | ------------------------- |
| `-type=A`    | IPv4 address records      |
| `-type=AAAA` | IPv6 records              |
| `-type=MX`   | Mail exchange records     |
| `-type=TXT`  | SPF / DKIM text records   |
| `-type=NS`   | Authoritative nameservers |

```bash
nslookup google.com
nslookup -type=MX gmail.com
nslookup -type=TXT google.com 8.8.8.8
nslookup 8.8.8.8                    # reverse lookup
```

---

### `whois` · `🟢 N` `🔴 S`

> Domain registration, owner, registrar, IP block and ASN ownership.

```bash
whois google.com
whois 8.8.8.8                       # IP block owner
whois AS15169                        # Google ASN lookup
whois 203.0.113.0/24
```

- `🔴 S` OSINT recon, incident response attribution

---

### `resolvectl` · `🔵 D` `🟢 N`

> Control systemd-resolved. Shows DNS per interface, flush cache, view stats.

```bash
resolvectl status                   # DNS config per interface
resolvectl query google.com         # resolve via systemd
resolvectl flush-caches             # clear DNS cache
resolvectl statistics               # cache hit/miss stats
```

---

### `host` · `🟢 N`

> Quick DNS lookup. Simpler output than dig — good for scripts.

```bash
host google.com
host -t MX gmail.com
host 8.8.8.8                        # reverse lookup
```

---

## 3. API & Web Testing

---

### `curl` · `🔵 D` `🟢 N` `🔴 S`

> The #1 tool for HTTP testing, API calls, health checks, TLS debugging, and automation.

| Flag                 | Description                                    |
| -------------------- | ---------------------------------------------- |
| `-I`                 | Headers only (HEAD request)                    |
| `-v`                 | Verbose — full request + response              |
| `-L`                 | Follow redirects                               |
| `-X POST`            | HTTP method                                    |
| `-H "Key: Val"`      | Add request header                             |
| `-d '{"key":"val"}'` | Request body                                   |
| `-u user:pass`       | Basic authentication                           |
| `-k`                 | Skip TLS certificate verification              |
| `-o file / -O`       | Save response to file                          |
| `-w "%{http_code}"`  | Output HTTP status code                        |
| `-s`                 | Silent — no progress bar                       |
| `-m 10`              | Max total time (seconds)                       |
| `--resolve H:P:IP`   | Override DNS (test staging without DNS change) |
| `--cacert file`      | Custom CA certificate                          |

```bash
curl https://api.example.com/health
curl -I https://google.com
curl -v https://example.com 2>&1 | grep "^[<>*]"
curl -X POST -H "Content-Type: application/json" \
     -d '{"name":"test"}' https://api.example.com
curl -H "Authorization: Bearer TOKEN" https://api.example.com/users
curl -w "%{http_code}" -s -o /dev/null https://example.com
curl -k https://internal.service.local/api
curl --resolve api.prod.com:443:192.168.1.50 https://api.prod.com
```

- `🔵 D` API health checks, CI/CD tests, scraping endpoints
- `🟢 N` HTTP debugging, response header inspection
- `🔴 S` Web app testing, cert inspection, bypass SSL for testing

---

### `wget` · `🔵 D` `🟢 N`

> Download files. Better than curl for resuming and recursive downloads.

| Flag                | Description                   |
| ------------------- | ----------------------------- |
| `-O file`           | Save with specific filename   |
| `-c`                | Continue interrupted download |
| `-r`                | Recursive site download       |
| `-q`                | Quiet mode                    |
| `--limit-rate=200k` | Throttle speed                |
| `--spider`          | Check URL without downloading |
| `--no-check-cert`   | Skip SSL verification         |

```bash
wget https://example.com/file.tar.gz
wget -c https://bigfile.example.com/large.iso
wget --spider https://example.com/api/health
wget -q -O - https://api.example.com | jq .
```

---

### `openssl s_client` · `🔵 D` `🟢 N` `🔴 S`

> Test TLS connections directly. Check certs, cipher suites, and protocol versions.

```bash


# Check cert expiry:
echo | openssl s_client -connect google.com:443 2>/dev/null | openssl x509 -noout -dates
# Test SMTP with STARTTLS:
openssl s_client -connect smtp.gmail.com:587 -starttls smtp
# Check cipher in use:
openssl s_client -tls1_2 -connect example.com:443 2>&1 | grep Cipher
```

- `🔴 S` Verify cert validity, check for weak ciphers, test TLS version support

---

## 4. Ports & Sockets

---

### `ss` · `🔵 D` `🟢 N` `🔴 S`

> Socket statistics — modern, faster replacement for netstat. Shows all sockets + process info.

| Flag                | Description                            |
| ------------------- | -------------------------------------- |
| `-t`                | TCP only                               |
| `-u`                | UDP only                               |
| `-l`                | Listening ports only                   |
| `-a`                | All states                             |
| `-n`                | Numeric — no DNS or service resolution |
| `-p`                | Show process name + PID                |
| `-s`                | Summary statistics                     |
| `state ESTABLISHED` | Filter by TCP state                    |
| `dport :443`        | Filter by destination port             |

```bash
ss -tlnp                                         # listening TCP + process
ss -tulnp                                        # TCP + UDP listening
ss -tan                                          # all TCP + states
ss -tan state ESTABLISHED
ss -tlnp | grep :443
ss -tan | awk '{print $1}' | sort | uniq -c | sort -rn   # count by state
```

**TCP State Cheatsheet:**

| State         | Meaning                                  |
| ------------- | ---------------------------------------- |
| `LISTEN`      | Server waiting for connections           |
| `ESTABLISHED` | Active connection, data flowing          |
| `TIME-WAIT`   | Normal close — waits 2×MSL (~60s)        |
| `CLOSE-WAIT`  | App hasn't called close() — possible bug |
| `SYN-SENT`    | Client sent SYN, waiting for SYN-ACK     |
| `SYN-RECV`    | Server got SYN, sent SYN-ACK             |

---

### `netstat` · `🔵 D` `🟢 N` `🔴 S`

> Deprecated but still everywhere — containers, minimal Linux, older systems.

```bash
netstat -tlnp                       # listening ports
netstat -an | grep :80
netstat -r                          # routing table
netstat -s | grep error             # protocol stats
```

---

### `nc (netcat)` · `🔵 D` `🟢 N` `🔴 S`

> Swiss-army knife. Port testing, listeners, file transfer, banner grabbing.

| Flag   | Description                             |
| ------ | --------------------------------------- |
| `-z`   | Scan mode — connect and close, no data  |
| `-v`   | Verbose output                          |
| `-l`   | Listen mode (act as server)             |
| `-u`   | UDP mode                                |
| `-w 2` | Timeout (seconds)                       |
| `-k`   | Keep listening after client disconnects |

```bash
nc -zv google.com 443               # port check
nc -zv 192.168.1.1 22               # SSH port check
nc -l 9999
                       # simple listener
cat file.txt | nc 192.168.1.50 9999 # file transfer
echo "GET / HTTP/1.0\r\n\r\n" | nc google.com 80
```

- `🔴 S` Banner grabbing, raw TCP testing, reverse shell

---

### `tcpdump` · `🔵 D` `🟢 N` `🔴 S`

> Capture raw packets. Save to `.pcap` for Wireshark analysis.

| Flag           | Description                         |
| -------------- | ----------------------------------- |
| `-i any`       | All interfaces                      |
| `-nn`          | No hostname or port name resolution |
| `-v / -vv`     | More verbose packet detail          |
| `-A`           | Print payload in ASCII              |
| `-X`           | Print payload in hex + ASCII        |
| `-w file.pcap` | Write capture to file               |
| `-r file.pcap` | Read + filter saved capture         |
| `-c 100`       | Capture only 100 packets            |

**BPF Filters:**

```bash
sudo tcpdump -i any -nn
sudo tcpdump -i eth0 port 80
sudo tcpdump -nn host 8.8.8.8 and port 53
sudo tcpdump -i eth0 -nn -w capture.pcap
sudo tcpdump -r capture.pcap port 443
sudo tcpdump -A -nn port 80 not port 22
```

- `🔴 S` Capture credentials in transit, malware traffic analysis, forensics

---

### `lsof -i` · `🔵 D` `🟢 N` `🔴 S`

> Find which process owns a port. Use when bind() fails with "address already in use".

```bash
sudo lsof -i :80
sudo lsof -i :443
sudo lsof -i -n -P | grep LISTEN
sudo lsof -i tcp | grep ESTABLISHED
```

---

## 5. IP & Firewall

---

### `ip` · `🔵 D` `🟢 N`

> Modern replacement for ifconfig + route + arp. Manage everything network-related.

| Subcommand | What it does                      |
| ---------- | --------------------------------- |
| `ip addr`  | Show / add / del IP addresses     |
| `ip link`  | Show / manage interfaces          |
| `ip route` | Show / add / del routing table    |
| `ip neigh` | ARP cache — show / flush          |
| `ip netns` | Network namespaces (Docker / K8s) |

```bash
ip addr show
ip addr add 192.168.1.10/24 dev eth0
ip addr del 192.168.1.10/24 dev eth0
ip link show
ip link set eth0 up
ip route show
ip route get 8.8.8.8                # which route would be used?
ip route add 10.0.0.0/8 via 192.168.1.1
ip route del 10.0.0.0/8
ip neigh show                       # ARP table
ip neigh flush all                  # flush ARP cache
ip -s link show eth0                # interface stats
ip netns list                       # list namespaces
```

---

### `iptables` · `🔵 D` `🟢 N` `🔴 S`

> Linux packet filter and NAT engine. Foundation of Docker networking and all Linux firewalls.

| Flag                       | Description                         |
| -------------------------- | ----------------------------------- |
| `-L -n -v`                 | List all rules with counters        |
| `-t nat`                   | NAT table (PREROUTING, POSTROUTING) |
| `-A INPUT`                 | Append rule to chain                |
| `-I INPUT 1`               | Insert at top of chain              |
| `-D chain`                 | Delete a rule                       |
| `-F`                       | Flush all rules (CAREFUL)           |
| `-j ACCEPT/DROP`           | Rule action                         |
| `-m state --state EST,REL` | Connection tracking match           |

```bash
sudo iptables -L -n -v
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
sudo iptables -t nat -L -n -v
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE          # NAT
sudo iptables -t nat -A PREROUTING -p tcp --dport 80 \
     -j DNAT --to 192.168.1.50:80                                   # port forward
sudo iptables-save > /etc/iptables/rules.v4
```

---

### `ufw` · `🔵 D` `🟢 N` `🔴 S`

> Ubuntu/Debian firewall manager. Simple interface over iptables.

```bash
sudo ufw status verbose
sudo ufw enable
sudo ufw allow 22
sudo ufw allow 80/tcp
sudo ufw allow from 192.168.1.0/24 to any port 22
sudo ufw deny 23
sudo ufw delete allow 80
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw app list                   # app profiles (OpenSSH, Nginx, etc.)
```

---

### `nft (nftables)` · `🔵 D` `🟢 N` `🔴 S`

> Modern iptables replacement. Default on RHEL 8+, Fedora, newer Ubuntu.

```bash
sudo nft list ruleset
sudo nft flush ruleset
sudo nft add rule inet filter input tcp dport 22 accept
sudo nft add rule inet filter input drop
sudo nft -f /etc/nftables.conf
```

---

## 6. Network Scan

---

### `nmap` · `🔵 D` `🟢 N` `🔴 S`

> Port scanner, service detector, OS fingerprinter, vulnerability scanner. Know every flag.

| Flag            | Description                                     |
| --------------- | ----------------------------------------------- |
| `-sS`           | SYN stealth scan — fast, no full connect (root) |
| `-sT`           | TCP connect scan — no root needed               |
| `-sU`           | UDP scan — slow but important for DNS/SNMP      |
| `-sn`           | Ping sweep — host discovery, no port scan       |
| `-p 22,80,443`  | Specific ports                                  |
| `-p-`           | All 65535 ports                                 |
| `-sV`           | Service version detection                       |
| `-O`            | OS fingerprinting                               |
| `-A`            | Aggressive: OS + version + scripts + traceroute |
| `-Pn`           | Skip ping — treat host as up                    |
| `-T4`           | Aggressive timing (T0=slow, T5=insane)          |
| `--script vuln` | NSE vulnerability scripts                       |
| `--open`        | Show open ports only                            |
| `-oN file`      | Save output to text file                        |

```bash
nmap localhost
nmap -sn 192.168.1.0/24                        # host discovery
nmap -p 22,80,443 192.168.1.50
sudo nmap -sS -sV -O 192.168.1.1
nmap -A -T4 example.com
sudo nmap --script vuln 192.168.1.50
nmap -p- --open -T4 192.168.1.50 -oN scan.txt
```

- `🔵 D` Inventory open ports before deployment, verify firewall rules
- `🟢 N` Network topology discovery, service enumeration
- `🔴 S` Vulnerability scanning, attack surface mapping, recon

---

### `arp-scan` · `🟢 N` `🔴 S`

> ARP-level LAN host discovery. Faster and more reliable than ping. Finds devices that block ICMP.

```bash
sudo arp-scan -l                                # scan local network
sudo arp-scan -I eth0 --localnet
sudo arp-scan 192.168.1.0/24
sudo arp-scan -l | grep -i "cisco\|apple\|dell"
```

- `🔴 S` Detect unauthorized devices on LAN

---

### `nmcli` · `🔵 D` `🟢 N`

> NetworkManager CLI. Manage WiFi, Ethernet, and VPN connections.

```bash
nmcli device status
nmcli device wifi list
nmcli device wifi connect "OfficeWiFi" password "pass123"
nmcli connection show
nmcli -p connection show "Wired 1"
nmcli general status
nmcli connection up "VPN"
```

---

### `fping` · `🟢 N` `🔴 S`

> Ping multiple hosts simultaneously. Much faster than sequential ping for subnet checks.

```bash
fping -g 192.168.1.0/24                        # entire subnet
fping -a -g 192.168.1.0/24 2>/dev/null         # alive hosts only
fping -c 3 google.com 8.8.8.8 1.1.1.1
fping -g -q 10.0.0.0/24 2>/dev/null
```

---

### `iw / iwconfig` · `🔵 D` `🟢 N`

> Wireless interface management. `iw` is modern; `iwconfig` is deprecated.

```bash
iw dev                                          # list wireless interfaces
sudo iw wlan0 scan | grep -E "SSID|signal"
iw wlan0 link                                   # current connection details
iwconfig wlan0                                  # (deprecated but still used)
```

---

## 7. Other Tools

---

### `ethtool` · `🔵 D` `🟢 N`

> Query NIC settings — speed, duplex, driver, errors, offload features.

```bash
ethtool eth0                        # speed / duplex / link status
ethtool -i eth0                     # driver + firmware version
ethtool -S eth0 | grep error        # NIC error counters
ethtool -k eth0                     # hardware offload features
sudo ethtool -p eth0 10             # blink NIC LED for 10s (identify physical port)
```

---

### `iperf3` · `🔵 D` `🟢 N`

> Measure actual network throughput between two hosts.

```bash
# On server:
iperf3 -s

# On client:
iperf3 -c 192.168.1.10
iperf3 -c 192.168.1.10 -t 30 -P 4      # 30s, 4 parallel streams
iperf3 -c 192.168.1.10 -R               # reverse (server → client)
iperf3 -c 192.168.1.10 -u -b 100M      # UDP test at 100 Mbps
```

- `🔵 D` Verify VPN throughput, baseline infrastructure before/after changes

---

### `socat` · `🔵 D` `🟢 N` `🔴 S`

> Advanced netcat. Relay between any two socket types — TCP, UDP, SSL, Unix sockets.

```bash
socat TCP-LISTEN:8080,fork TCP:localhost:80    # port forward
socat - TCP:google.com:80                     # raw TCP connection
socat SSL:host:443 -                          # SSL connection
socat UNIX-LISTEN:/tmp/sock TCP:localhost:5000
```

---

### `journalctl + dmesg` · `🔵 D` `🟢 N` `🔴 S`

> System and kernel logs for network events — interface errors, driver issues, DHCP.

```bash
journalctl -u NetworkManager -f
journalctl -k | grep -i "eth\|link\|error"
dmesg | grep -i "eth\|ens\|wlan"
dmesg -T | tail -30
```

---

## 8. Network Devices

---

### Hub — Layer 1 (Physical)

> 📸 [View device photos → Wikipedia: Ethernet hub](https://en.wikipedia.org/wiki/Ethernet_hub)

```
FRONT PANEL
┌──────────────────────────────────────────────┐
│  [●][●][●][●][●][●][●][●]  [UPLINK●][PWR●] │
│   1  2  3  4  5  6  7  8                    │
│                  8-Port Hub                  │
└──────────────────────────────────────────────┘

BACK PANEL
┌──────────────────────────────────────────────┐
│  [⚡ Power input]                            │
│  (no config — dumb device)                   │
└──────────────────────────────────────────────┘
```

**What it does:** Broadcasts ALL traffic to ALL ports. No intelligence.
One shared collision domain. Every device receives every packet.

**Status:** ⚠️ Obsolete — completely replaced by switches.

| Tag  | Detail                                                        |
| ---- | ------------------------------------------------------------- |
| 🟢 N | 1 collision domain. Half-duplex. CSMA/CD required.            |
| 🔴 S | Security risk — all traffic visible to all devices on segment |

---

### Managed Switch — Layer 2 (Data Link)

> 📸 [View device photos → Wikipedia: Network switch](https://en.wikipedia.org/wiki/Network_switch)

```
FRONT PANEL (24-port 1U rack)
┌──────────────────────────────────────────────────────────────┐
│  [■][■][■][■][■][■][■][■][■][■][■][■]  [SFP▪▪] [PWR●]    │
│  [■][■][■][■][■][■][■][■][■][■][■][■]  [ACT●]             │
│   1  2  3  4  5  6  7  8  9 10 11 12   13-24               │
│                  MANAGED SWITCH 24G + 4SFP                   │
└──────────────────────────────────────────────────────────────┘

BACK PANEL
┌──────────────────────────────────────────────────────────────┐
│  [⚡ PWR]  [Console RJ45]  [Mgmt ETH]  ≋≋≋≋≋≋ (vents)     │
└──────────────────────────────────────────────────────────────┘
```

**What it does:** Learns MAC addresses → forwards frames only to the correct port.
Key features: VLANs, STP (loop prevention), LACP (link bonding), QoS, port security, 802.1X auth.

| Tag  | Detail                                                                                  |
| ---- | --------------------------------------------------------------------------------------- |
| 🔵 D | Configure VLANs to isolate dev/prod/mgmt. Cloud-managed = zero-touch provisioning.      |
| 🟢 N | Each port = separate collision domain. STP prevents broadcast storms. 802.1Q VLAN tags. |
| 🔴 S | 802.1X port authentication, VLAN isolation, DHCP snooping, dynamic ARP inspection       |

---

### Router — Layer 3 (Network)

> 📸 [View device photos → Wikipedia: Router](<https://en.wikipedia.org/wiki/Router_(computing)>)

```
FRONT PANEL (Home/SOHO router)
           [Ant1] [Ant2] [Ant3]  ← WiFi antennas
┌────────────────────────────────────────────────┐
│  [WAN●]  [LAN1●][LAN2●][LAN3●][LAN4●]        │
│  (blue)   ←————— yellow LAN ports —————→      │
│  [USB]  [WPS]  [WiFi]  [PWR]                  │
└────────────────────────────────────────────────┘

BACK PANEL
┌────────────────────────────────────────────────┐
│  [WAN]  [LAN1][LAN2][LAN3][LAN4]  [⚡ PWR]   │
│  [USB]  [Reset]                               │
└────────────────────────────────────────────────┘
```

**What it does:** Connects different IP networks (subnets). Routes packets by IP address.
Implements NAT. Separates broadcast domains. Has routing table with default gateway.

| Tag  | Detail                                                                           |
| ---- | -------------------------------------------------------------------------------- |
| 🔵 D | Default gateway for all servers. AWS VPC route tables = virtual router.          |
| 🟢 N | Longest prefix match for routing. BGP/OSPF for dynamic routing. NAT at LAN edge. |
| 🔴 S | Router ACLs = first-hop filtering. BGP hijacking is a real attack vector.        |

---

### Firewall Appliance — Layer 3–7

> 📸 [View device photos → Wikipedia: Firewall](<https://en.wikipedia.org/wiki/Firewall_(computing)>)

```
FRONT PANEL (1U rack appliance)
┌──────────────────────────────────────────────────────────────┐
│  [WAN●] [LAN●] [DMZ●] [MGMT●] [HA●]     [LCD: OK]         │
│  (red)  (green)(amber)(blue) (purple)                        │
│                   NGFW Appliance                             │
└──────────────────────────────────────────────────────────────┘

BACK PANEL
┌──────────────────────────────────────────────────────────────┐
│  [⚡ PSU x2 redundant]  [Console RJ45]  ≋≋≋≋≋≋ (vents)    │
└──────────────────────────────────────────────────────────────┘
```

**What it does:** Stateful packet inspection. Separates WAN / LAN / DMZ zones.
Enforces security policies. Modern NGFWs also do SSL inspection, threat signatures, sandboxing.

**Brands:** Palo Alto, Fortinet FortiGate, Cisco ASA/Firepower, pfSense, OPNsense

| Tag  | Detail                                                                                 |
| ---- | -------------------------------------------------------------------------------------- |
| 🔵 D | AWS Security Groups = cloud firewall. Manage rules as code with Terraform.             |
| 🟢 N | Zone-based policies. Stateful connection tracking (conntrack). SNAT/DNAT at WAN.       |
| 🔴 S | IPS integration, SSL deep inspection, threat intel, user identity policies, sandboxing |

---

### Access Point (AP) — Layer 2 (Data Link)

> 📸 [View device photos → Wikipedia: Wireless access point](https://en.wikipedia.org/wiki/Wireless_access_point)

```
FRONT PANEL (ceiling-mount enterprise AP)
         ┌──────────────────────┐
         │   ○   ○   ○   ○   ○ │  ← LED ring shows status
         │    Ubiquiti UniFi    │
         │     (flat disc)      │
         │      WiFi 6 AP       │
         └──────────────────────┘
              ↓ ceiling mount

BACK PANEL
         ┌──────────────────────┐
         │  [RJ45 PoE input]    │  ← Single cable = data + power
         │  [Kensington slot]   │
         │  [Mounting bracket]  │
         └──────────────────────┘
```

**What it does:** Extends WiFi. PoE-powered from switch (no separate power cable).
Managed by a controller. Supports seamless roaming between APs.

**Brands:** Ubiquiti UniFi, Cisco Meraki, Aruba, Ruckus

| Tag  | Detail                                                                                   |
| ---- | ---------------------------------------------------------------------------------------- |
| 🔵 D | RADIUS/802.1X integrates with Active Directory. Cloud-managed = zero-touch provisioning. |
| 🟢 N | BSS per radio. CSMA/CA for wireless. 802.11r fast roaming. PoE = 802.3af/at.             |
| 🔴 S | WPA3-Enterprise + certificates, rogue AP detection, per-SSID VLAN isolation              |

**AP vs Repeater:**

| Feature    | Access Point         | Repeater / Extender |
| ---------- | -------------------- | ------------------- |
| Connection | Ethernet cable (PoE) | WiFi (wireless)     |
| Bandwidth  | Full speed           | Halved (~50%)       |
| Grade      | Enterprise           | Home use            |
| Latency    | Low                  | Higher              |

---

### Modem / ONT — Layer 1–2

> 📸 [View device photos → Wikipedia: Cable modem](https://en.wikipedia.org/wiki/Cable_modem)

```
FRONT PANEL (Cable modem — coaxial input)
┌──────────────────────────────────────────┐
│  ISP Logo                                │
│  [PWR●] [DS●] [US●] [ONLINE●] [ETH●]   │
│  ← green LEDs — all should be lit →     │
└──────────────────────────────────────────┘

BACK PANEL
┌──────────────────────────────────────────┐
│  [Coax ⊗]  [ETH ×1–4]  [⚡ PWR]       │
│  (threaded) (to router)                 │
└──────────────────────────────────────────┘

BACK PANEL — DSL Modem
┌──────────────────────────────────────────┐
│  [Phone ▬ RJ11]  [ETH ×1]  [⚡ PWR]   │
└──────────────────────────────────────────┘
```

**What it does:** Converts ISP signal (coax / phone line / fiber) to Ethernet.
Provides public IP via ISP DHCP. Usually ISP-supplied.

**Types:**

| Type        | Signal            | Protocol       |
| ----------- | ----------------- | -------------- |
| Cable modem | Coaxial cable     | DOCSIS 3.1     |
| DSL modem   | Phone line (RJ11) | ADSL2+ / VDSL2 |
| ONT         | Fiber optic       | GPON / XGS-PON |

| Tag  | Detail                                                                  |
| ---- | ----------------------------------------------------------------------- |
| 🔵 D | WAN starting point. Rarely configurable. Bridge mode recommended.       |
| 🟢 N | DOCSIS 3.1 supports up to 10 Gbps downstream.                           |
| 🔴 S | Publicly exposed. Default credentials = critical risk. Update firmware. |

---

### Patch Panel — Layer 1 (Physical)

> 📸 [View device photos → Wikipedia: Patch panel](https://en.wikipedia.org/wiki/Patch_panel)

```
FRONT PANEL (24-port 1U)
┌──────────────────────────────────────────────────────────────┐
│  [▣][▣][▣][▣][▣][▣][▣][▣][▣][▣][▣][▣]  ← RJ45 patch ports │
│  [▣][▣][▣][▣][▣][▣][▣][▣][▣][▣][▣][▣]                     │
│   1  2  3  4  5  6  7  8  9 10 11 12    13-24               │
└──────────────────────────────────────────────────────────────┘

BACK PANEL (punch-down wires from wall jacks)
┌──────────────────────────────────────────────────────────────┐
│  ║ ║ ║ ║ ║ ║ ║ ║ ║ ║ ║ ║ ║ ║ ║ ║ ║ ║ ║ ║ ║ ║ ║ ║      │
│  ← 110-block punch-down — Cat6A wires from wall jacks →    │
└──────────────────────────────────────────────────────────────┘
```

**What it does:** Passive cable management. Wall jack → patch panel front → switch above.
Enables port changes without pulling cable through walls. No active electronics.

| Tag  | Detail                                                                     |
| ---- | -------------------------------------------------------------------------- |
| 🔵 D | Label every port (both ends). Critical for operational efficiency.         |
| 🟢 N | 1U = 24 ports. Cat6A supports 10 GbE at full 100m. TIA-568 standard.       |
| 🔴 S | Physical security risk — unauthorized patch = unauthorized network access. |

---

### PoE Switch — Layer 2 (Data Link)

> 📸 [View device photos → Wikipedia: Power over Ethernet](https://en.wikipedia.org/wiki/Power_over_Ethernet)

```
FRONT PANEL
┌──────────────────────────────────────────────────────────────┐
│  [PoE●][PoE●][PoE●][PoE●][PoE●][PoE●][PoE●][PoE●]        │
│   ← Power delivered over these RJ45 ports →                 │
│                                          [SFP▪] [PWR●]      │
│              PoE+ Switch — 802.3at (30W per port)            │
└──────────────────────────────────────────────────────────────┘
```

**What it does:** Managed switch that also delivers electrical power over ethernet.
Powers APs, IP cameras, VoIP phones, IoT — no separate power adapters needed.

**PoE Standards:**

| Standard        | Max Power | Use For                    |
| --------------- | --------- | -------------------------- |
| 802.3af (PoE)   | 15.4W     | Basic APs, VoIP phones     |
| 802.3at (PoE+)  | 30W       | Cameras, mid-range APs     |
| 802.3bt (PoE++) | 90W       | Thin clients, high-end APs |

| Tag  | Detail                                                                           |
| ---- | -------------------------------------------------------------------------------- |
| 🔵 D | PoE budget = total wattage of all devices. Exceeding it = random device reboots. |
| 🟢 N | Power negotiated via LLDP-MED. CLI port shutdown = remote device power off.      |
| 🔴 S | Isolate IP cameras and IoT on a separate VLAN.                                   |

---

### IDS / IPS — Layer 3–7

> 📸 [View device photos → Wikipedia: Intrusion detection system](https://en.wikipedia.org/wiki/Intrusion_detection_system)

```
FRONT PANEL (1U rack appliance)
┌──────────────────────────────────────────────────────────────┐
│  [LCD: THREATS: 0 | SIGNATURES: 45,231]                     │
│  [MON ●]  [MGMT ●]  [HA ●]                                 │
│                   IDS / IPS Appliance                        │
└──────────────────────────────────────────────────────────────┘

BACK PANEL
┌──────────────────────────────────────────────────────────────┐
│  [⚡ PWR x2]  [Console]  ≋≋≋≋≋≋ (vents)                   │
└──────────────────────────────────────────────────────────────┘
```

**What it does:**

- **IDS (Intrusion Detection)** — Alert only. Passive — tap or SPAN mirror port.
- **IPS (Intrusion Prevention)** — Alert + Block. Inline between firewall and core switch.

**Open-source:** Snort, Suricata **Commercial:** Palo Alto, Cisco Firepower

| Tag  | Detail                                                                               |
| ---- | ------------------------------------------------------------------------------------ |
| 🔵 D | Forward alerts to SIEM (Splunk, Elastic). Requires tuning to reduce false positives. |
| 🟢 N | Deployed inline (IPS) or on SPAN port (IDS). Dedicated MON interface for traffic.    |
| 🔴 S | Signature + behavioral. Detects SQLi, XSS, exploits, port scans, C2 callbacks.       |

---

## 9. Office Network Topology

```
                    ┌───────────────────────────┐
                    │         INTERNET           │
                    └──────────────┬────────────┘
                                   │ ISP fiber / coax / DSL
                    ┌──────────────┴────────────┐
                    │        Modem / ONT         │  ← Public IP from ISP
                    │  (converts signal→Ethernet)│
                    └──────────────┬────────────┘
                                   │ Ethernet
                    ┌──────────────┴────────────┐
                    │   Perimeter Firewall       │  ← WAN / LAN / DMZ
                    │  (Palo Alto / pfSense)     │  ← Stateful + IPS
                    └──────┬───────┬──────┬─────┘
                           │       │      └──────────────────────┐
                     DMZ   │       │ LAN                         │
                           │       │                         ┌───┴──────────┐
          ┌────────────────┘       │                         │  IDS/IPS     │
          │                        │                         │ (SPAN port)  │
  ┌───────┴────────┐    ┌──────────┴──────────┐             └──────────────┘
  │   DMZ Switch   │    │    Core Switch (L3)  │
  └───┬────────────┘    │  (STP root, inter-   │
      │                 │   VLAN routing)       │
  ┌───┴──────┐          └──────┬─────┬────┬────┘
  │ Web srv  │                 │     │    │
  │ Mail srv │     ┌───────────┘     │    └───────────────┐
  │ VPN GW   │     │ Floor/Access    │ WiFi PoE           │ Server Room
  └──────────┘     │ Switch (L2)     │ Switch (L2)        │ Switch (L2)
  203.0.113.x      │ VLAN 10         │ VLAN 20            │ VLAN 40
                   └─────┬───────    └─────┬──────        └──────┬──────
                         │                 │                      │
                   ┌─────┴─────┐     ┌────┴────────┐      ┌────┴────────┐
                   │Patch Panel│     │ [AP1][AP2]  │      │ AD / DNS    │
                   └─────┬─────┘     │  (WiFi 6)   │      │ File server │
                         │           │  PoE-powered │      │ App server  │
                   ┌─────┴──────┐    └─────────────┘      │ Backup      │
                  PC1  PC2  VoIP                           └─────────────┘
               .10.10 .10.11 .30.x                          10.0.0.1–.4
```

---

### IP Addressing Plan

| Zone         | VLAN    | Subnet          | Description                   |
| ------------ | ------- | --------------- | ----------------------------- |
| Management   | VLAN 1  | 192.168.1.0/24  | Switches, APs, printers       |
| Staff LAN    | VLAN 10 | 192.168.10.0/24 | Office PCs, laptops           |
| WiFi / Guest | VLAN 20 | 192.168.20.0/24 | Wireless devices, IoT, guests |
| VoIP         | VLAN 30 | 192.168.30.0/24 | IP phones — QoS priority      |
| Servers      | VLAN 40 | 10.0.0.0/24     | AD, DNS, file, app servers    |
| DMZ          | —       | 203.0.113.0/28  | Public web / mail servers     |

---

### Traffic Flows

```
Internet → Firewall (DNAT) → DMZ web server        # inbound public traffic
Staff PC → Access Switch → Core Switch → Firewall → Internet
Staff PC → Access Switch → Core Switch → Server Room # internal, no firewall
VoIP Phone → Access Switch → Core Switch → PSTN GW  # QoS prioritized
```

---

### Each Component's Role

| Device             | Role                                | Key Config                                          |
| ------------------ | ----------------------------------- | --------------------------------------------------- |
| Modem / ONT        | ISP signal to Ethernet              | Bridge mode — let firewall do NAT                   |
| Firewall           | Zone separation, policy enforcement | Allow outbound 80/443, block all inbound except DMZ |
| Core switch (L3)   | Inter-VLAN routing, STP root        | SVI per VLAN, 10G uplinks to access switches        |
| DMZ switch         | Isolate public servers              | Only firewall uplink, no LAN access                 |
| Access switch (L2) | Floor-level device connection       | Access ports on VLAN 10, trunk to core              |
| PoE switch         | Power + data to APs and cameras     | Plan PoE budget per port                            |
| Access points      | WiFi per zone / floor               | SSID → VLAN mapping, WPA3-Enterprise                |
| Patch panel        | Cable management in rack            | Label both ends, one panel per switch               |
| IDS / IPS          | Detect and block threats            | SPAN port (IDS) or inline (IPS)                     |
| Server room        | Internal services                   | No internet exposure, AD/DNS/file                   |
| DMZ servers        | Public-facing services              | Port 80/443/25 only, hardened OS                    |

---

## 10. Quick Reference Cheatsheet

### By Symptom — What to Run First

| Symptom                 | First Command                        | Follow-up                 |
| ----------------------- | ------------------------------------ | ------------------------- |
| Can't reach a host      | `ping host`                          | `traceroute -T host`      |
| DNS not resolving       | `dig +trace domain`                  | `resolvectl status`       |
| Port not responding     | `nc -zv host port`                   | `nmap -p port host`       |
| Port already in use     | `ss -tlnp \| grep :port`             | `sudo lsof -i :port`      |
| Slow / lossy connection | `mtr host`                           | `iperf3 -c server`        |
| Who's on my network     | `sudo arp-scan -l`                   | `nmap -sn 192.168.1.0/24` |
| Check firewall rules    | `sudo iptables -L -n -v`             | `sudo ufw status verbose` |
| TLS / cert issue        | `openssl s_client -connect host:443` | `curl -v https://host`    |
| WiFi issues             | `iw wlan0 link`                      | `nmcli device status`     |
| Interface down          | `ip link show`                       | `ethtool eth0`            |
| Check routing           | `ip route show`                      | `ip route get 8.8.8.8`    |
| Capture traffic         | `sudo tcpdump -i eth0 -nn`           | Open in Wireshark         |

---

### Command Category Quick Map

| Category           | Commands                                     |
| ------------------ | -------------------------------------------- |
| 🔴 Connectivity    | `ping` `traceroute` `tracepath` `mtr`        |
| 🌐 DNS             | `dig` `nslookup` `whois` `host` `resolvectl` |
| 🌍 API / Web       | `curl` `wget` `openssl s_client`             |
| 🔌 Ports / Sockets | `ss` `netstat` `nc` `tcpdump` `lsof`         |
| 🛡️ IP / Firewall   | `ip` `iptables` `ufw` `nft`                  |
| 🔍 Scan            | `nmap` `arp-scan` `nmcli` `fping` `iw`       |
| 🛠️ Other           | `ethtool` `iperf3` `socat` `journalctl`      |

---

_Last updated: June 2026 — DevOps Networking Reference_
