# 🌐 Complete Networking Master Notes

> DevOps-Focused | Device Identification | Linux CLI | Concepts & Commands

---

# PART 1 — Network Devices Visual Guide

## Quick Identification Table

| Device        | Ports                | Key Physical Clue                    | OSI Layer | Intelligence  |
| ------------- | -------------------- | ------------------------------------ | --------- | ------------- |
| **Hub**       | 4–24 RJ45            | All ports same, cheap/old hardware   | L1        | ❌ None       |
| **Switch**    | 8–48 RJ45 + SFP      | Per-port LEDs, rack-mount, metal     | L2        | ✅ MAC table  |
| **Router**    | 1 WAN + 4 LAN        | WAN port different color / labeled   | L3        | ✅ Routing    |
| **Modem**     | Coax/Phone + 1 RJ45  | ISP branding, coaxial screw port     | L1–L2     | ❌ Signal     |
| **Repeater**  | 0–2 ports            | Small plug-in form, "range extender" | L1        | ❌ None       |
| **Access Pt** | 1 PoE uplink         | Ceiling disc, no visible antennas    | L2        | ✅ WiFi mgmt  |
| **Bridge**    | 2 ports (or virtual) | Connects 2 segments, often software  | L2        | ✅ MAC table  |
| **Firewall**  | WAN/LAN/DMZ ports    | Labeled zones, rack appliance        | L3–L7     | ✅✅ Inspect  |
| **Load Bal.** | Many uplinks         | Enterprise rack, VIP config          | L4/L7     | ✅✅ Distrib  |
| **NIC**       | 1 RJ45 per card      | Internal card or USB adapter         | L1–L2     | ✅ Per device |

---

## Device 1 — Hub (Layer 1)

```
📦 PHYSICAL LOOK
┌─────────────────────────────────────────┐
│ ┌───────────────────────────────────┐   │
│ │  HUB  ● ● ● ● ● ● ● ● [PWR●]    │   │
│ │       1 2 3 4 5 6 7 8            │   │
│ └───────────────────────────────────┘   │
└─────────────────────────────────────────┘
  - Older, cheap, small box
  - All ports identical RJ45
  - Single shared activity LED
  - No console/management port
  - No labeling on ports
```

```
📡 HOW IT WORKS
         PC1        PC2        PC3
          │          │          │
     ─────┴──────────┴──────────┴─────
                   HUB
     ─────┬──────────┬──────────┬─────
          │          │          │
         PC4        PC5        PC6

  PC1 sends data → ALL ports receive it
  ⚠️  = Collision domain (CSMA/CD needed)
  ⚠️  Half-duplex only
  ⚠️  All see all traffic (security risk)
```

**Identify from CLI:**

```bash
# Run tcpdump on your machine
sudo tcpdump -i eth0 -nn

# If you see traffic NOT destined for your MAC address
# (other machines' unicast traffic) → you're on a HUB!
# On a switch, you'd only see broadcast + your own traffic
```

**Key Facts:**

- No MAC learning. No intelligence.
- Broadcasts to **all** ports always
- Obsolete — replaced by switches
- Creates **1 collision domain** for all ports
- Still asked in **networking interviews**

---

## Device 2 — Switch (Layer 2)

```
📦 PHYSICAL LOOK
┌──────────────────────────────────────────────────────┐
│  CISCO / HP / Netgear SWITCH                         │
│ ┌────────────────────────────────────────────────┐   │
│ │ [●][●][●][●][●][●][●][●][●][●][●][●] [SFP▣▣] │   │
│ │  1  2  3  4  5  6  7  8  9 10 11 12           │   │
│ │              [CONSOLE] [MGMT-ETH]              │   │
│ └────────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────┘
  - 8 / 16 / 24 / 48 RJ45 ports
  - Per-port LEDs (green=link, amber=activity)
  - SFP slots for fiber uplinks
  - Console/serial port (managed switches)
  - Management ethernet port (managed)
  - Metal chassis, rack ears
```

```
📡 HOW IT WORKS

         PC1        PC2        PC3
          │          │          │
         [1]        [2]        [3]
    ┌─────────────────────────────┐
    │    S W I T C H              │
    │  MAC Table:                 │
    │  aa:bb:cc:11 → Port 1       │
    │  aa:bb:cc:22 → Port 2       │
    │  aa:bb:cc:33 → Port 3       │
    └─────────────────────────────┘

  PC1 → PC3: Frame goes ONLY to Port 3 ✅
  Each port = its own collision domain ✅
  Full-duplex supported ✅
```

**Identify from CLI:**

```bash
# Traffic isolation - you only see your traffic
sudo tcpdump -i eth0 -nn
# (Only broadcast + unicast TO you = switch ✅)

# On a Linux software switch (bridge):
bridge fdb show           # MAC table
ip link show type bridge  # Show bridges

# On Cisco switch (if you have access):
# show mac address-table
# show interfaces status
```

**Managed vs Unmanaged:**

```
Managed Switch:
  - Has IP address
  - Supports VLANs
  - Supports STP (Spanning Tree)
  - Has console port
  - SSH/Web management
  - Examples: Cisco Catalyst, HP Aruba

Unmanaged Switch:
  - No IP, no config
  - Plug and play
  - No VLANs
  - Home/small office use
  - Examples: TP-Link TL-SG108, Netgear GS308
```

---

## Device 3 — Router (Layer 3)

```
📦 PHYSICAL LOOK — HOME ROUTER
┌─────────────────────────────────────────────┐
│    )))   )))   )))  ← WiFi antennas         │
│  ┌──────────────────────────────────────┐   │
│  │ [WAN●] [LAN1] [LAN2] [LAN3] [LAN4]  │   │
│  │ (blue)  (yellow ports)               │   │
│  │ [USB] [RESET] [WPS]                  │   │
│  └──────────────────────────────────────┘   │
└─────────────────────────────────────────────┘
  - WAN port = different color (usually blue/yellow)
  - WAN port labeled "Internet" or "WAN"
  - LAN ports = same color, usually 4
  - WiFi antennas (1–4)
  - USB port for storage/4G dongle

📦 ENTERPRISE ROUTER
  - No antennas
  - Many labeled interfaces: G0/0, G0/1
  - Rack mounted
  - Console/AUX port
  - Cisco ISR / Juniper / MikroTik
```

```
📡 HOW IT WORKS

    Internet (Public IP)
         │
       [WAN] ← eth0: 203.0.113.1
    ┌────────────────────────────────┐
    │         R O U T E R            │
    │  Routing Table:                │
    │  0.0.0.0/0  → WAN (default)   │
    │  192.168.1.0/24 → LAN         │
    │  10.0.0.0/8  → VPN            │
    └────────────────────────────────┘
       [LAN] ← eth1: 192.168.1.1
         │
    ─────┴─────────────────────
         │          │
        PC1        PC2
    192.168.1.x  192.168.1.y

  Router connects DIFFERENT subnets
  Router separates broadcast domains
```

**Identify from CLI:**

```bash
# Find your router (default gateway)
ip route show default
# → default via 192.168.1.1 dev eth0

# If YOU are the router, check multiple interfaces
ip addr
# → eth0: 203.x.x.x (WAN)  eth1: 192.168.1.1 (LAN)

# Router enables IP forwarding:
cat /proc/sys/net/ipv4/ip_forward
# → 1 means this machine is routing/forwarding

# Check routing table:
ip route
```

---

## Device 4 — Modem (Layer 1–2)

```
📦 PHYSICAL LOOK

CABLE MODEM:                  DSL MODEM:
┌──────────────────┐          ┌──────────────────┐
│  ISP LOGO        │          │  ISP LOGO        │
│  [Coax ⊗] [RJ45]│          │  [Phone▬][RJ45]  │
│  (Screw port)    │          │  (RJ11 port)     │
│  [PWR] [DS][US]  │          │  [PWR][DSL][ETH] │
│  [Online][ETH]   │          │                  │
└──────────────────┘          └──────────────────┘

FIBER ONT (Optical Network Terminal):
┌──────────────────────────────┐
│  ISP LOGO                    │
│  [Fiber-SC/LC] [RJ45×1-4]   │
│  [PON●][LAN●][POWER●]        │
└──────────────────────────────┘

Key identifiers:
  - Coaxial port (round, threaded) = cable modem
  - RJ11 phone port = DSL modem
  - Fiber optic port = ONT/ONU
  - ISP branding always present
  - Usually only 1 ethernet output
  - DS/US LEDs (downstream/upstream)
```

```
📡 HOW IT WORKS

ISP Network
    │
    │ Coax / Phone line / Fiber  ← analog/RF signal
    │
┌───┴──────────────────────────┐
│          M O D E M           │
│  Signal → Digital (demod)    │
│  Digital → Signal (mod)      │
│  Assigns PUBLIC IP to WAN    │
└───────────────────┬──────────┘
                    │ Ethernet (digital)
               [Router WAN port]
               OR
               [Your PC directly]
```

**Identify from CLI:**

```bash
# If modem gives you a public IP directly:
ip addr show eth0
# → inet 203.0.113.x (public IP = you're behind modem only)

# If you're behind router → you get 192.168.x.x

# Modem management page is usually at:
# http://192.168.100.1  (cable modems - DOCSIS)
curl -I http://192.168.100.1
```

**Modem vs Gateway (Combo):**

```
Pure Modem:     Coax/DSL → 1 Ethernet out  (ISP provides this)
Gateway/Combo:  Modem + Router + WiFi in one box (common in homes)
                Usually has multiple LAN ports + WiFi
```

---

## Device 5 — Repeater / WiFi Extender (Layer 1)

```
📦 PHYSICAL LOOK
┌─────────────────┐
│  TP-Link RE200  │  ← Plugs directly into wall outlet
│  [WPS●][ETH●]  │
│  2 prongs       │
└────────┬────────┘
         │ Plugs into wall
         ⚡

  - Small, plug-in form factor
  - Labeled "Range Extender" or "WiFi Booster"
  - 0–2 ethernet ports
  - LED shows signal strength
  - Brands: TP-Link RE series, Netgear EX series
  - Setup via WPS button or web page
```

```
📡 HOW IT WORKS

Router )))WiFi((( → Repeater )))WiFi((( → Far Devices
(strong signal)    (receives +          (extended range)
                   retransmits)

  ⚠️ Bandwidth is HALVED:
  - Repeater receives on same channel it retransmits
  - 300 Mbps router → ~150 Mbps through repeater

  ✅ Seamless roaming: Same SSID as router
  ✅ No cabling needed

Powerline Adapter (similar concept):
Router → [Adapter] ──electrical wiring──  [Adapter] → PC
         plug in wall                      plug in wall
```

**Identify from CLI:**

```bash
# Connected device appears as same subnet as router
ip addr
# → 192.168.1.x (same network, different IP)

# No routing entry differences visible from client
# But signal may be weaker/slower through repeater
```

---

## Device 6 — Access Point / AP (Layer 2)

```
📦 PHYSICAL LOOK
         ┌──────────────────┐
         │  ○  Ubiquiti AP  │  ← Ceiling mounted
         │  UniFi U6-Pro    │
         │  (flat disc)     │
         │  LED ring ◯      │
         └────────┬─────────┘
                  │ PoE Ethernet cable (power + data)
             [Network Switch]

  - Round/square flat disc shape
  - Ceiling or wall mounted
  - Internal antennas (no visible antennas)
  - Single RJ45 port (PoE powered)
  - LED ring shows status (white/blue/red)
  - Brands: Ubiquiti UniFi, Cisco Meraki, Aruba
  - No power brick (PoE from switch)
```

```
📡 HOW IT WORKS

      Management Controller
              │
     ┌────────┴─────────────────────────────┐
     │  Network Switch (PoE)                │
     └───┬──────────────┬───────────────────┘
         │              │
    [AP #1]          [AP #2]          ← Each covers zone
    )))WiFi(((       )))WiFi(((
    /      \         /      \
 [Laptop] [Phone] [PC]  [Tablet]

  All APs = same SSID = seamless roaming
  Each AP = L2 device (no routing)
  Power from PoE = no separate power cable
```

**AP vs Repeater — Key Difference:**

```
┌──────────────────────────────────────────────────────────────┐
│ Feature         │ Access Point (AP)   │ Repeater/Extender     │
├──────────────────────────────────────────────────────────────┤
│ Connection      │ Ethernet (PoE)      │ WiFi (wireless)       │
│ Bandwidth       │ Full speed          │ Halved (~50%)         │
│ Placement       │ Ceiling mount       │ Wall outlet           │
│ Management      │ Controller/app      │ Web UI / WPS          │
│ Grade           │ Enterprise          │ Home/consumer         │
│ Latency         │ Low                 │ Higher                │
└──────────────────────────────────────────────────────────────┘
```

**Identify from CLI:**

```bash
# Check WiFi connection info
iwconfig wlan0         # (older)
iw wlan0 link          # (newer)

# See which AP (BSS) you're connected to
iw wlan0 info
# → "connected to aa:bb:cc:dd:ee:ff" ← AP's MAC

# Scan for nearby APs
sudo iw wlan0 scan | grep -E "SSID|signal|BSS"

# Check if device is PoE powered
# (Can't see from software, but IP comes from switch not router directly)
```

---

## Device 7 — Bridge (Layer 2)

```
📦 PHYSICAL (mostly software today)

Physical bridge (rare):
  [Network A] ──── [Bridge] ──── [Network B]
    Switch                         Switch

Software bridge (Linux - very common):
  eth0 ──── [br0 bridge] ──── eth1
              │
         (virtual IPs attached here)
              │
         VM/Container
```

```
📡 HOW IT WORKS

Docker uses Linux bridge (docker0):

  [Container 1]    [Container 2]
  veth0a           veth0b
     │               │
     └───────┬───────┘
          [docker0]        ← Linux bridge
             │
          [eth0]           ← Host physical NIC
             │
         [Network]
```

**Identify from CLI:**

```bash
# Show all bridges on system
ip link show type bridge
# → docker0, br-xxx, virbr0 etc.

# Show bridge details
bridge link show

# Show MAC table (bridge = software switch)
bridge fdb show

# Docker's bridge:
ip addr show docker0
# → 172.17.0.1/16

# List docker networks:
docker network ls
docker network inspect bridge
```

---

## Device 8 — Firewall (Layer 3–7)

```
📦 PHYSICAL LOOK — HARDWARE FIREWALL
┌──────────────────────────────────────────────────────────┐
│  Palo Alto / Fortinet / Cisco ASA                        │
│ ┌──────────────────────────────────────────────────────┐ │
│ │ [WAN●][LAN1●][LAN2●][DMZ●][MGMT●] [CONSOLE]        │ │
│ │  (labeled zones)              [STATUS LEDs]          │ │
│ └──────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────┘

  - Rack-mounted appliance
  - Ports explicitly labeled: WAN / LAN / DMZ / MGMT
  - Console (serial) port
  - Dedicated management port (out-of-band)
  - Brands: Palo Alto PA, Fortinet FortiGate,
            Cisco ASA/Firepower, pfSense (software)

Software Firewalls:
  - iptables / nftables (Linux)
  - UFW (Ubuntu)
  - Windows Defender Firewall
  - pfSense / OPNsense (open source)
```

```
📡 HOW IT WORKS

Internet
    │
  [WAN]
    │
┌───┴──────────────────────────────────────────┐
│              F I R E W A L L                 │
│                                              │
│  Rule 1: ALLOW TCP 443 from any → LAN ✅    │
│  Rule 2: DENY  TCP 23  from any → any  ❌   │
│  Rule 3: ALLOW ESTABLISHED connections ✅   │
│  Rule 4: DENY  all others ❌               │
│                                              │
│  [WAN]    [LAN]    [DMZ]     [MGMT]         │
└────────────┬─────────┬────────────────────── ┘
             │         │
           [LAN]     [DMZ]
           office    web servers
```

**Identify from CLI:**

```bash
# Linux iptables (stateful firewall):
sudo iptables -L -n -v

# Check NAT rules:
sudo iptables -t nat -L -n -v

# nftables (modern replacement):
sudo nft list ruleset

# UFW (Ubuntu firewall):
sudo ufw status verbose

# Connection tracking:
sudo conntrack -L 2>/dev/null

# Check if machine is doing IP forwarding (firewall/router):
cat /proc/sys/net/ipv4/ip_forward
# → 1 means yes, it's routing/firewalling
```

---

## Device 9 — Load Balancer (Layer 4 / Layer 7)

```
📦 PHYSICAL LOOK
  - Enterprise rack-mounted device
  - Brands: F5 BIG-IP, Citrix NetScaler
  - Software: HAProxy, Nginx, AWS ALB/NLB, Kubernetes Service

📡 HOW IT WORKS

Clients → single VIP (Virtual IP)
              │
    ┌─────────┴─────────────────────────┐
    │       L O A D  B A L A N C E R   │
    │                                   │
    │  Algorithm:                       │
    │  - Round Robin (rotate)           │
    │  - Least Connections (min active) │
    │  - IP Hash (sticky sessions)      │
    │  - Weighted (server capacity)     │
    └────────┬──────────┬──────────┬────┘
             │          │          │
        [Server1]  [Server2]  [Server3]
        10.0.0.11  10.0.0.12  10.0.0.13

L4 LB → Balances TCP/UDP connections (fast)
L7 LB → Reads HTTP headers, routes by URL/cookie
```

**Identify from CLI:**

```bash
# HAProxy status:
systemctl status haproxy
haproxy -vv

# Nginx as LB:
cat /etc/nginx/nginx.conf | grep -A5 upstream

# Kubernetes Service = L4 load balancer:
kubectl get services
kubectl describe service my-app

# Check listening VIPs:
ss -tlnp | grep :80
ip addr | grep -i vip
```

---

## Device 10 — NIC (Network Interface Card)

```
📦 PHYSICAL LOOK

PCIe NIC (Desktop):            USB-to-Ethernet Adapter:
┌──────────────────────┐       ┌───────────────┐
│  [RJ45][RJ45]        │       │  USB ──▶ RJ45 │
│  Intel/Broadcom      │       │  (travel use) │
│  [PCIe connector▬▬] │       └───────────────┘
└──────────────────────┘

Wireless NIC (PCIe):
┌──────────────────────┐
│  WiFi Card           │
│  [Antenna conn. ×2]  │
│  [PCIe connector▬▬] │
└──────────────────────┘
```

**Identify from CLI:**

```bash
# List all network interfaces:
ip link show

# Detailed NIC info:
ethtool eth0           # speed, duplex, link status

# NIC driver:
ethtool -i eth0        # driver name + version

# NIC speed:
ethtool eth0 | grep Speed

# PCI NICs:
lspci | grep -i ethernet
lspci | grep -i network

# USB NICs:
lsusb | grep -i ethernet

# All NICs with stats:
ip -s link show

# Physical vs virtual NICs:
ip link show | grep -E "(eth|ens|enp|wlan|wlp|lo|veth|br|docker|virbr)"
```

---

## Device 11 — Gateway

```
📦 CONCEPT (not a physical device type)

Gateway = Exit door from your local network
        = Usually your Router's LAN IP

Your PC              Gateway (Router)         Internet
192.168.1.100  →  192.168.1.1  →  203.0.113.x

Default Gateway = Where packets go when no better route exists
```

**Identify from CLI:**

```bash
# Show default gateway:
ip route show default
# → default via 192.168.1.1 dev eth0

# Full routing table:
ip route

# Test gateway is alive:
ping -c 3 $(ip route | grep default | awk '{print $3}')

# Gateway's MAC address:
ip neigh show | grep $(ip route | grep default | awk '{print $3}')

# On multiple routes:
ip route | grep default   # all default gateways
```

---

# PART 2 — Device Identification Guide

## 2.1 Physical Identification — What To Look For

```
STEP 1: Look at the PORTS

Coaxial port (round, threaded) = MODEM (cable)
Phone port (RJ11, tiny) = MODEM (DSL)
Fiber port (SC/LC optical) = ONT (fiber modem)
All RJ45, no management = SWITCH or HUB
1 RJ45 different color (WAN) = ROUTER
Ceiling disc, single RJ45 = ACCESS POINT
Labeled WAN/LAN/DMZ = FIREWALL
```

```
STEP 2: Look at the SIZE & FORM

Tiny plug-in device = REPEATER / WiFi Extender
Ceiling disc mount = ACCESS POINT
Small box (home) = HOME ROUTER or HOME SWITCH
1U/2U rack appliance = SWITCH, ROUTER, or FIREWALL
Large rack with labeled zones = ENTERPRISE FIREWALL or LB
```

```
STEP 3: Look at the ANTENNAS

External antennas = HOME ROUTER or OUTDOOR AP
No visible antennas + ceiling = ENTERPRISE AP (internal ant)
No antennas + many ports = SWITCH or ROUTER (enterprise)
```

```
STEP 4: Look at the LABELS

"WAN" / "Internet" port = ROUTER
"LAN 1-4" ports = SWITCH section of ROUTER
"DSL" / "Coax" label = MODEM
"PoE" label = SWITCH (powers APs)
"Console" port = MANAGED SWITCH / ROUTER / FIREWALL
"DMZ" port = FIREWALL
ISP logo on device = MODEM or GATEWAY combo
"Range Extender" / "Booster" = REPEATER
```

## 2.2 Decision Tree — What Device Is This?

```
START: You see a network device
             │
    ┌────────┴─────────────────────────────────────────┐
    │  Does it have a coaxial or phone line port?       │
    └─────────────────────────────────────────┬─────────┘
                    YES │                     │ NO
                        ▼                     ▼
                    ┌───────┐     ┌───────────────────────────┐
                    │ MODEM │     │ How many ethernet ports?  │
                    └───────┘     └─────────────┬─────────────┘
                                                │
                             ┌──────────────────┼───────────────────┐
                             │                  │                   │
                           0–1               2–5                8+ ports
                             │                  │                   │
                             ▼                  ▼                   ▼
                    ┌──────────────┐   ┌───────────────┐  ┌──────────────────┐
                    │  Small plug  │   │ 1 port = WAN? │  │ Per-port LEDs?   │
                    │  in device?  │   │ + LAN ports?  │  │ Rack mounted?    │
                    └──────┬───────┘   └───────┬───────┘  └────────┬─────────┘
                           │ YES               │ YES               │ YES
                           ▼                   ▼                   ▼
                      ┌──────────┐      ┌──────────┐      ┌──────────────┐
                      │ REPEATER │      │  ROUTER  │      │   SWITCH     │
                      └──────────┘      └──────────┘      └──────────────┘
                                                          (Managed if console port)

    Has antennas?          Ceiling mount?      Zones labeled?
        │                      │              (WAN/DMZ/LAN)?
        ▼                      ▼                    ▼
    WIFI ROUTER            ACCESS POINT         FIREWALL
```

## 2.3 CLI-Based Identification

```bash
# Run these on an UNKNOWN Linux machine to identify its role:

# ── What interfaces does it have? ──────────────────────
ip link show
# Many interfaces = switch/router/firewall

# ── Is it routing? ─────────────────────────────────────
cat /proc/sys/net/ipv4/ip_forward
# 1 = router / firewall

# ── Does it have firewall rules? ───────────────────────
sudo iptables -L -n 2>/dev/null | head -20
# Many rules = firewall

# ── Is it running DHCP? (router/gateway function) ──────
systemctl status isc-dhcp-server dnsmasq 2>/dev/null

# ── Does it bridge interfaces? ─────────────────────────
ip link show type bridge
bridge link show
# Has bridges = bridge/switch/docker host

# ── What services are listening? ───────────────────────
ss -tlnp
# :53 = DNS server, :67 = DHCP, :80/:443 = web/LB

# ── Is it a load balancer? ─────────────────────────────
systemctl status haproxy nginx 2>/dev/null
# Running = load balancer

# ── What's connected to it? ────────────────────────────
ip neigh
# ARP table shows nearby devices

# ── How many routes does it know? ──────────────────────
ip route | wc -l
# Many routes = router/firewall
```

---

# PART 3 — Linux Networking Concepts

## OSI Model (Full)

| Layer | Name         | Device         | Example              |
| ----- | ------------ | -------------- | -------------------- |
| L1    | Physical     | Hub, Repeater  | Cables, signals      |
| L2    | Data Link    | Switch, Bridge | MAC, ARP, Ethernet   |
| L3    | Network      | Router         | IP, ICMP, Routing    |
| L4    | Transport    | Firewall, LB   | TCP, UDP, Ports      |
| L5    | Session      | (mostly OS)    | NetBIOS, RPC         |
| L6    | Presentation | (mostly apps)  | SSL/TLS, encoding    |
| L7    | Application  | Proxy, App LB  | HTTP, DNS, SSH, SMTP |

**DevOps Shortcut to memorize:**

```
L1 = Physical (cable, signal)
L2 = Switch → MAC address
L3 = Router → IP address
L4 = Firewall / LB → Ports (TCP/UDP)
L7 = App proxy → HTTP / HTTPS content
```

---

## 1. MAC Address

**Concept:** Physical address burned into NIC. Used within local network.

```
Key Rules:
  ✅ MAC is used INSIDE local network only
  ✅ MAC changes at every router hop (L2 rewrite)
  ✅ IP address stays the same end-to-end
  ✅ Format: aa:bb:cc:dd:ee:ff (6 bytes hex)
  ✅ First 3 bytes = OUI (manufacturer ID)
```

```bash
# Show MAC addresses:
ip link show
ip addr

# Find MAC of specific interface:
ip link show eth0 | grep link/ether
cat /sys/class/net/eth0/address

# Identify manufacturer from MAC (OUI lookup):
# First 3 bytes: aa:bb:cc = manufacturer
```

---

## 2. ARP — Address Resolution Protocol

**Concept:** Maps IP address → MAC address. Works within LAN only.

```
How ARP works:
  PC wants to send to 192.168.1.50

  Step 1: PC broadcasts → "Who has 192.168.1.50? Tell 192.168.1.25"
          (Destination MAC = ff:ff:ff:ff:ff:ff)

  Step 2: 192.168.1.50 replies → "I have it! My MAC is aa:bb:cc..."

  Step 3: Sender caches the mapping (ARP cache/table)
```

```bash
# View ARP table (IP → MAC mappings):
ip neigh
ip neigh show

# Flush ARP cache:
sudo ip neigh flush all

# Add static ARP entry:
sudo ip neigh add 192.168.1.50 lladdr aa:bb:cc:dd:ee:ff dev eth0

# Watch ARP in real time:
sudo tcpdump -i eth0 arp -nn
```

**ARP Spoofing (Security):**

```
Attacker sends fake ARP replies:
"192.168.1.1 (gateway) is at MY_MAC"
→ All traffic to gateway now goes through attacker
→ Man-in-the-Middle attack
```

---

## 3. Switch (Concept Review)

```
Switch builds MAC table by LEARNING:
  Frame arrives on Port 3 from MAC aa:bb:cc
  → Switch records: aa:bb:cc = Port 3

  Frame destined to aa:bb:cc:
  → Switch looks up table → sends to Port 3 ONLY

  Unknown destination?
  → Switch FLOODS (sends to all ports except source)
  → This is called "unicast flooding"

  Broadcast (ff:ff:ff:ff:ff:ff)?
  → Always floods to all ports
```

---

## 4. Default Gateway

**Concept:** The exit door for packets leaving your local network.

```
Rule: If destination IP is NOT in my local subnet,
      send the packet to the default gateway.

Example:
  My IP:      192.168.1.100
  My subnet:  192.168.1.0/24
  Gateway:    192.168.1.1

  Sending to 8.8.8.8 → NOT in 192.168.1.0/24
  → Send to gateway 192.168.1.1
  → Gateway forwards it toward internet
```

```bash
# Show default gateway:
ip route
# → default via 192.168.1.1 dev eth0

# Add default gateway:
sudo ip route add default via 192.168.1.1

# Delete default gateway:
sudo ip route del default

# Ping gateway:
ping -c 3 192.168.1.1
```

---

## 5. IP Address

**Concept:** Logical address. Identifies device on a network.

```
IPv4: 32-bit → 4 octets
  192 . 168 . 1 . 100
  ─── ─── ─ ─ ─ ─ ───
  Network portion  │  Host portion (depends on subnet mask)

Private IP ranges (RFC 1918):
  10.0.0.0/8         (10.x.x.x)
  172.16.0.0/12      (172.16.x.x – 172.31.x.x)
  192.168.0.0/16     (192.168.x.x)

Special:
  127.0.0.1          Loopback (localhost)
  0.0.0.0            Represents "any" interface
  255.255.255.255    Broadcast
```

```bash
# View IPs:
ip addr
ip addr show eth0

# Add IP to interface:
sudo ip addr add 192.168.1.100/24 dev eth0

# Remove IP:
sudo ip addr del 192.168.1.100/24 dev eth0

# Show only IPv4:
ip -4 addr

# Show only IPv6:
ip -6 addr
```

---

## 6. Routing

**Concept:** Deciding where packets go based on destination IP.

```
Routing table entry:
  Destination   Gateway        Interface    Metric
  0.0.0.0/0     192.168.1.1   eth0         100    ← default route
  192.168.1.0/24 0.0.0.0      eth0         0      ← local network
  10.0.0.0/8    10.0.0.1      tun0         50     ← VPN route

Decision process (per packet):
  1. Check routing table for matching destination
  2. If multiple matches → use Longest Prefix Match
  3. If no match → use default route (0.0.0.0/0)
  4. If no default route → "Network unreachable"
```

```bash
# View routing table:
ip route
ip route show

# Add static route:
sudo ip route add 10.0.0.0/8 via 192.168.1.1

# Delete route:
sudo ip route del 10.0.0.0/8

# Test which route a packet will take:
ip route get 8.8.8.8

# Show routes with metrics:
ip route show metric
```

---

## 7. Longest Prefix Match (LPM)

**Concept:** Most specific route always wins.

```
Example routing table:
  10.0.0.0/8     → via 10.0.0.1      (broad)
  10.0.1.0/24    → via 10.0.1.1      (specific)
  10.0.1.128/25  → via 10.0.1.129   (very specific)

Packet destined to 10.0.1.200:
  Matches 10.0.0.0/8     → /8  (8 bits match)
  Matches 10.0.1.0/24    → /24 (24 bits match)
  Matches 10.0.1.128/25  → /25 (25 bits match)

  WINNER: 10.0.1.128/25 → most specific prefix wins!
```

```bash
# See which route will be used:
ip route get 10.0.1.200
# → 10.0.1.128/25 via 10.0.1.129 dev eth0
```

---

## 8. NAT — Network Address Translation

**Concept:** Converts private IP ↔ public IP.

```
Types of NAT:
  SNAT (Source NAT):    Change source IP
    → Used for outgoing internet traffic
    → Private IP → Public IP
    → Most common in home/office

  DNAT (Destination NAT): Change destination IP
    → Used for incoming traffic (port forwarding)
    → Public IP:port → Internal server IP:port

  MASQUERADE:
    → Like SNAT but IP is dynamic (DHCP public IP)
    → Used when public IP can change
```

```bash
# Enable SNAT / IP Masquerade (your machine as NAT router):
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
# Also need:
sudo sysctl -w net.ipv4.ip_forward=1

# Port forwarding (DNAT) - forward port 80 to internal server:
sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT \
  --to-destination 192.168.1.50:80

# View NAT table:
sudo iptables -t nat -L -n -v
```

---

## 9. DNS — Domain Name System

**Concept:** Translates domain names → IP addresses.

```
DNS Resolution flow:
  1. You type google.com
  2. Check /etc/hosts (local override)
  3. Check local DNS cache
  4. Query DNS resolver (your router or 8.8.8.8)
  5. Resolver queries Root servers → TLD → Authoritative
  6. Returns 142.250.x.x
  7. Browser connects to that IP

Record Types:
  A      = hostname → IPv4
  AAAA   = hostname → IPv6
  CNAME  = alias → hostname
  MX     = mail server
  NS     = nameserver
  PTR    = IP → hostname (reverse DNS)
  TXT    = text records (SPF, DKIM etc.)
  SRV    = service location
```

```bash
# Basic lookup:
dig google.com
dig google.com A         # IPv4 only
dig google.com AAAA      # IPv6
dig google.com MX        # Mail servers
dig -x 8.8.8.8           # Reverse DNS

# Short output:
dig +short google.com

# Query specific DNS server:
dig @8.8.8.8 google.com
dig @1.1.1.1 google.com  # Cloudflare

# Trace full resolution path:
dig +trace google.com

# nslookup (interactive):
nslookup google.com
nslookup 8.8.8.8         # Reverse lookup

# Check system DNS config:
resolvectl status
cat /etc/resolv.conf
cat /etc/hosts

# Test with custom /etc/hosts:
echo "127.0.0.1 myapp.local" | sudo tee -a /etc/hosts
```

---

## 10. TCP — Transmission Control Protocol

**Concept:** Reliable, ordered, connection-oriented communication.

```
TCP Features:
  ✅ Connection-oriented (3-way handshake)
  ✅ Ordered delivery (sequence numbers)
  ✅ Reliable (acknowledgements + retransmission)
  ✅ Flow control (window size)
  ✅ Congestion control (slow start, etc.)

TCP Segment Header fields:
  Source Port | Destination Port
  Sequence Number
  Acknowledgement Number
  Flags: SYN, ACK, FIN, RST, PSH, URG
  Window Size
  Checksum
```

---

## 11. UDP — User Datagram Protocol

**Concept:** Fast, connectionless, no reliability guarantees.

```
UDP Features:
  ❌ No connection (fire and forget)
  ❌ No ordering
  ❌ No retransmission
  ✅ Very fast (minimal overhead)
  ✅ Good for real-time data

Use cases:
  DNS     → Fast lookups (single request/reply)
  DHCP    → Network config (broadcast)
  VoIP    → Real-time voice (latency > reliability)
  Gaming  → Fast updates (can miss a frame)
  NTP     → Time sync
  SNMP    → Network monitoring
  Syslog  → Log forwarding
  Video streaming → live/HLS
```

---

## 12. TCP 3-Way Handshake

**Concept:** Establishes a TCP connection before data flows.

```
Client                          Server
  │                               │
  │─── SYN (seq=100) ────────────▶│   "I want to connect"
  │                               │
  │◀── SYN-ACK (seq=200,ack=101) ─│   "OK, I'm ready"
  │                               │
  │─── ACK (ack=201) ────────────▶│   "Got it, let's go"
  │                               │
  │═══════ DATA FLOWS ════════════│
  │                               │
  └── FIN ──────────────────────▶ │   4-way teardown
  │◀── ACK ──────────────────── ──│
  │◀── FIN ───────────────────────│
  └─── ACK ──────────────────────▶│
```

```bash
# Watch 3-way handshake in real time:
sudo tcpdump -i eth0 -nn 'tcp[tcpflags] & (tcp-syn|tcp-ack|tcp-fin) != 0'

# Or filter specific port:
sudo tcpdump -i any -nn port 80 and host google.com
```

---

## 13. TCP States

```
State         Meaning
─────────────────────────────────────────────────────────
LISTEN        Server waiting for incoming connections
SYN-SENT      Client sent SYN, waiting for SYN-ACK
SYN-RECV      Server got SYN, sent SYN-ACK, waiting ACK
ESTABLISHED   Active connection (data flowing)
FIN-WAIT-1    Sent FIN, waiting for ACK
FIN-WAIT-2    Got ACK for FIN, waiting for server's FIN
CLOSE-WAIT    Got FIN from peer, waiting for app to close
LAST-ACK      Sent FIN, waiting for final ACK
TIME-WAIT     Connection closed, waiting to ensure delivery
CLOSED        No connection
```

```bash
# View TCP connections with states:
ss -tan                    # all TCP + state + no DNS
ss -tanp                   # + process info
ss -tan state ESTABLISHED  # only established
ss -tan state LISTEN       # only listening

# Count connections by state:
ss -tan | awk '{print $1}' | sort | uniq -c | sort -rn

# Legacy:
netstat -an | grep tcp

# Monitor live:
watch -n 1 'ss -tan | awk "{print \$1}" | sort | uniq -c'
```

**Troubleshooting with States:**

```
Too many TIME-WAIT  → Normal after many short connections (HTTP)
Too many CLOSE-WAIT → Application bug (not closing sockets)
Too many SYN-RECV   → Possible SYN flood / DDoS
Many ESTABLISHED    → Active connections (normal)
```

---

## 14. Ports

```
Well-Known Ports (0–1023):
  Port  Protocol  Service
  ────────────────────────────────
  21    TCP       FTP
  22    TCP       SSH
  23    TCP       Telnet (insecure!)
  25    TCP       SMTP (email send)
  53    TCP/UDP   DNS
  67    UDP       DHCP Server
  68    UDP       DHCP Client
  80    TCP       HTTP
  110   TCP       POP3 (email)
  143   TCP       IMAP (email)
  443   TCP       HTTPS
  465   TCP       SMTPS (secure email)
  993   TCP       IMAPS
  995   TCP       POP3S

Registered Ports (1024–49151):
  3306  TCP       MySQL
  5432  TCP       PostgreSQL
  6379  TCP       Redis
  9200  TCP       Elasticsearch
  27017 TCP       MongoDB
  8080  TCP       HTTP alternate / Tomcat
  8443  TCP       HTTPS alternate
  9090  TCP       Prometheus
  3000  TCP       Grafana / Dev servers
  2376  TCP       Docker TLS
  2377  TCP       Docker Swarm
  10250 TCP       Kubernetes kubelet
  6443  TCP       Kubernetes API server
  2379  TCP       etcd client
  2380  TCP       etcd peer

Ephemeral Ports (49152–65535):
  Used by OS as source ports for outgoing connections
```

```bash
# Check what's listening on a port:
ss -tlnp | grep :80
sudo lsof -i :80

# List all listening ports:
ss -tlnp

# Check if specific port is open on remote host:
nc -zv 192.168.1.50 22
nc -zv google.com 443

# Scan ports:
nmap -p 1-1000 localhost
nmap -p 22,80,443 192.168.1.50
```

---

## 15. Ephemeral Ports

**Concept:** Temporary ports assigned by OS for outgoing connections.

```
Your PC                         Server
192.168.1.25:54312   ───────▶  8.8.8.8:443
                                        │
54312 = ephemeral (temporary, auto-assigned)
443   = well-known server port

Range: 49152–65535 (Linux default: 32768–60999)

Each outgoing connection = unique (IP:port, IP:port) 4-tuple
```

```bash
# Check ephemeral port range:
cat /proc/sys/net/ipv4/ip_local_port_range
# → 32768   60999

# See ephemeral ports in use:
ss -tan | grep ESTABLISHED

# Change range if needed (e.g., high-traffic servers):
sudo sysctl -w net.ipv4.ip_local_port_range="1024 65535"
```

---

## 16. Socket Inspection with ss

```bash
# ─── Common ss Commands ─────────────────────────────────

ss -tlnp      # TCP, listening, numeric, with process
ss -tulnp     # TCP + UDP, listening
ss -tan       # TCP, all states, numeric
ss -tanp      # TCP, all states, with process
ss -s         # Summary statistics

# ─── Filter by State ────────────────────────────────────
ss -tan state established
ss -tan state listen
ss -tan state time-wait
ss -tan state close-wait

# ─── Filter by Port ─────────────────────────────────────
ss -tan dport = :443    # destination port 443
ss -tan sport = :80     # source port 80

# ─── Filter by Address ──────────────────────────────────
ss -tan dst 8.8.8.8     # connections to 8.8.8.8
ss -tan src 192.168.1.0/24

# ─── Show socket memory ─────────────────────────────────
ss -tm

# ─── Count established connections ──────────────────────
ss -tan state established | wc -l
```

---

## 17. tcpdump — Packet Capture

```bash
# ─── Basic Usage ────────────────────────────────────────
sudo tcpdump -i any           # all interfaces
sudo tcpdump -i eth0          # specific interface
sudo tcpdump -nn              # no hostname/port resolution (faster)
sudo tcpdump -v               # verbose
sudo tcpdump -vvv             # very verbose

# ─── Filters ────────────────────────────────────────────
sudo tcpdump -nn port 80             # HTTP traffic
sudo tcpdump -nn port 443            # HTTPS traffic
sudo tcpdump -nn host 8.8.8.8        # to/from specific host
sudo tcpdump -nn src host 192.168.1.5 # from specific IP
sudo tcpdump -nn dst host 8.8.8.8    # to specific IP
sudo tcpdump -nn net 192.168.1.0/24  # subnet traffic

# ─── Protocol Filters ───────────────────────────────────
sudo tcpdump -nn tcp
sudo tcpdump -nn udp
sudo tcpdump -nn icmp
sudo tcpdump -nn arp

# ─── Combine Filters ────────────────────────────────────
sudo tcpdump -nn 'port 80 or port 443'
sudo tcpdump -nn 'host 8.8.8.8 and port 53'
sudo tcpdump -nn 'not port 22'   # exclude SSH (useful!)

# ─── Save & Read Captures ───────────────────────────────
sudo tcpdump -nn -w capture.pcap           # save
sudo tcpdump -nn -r capture.pcap           # read back
sudo tcpdump -nn -r capture.pcap port 80   # filter pcap

# ─── Display Packet Contents ────────────────────────────
sudo tcpdump -nn -A port 80    # ASCII content
sudo tcpdump -nn -X port 80    # hex + ASCII

# ─── Watch Handshake ────────────────────────────────────
sudo tcpdump -nn 'tcp[tcpflags] & (tcp-syn|tcp-ack) != 0'
```

---

## 18. curl — HTTP Testing

```bash
# ─── Basic Requests ─────────────────────────────────────
curl http://localhost
curl https://google.com
curl http://192.168.1.50:8080/api/health

# ─── Show Headers ───────────────────────────────────────
curl -I https://google.com         # HEAD request (headers only)
curl -v https://google.com         # verbose (full request/response)
curl -D - https://google.com       # dump headers to stdout

# ─── HTTP Methods ───────────────────────────────────────
curl -X GET    http://api.example.com/users
curl -X POST   http://api.example.com/users \
     -H "Content-Type: application/json" \
     -d '{"name":"Harshith"}'
curl -X PUT    http://api.example.com/users/1 -d '{...}'
curl -X DELETE http://api.example.com/users/1

# ─── Auth ───────────────────────────────────────────────
curl -u admin:password https://example.com
curl -H "Authorization: Bearer TOKEN" https://api.example.com

# ─── SSL/TLS ────────────────────────────────────────────
curl -k https://self-signed.example.com   # skip cert verify
curl --cacert /path/to/ca.crt https://...

# ─── Timeouts ───────────────────────────────────────────
curl --connect-timeout 5 --max-time 10 https://example.com

# ─── Follow Redirects ───────────────────────────────────
curl -L http://google.com

# ─── Download ───────────────────────────────────────────
curl -O https://example.com/file.tar.gz    # save with original name
curl -o myfile.tar.gz https://...          # save with custom name
```

---

## 19. Netcat (nc) — Raw TCP/UDP Testing

```bash
# ─── Port Checking ──────────────────────────────────────
nc -zv localhost 80          # TCP port check
nc -zv 192.168.1.50 22      # SSH port check
nc -zvu localhost 53         # UDP port check
nc -zv google.com 443        # Remote port check

# ─── Simple Server ──────────────────────────────────────
nc -l 9999                   # Listen on port 9999
nc -lp 9999                  # (some systems)
nc -lu 9999                  # UDP listener

# ─── Connect to Server ──────────────────────────────────
nc localhost 9999            # Connect to listener
nc 192.168.1.50 9999        # Remote connect

# ─── File Transfer with nc ──────────────────────────────
# Receiver:
nc -l 9999 > received_file.txt

# Sender:
cat myfile.txt | nc 192.168.1.50 9999

# ─── Banner Grabbing (service identification) ────────────
nc -v 192.168.1.50 22       # SSH banner
nc -v 192.168.1.50 25       # SMTP banner
echo "QUIT" | nc -v 192.168.1.50 25

# ─── HTTP request manually ──────────────────────────────
echo -e "GET / HTTP/1.0\r\nHost: example.com\r\n\r\n" | nc example.com 80
```

---

## 20. nmap — Port Scanner

```bash
# ─── Basic Scans ────────────────────────────────────────
nmap localhost                      # Common ports
nmap 192.168.1.50                   # Remote host
nmap 192.168.1.0/24                 # Entire subnet

# ─── Port Range ─────────────────────────────────────────
nmap -p 80 localhost                # Single port
nmap -p 22,80,443 localhost         # Multiple ports
nmap -p 1-1024 localhost            # Port range
nmap -p- localhost                  # All 65535 ports

# ─── Scan Types ─────────────────────────────────────────
nmap -sS localhost         # SYN scan (stealth, fast)
nmap -sT localhost         # TCP connect scan
nmap -sU localhost         # UDP scan (slow)
nmap -sn 192.168.1.0/24   # Ping scan (host discovery only)

# ─── Service/Version Detection ──────────────────────────
nmap -sV localhost         # Service version detection
nmap -O localhost          # OS detection (sudo needed)
nmap -A localhost          # Aggressive (OS + version + scripts)

# ─── Output ─────────────────────────────────────────────
nmap -oN output.txt localhost   # Normal output
nmap -oG output.gnmap localhost # Grepable output
nmap -oX output.xml localhost   # XML output
```

---

## 21. lsof — Find Process Using Port

```bash
# Find what's using port 80:
sudo lsof -i :80

# Find what's using port 443:
sudo lsof -i :443

# All network connections:
sudo lsof -i

# Specific protocol:
sudo lsof -i tcp
sudo lsof -i udp

# Specific process:
sudo lsof -i -p 1234    # by PID

# Combined: port + protocol:
sudo lsof -i TCP:443
sudo lsof -i UDP:53

# Alternative (usually faster):
ss -tlnp | grep :80     # shows PID too
fuser 80/tcp            # simple output
```

---

## 22. mtr — My Traceroute

```bash
# Basic mtr:
mtr google.com

# Non-interactive (good for scripts):
mtr --report google.com
mtr --report --report-cycles 20 google.com

# Numeric only (no DNS):
mtr -n google.com
mtr --no-dns google.com

# TCP mode (bypasses ICMP blocking):
sudo mtr --tcp --port 443 google.com

# UDP mode:
sudo mtr --udp google.com

# Reading mtr output:
# Loss% = packet loss at each hop
# Avg   = average RTT
# StDev = jitter

# If a hop shows 100% loss but later hops are fine:
# → That hop blocks ICMP but traffic still flows (normal!)
```

---

## 23. iperf3 — Bandwidth Testing

```bash
# ─── Setup ──────────────────────────────────────────────
# On SERVER:
iperf3 -s              # Listen on port 5201
iperf3 -s -p 9999     # Custom port

# On CLIENT:
iperf3 -c SERVER_IP
iperf3 -c 192.168.1.50

# ─── Options ────────────────────────────────────────────
iperf3 -c 192.168.1.50 -t 30    # Test for 30 seconds
iperf3 -c 192.168.1.50 -P 4     # 4 parallel streams
iperf3 -c 192.168.1.50 -R       # Reverse (server→client)
iperf3 -c 192.168.1.50 -u       # UDP mode
iperf3 -c 192.168.1.50 -u -b 100M  # UDP with 100 Mbps target

# ─── Interpreting Results ───────────────────────────────
# Interval  = time window
# Transfer  = bytes transferred
# Bandwidth = throughput (what you care about)
# Retr      = TCP retransmissions (high = network issues)
```

---

## 24. UFW — Ubuntu Firewall

```bash
# ─── Status ─────────────────────────────────────────────
sudo ufw status
sudo ufw status verbose
sudo ufw status numbered

# ─── Enable / Disable ───────────────────────────────────
sudo ufw enable
sudo ufw disable
sudo ufw reset           # reset all rules

# ─── Allow Rules ────────────────────────────────────────
sudo ufw allow 22              # SSH (TCP)
sudo ufw allow 80              # HTTP
sudo ufw allow 443             # HTTPS
sudo ufw allow 22/tcp          # Specific protocol
sudo ufw allow from 192.168.1.0/24   # From subnet
sudo ufw allow from 10.0.0.1 to any port 22  # From IP to SSH

# ─── Deny Rules ─────────────────────────────────────────
sudo ufw deny 23               # Block Telnet
sudo ufw deny from 10.0.0.0/8 # Block subnet

# ─── Delete Rules ───────────────────────────────────────
sudo ufw status numbered
sudo ufw delete 3              # Delete rule #3
sudo ufw delete allow 80       # Delete by rule

# ─── Application Profiles ───────────────────────────────
sudo ufw app list
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'

# ─── Default Policies ───────────────────────────────────
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

---

## 25. iptables — Linux Firewall Engine

```
iptables structure:
  Tables:   filter (default), nat, mangle, raw
  Chains:   INPUT, OUTPUT, FORWARD (+ custom)
  Rules:    match conditions + action (ACCEPT/DROP/REJECT/LOG)

Packet flow:
  Incoming: → PREROUTING → INPUT → (process)
  Forward:  → PREROUTING → FORWARD → POSTROUTING
  Outgoing: → (process) → OUTPUT → POSTROUTING
```

```bash
# ─── View Rules ─────────────────────────────────────────
sudo iptables -L -n -v            # filter table
sudo iptables -t nat -L -n -v     # NAT table
sudo iptables -t mangle -L -n -v  # mangle table

# ─── Add Rules ──────────────────────────────────────────
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT   # Allow SSH
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT   # Allow HTTP
sudo iptables -A INPUT -j DROP                       # Drop everything else

# ─── Insert at top ──────────────────────────────────────
sudo iptables -I INPUT 1 -p tcp --dport 22 -j ACCEPT

# ─── Delete Rules ───────────────────────────────────────
sudo iptables -D INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -F             # Flush all rules (dangerous!)

# ─── NAT Examples ───────────────────────────────────────
# Masquerade (SNAT):
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

# Port Forwarding (DNAT):
sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT \
  --to-destination 192.168.1.50:80

# ─── Allow Established Connections (stateful) ──────────
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# ─── Save Rules ─────────────────────────────────────────
sudo iptables-save > /etc/iptables/rules.v4
sudo iptables-restore < /etc/iptables/rules.v4
```

---

## 26. CIDR and Subnetting

```
CIDR notation: IP/prefix-length
  192.168.1.0/24 → 24 bits network, 8 bits host

Subnet Mask conversions:
  /24  = 255.255.255.0     → 256 IPs  (254 usable)
  /25  = 255.255.255.128   → 128 IPs  (126 usable)
  /26  = 255.255.255.192   → 64 IPs   (62 usable)
  /27  = 255.255.255.224   → 32 IPs   (30 usable)
  /28  = 255.255.255.240   → 16 IPs   (14 usable)
  /29  = 255.255.255.248   → 8 IPs    (6 usable)
  /30  = 255.255.255.252   → 4 IPs    (2 usable)
  /32  = 255.255.255.255   → 1 IP     (host route)

Formula:
  Hosts = 2^(32 - prefix) - 2
  Block Size = 256 - last octet of subnet mask
```

**Subnetting Quick Math:**

```
You have 192.168.1.0/24 and need 4 subnets of /26:

  /26 = 64 IPs each
  Subnet 1: 192.168.1.0   – 192.168.1.63    (GW: .1, BC: .63)
  Subnet 2: 192.168.1.64  – 192.168.1.127   (GW: .65, BC: .127)
  Subnet 3: 192.168.1.128 – 192.168.1.191   (GW: .129, BC: .191)
  Subnet 4: 192.168.1.192 – 192.168.1.255   (GW: .193, BC: .255)

Rule: Start + Block size = next subnet start
  0 + 64 = 64 → 64 + 64 = 128 → 128 + 64 = 192
```

```bash
# Calculate subnet info:
ipcalc 192.168.1.0/26
ipcalc 10.0.0.0/24

# Install if needed:
sudo apt install ipcalc -y

# Python quick calc:
python3 -c "import ipaddress; n=ipaddress.ip_network('192.168.1.0/26'); print(n.num_addresses, list(n.hosts())[:3])"
```

---

## 27. Network Namespaces

**Concept:** Isolated network stacks — foundation of containers and VMs.

```
Default namespace = your host network
Each container/pod gets its own namespace with:
  - Separate interfaces
  - Separate routing table
  - Separate iptables rules
  - Separate ARP table
```

```bash
# ─── List Namespaces ────────────────────────────────────
ip netns list
ip netns show

# ─── Create & Use ───────────────────────────────────────
sudo ip netns add ns1
sudo ip netns exec ns1 ip addr         # run cmd in ns1
sudo ip netns exec ns1 bash            # shell in ns1

# ─── Create veth pair (connect two namespaces) ──────────
# veth pairs = virtual ethernet cable between namespaces

sudo ip link add veth0 type veth peer name veth1
sudo ip link set veth1 netns ns1

# Configure:
sudo ip addr add 10.0.0.1/24 dev veth0
sudo ip netns exec ns1 ip addr add 10.0.0.2/24 dev veth1

# Bring up:
sudo ip link set veth0 up
sudo ip netns exec ns1 ip link set veth1 up

# Test connectivity:
ping 10.0.0.2                               # from host
sudo ip netns exec ns1 ping 10.0.0.1       # from ns1

# ─── Delete Namespace ───────────────────────────────────
sudo ip netns del ns1

# ─── Docker uses this internally ────────────────────────
# Each container = separate netns
# docker0 bridge connects them
docker inspect container_name | grep NetworkMode
```

---

## 28. Linux Bridge

**Concept:** Software switch. Connects network interfaces at L2.

```bash
# ─── Create Bridge ──────────────────────────────────────
sudo ip link add br0 type bridge
sudo ip link set br0 up

# ─── Add Interfaces to Bridge ───────────────────────────
sudo ip link set eth0 master br0     # eth0 joins br0
sudo ip link set veth1 master br0    # veth1 joins br0

# ─── Assign IP to Bridge ────────────────────────────────
sudo ip addr add 192.168.1.1/24 dev br0

# ─── Show Bridge Info ───────────────────────────────────
ip link show type bridge
bridge link show             # members of bridge
bridge fdb show              # MAC table
bridge vlan show             # VLAN config

# ─── Remove Interface from Bridge ───────────────────────
sudo ip link set eth0 nomaster

# ─── Delete Bridge ──────────────────────────────────────
sudo ip link del br0
```

---

## 29. veth Pairs

**Concept:** Virtual ethernet cable — one end in host, other in container/namespace.

```
Host namespace        Container namespace
    │                       │
  [veth0]  ←────────→  [veth1 / eth0]
  (host side)              (container side)

Both ends are peers — send on one = receive on other
```

```bash
# Create veth pair:
sudo ip link add veth0 type veth peer name veth1

# Move one end to container/namespace:
sudo ip link set veth1 netns container_ns

# Or connect to bridge:
sudo ip link set veth0 master docker0

# List veth pairs:
ip link show type veth

# Show peer of a veth:
ethtool -S veth0 | grep peer
ip link show veth0  # look for "link-netns" or check index
```

---

## 30. Docker Networking

```
Docker network types:
  bridge  = default. Container → docker0 bridge → host
  host    = container shares host network stack
  none    = no networking (isolated)
  overlay = multi-host (Swarm/Kubernetes)
  macvlan = container gets its own MAC on host network
```

```bash
# ─── Network Commands ───────────────────────────────────
docker network ls
docker network inspect bridge
docker network create mynet
docker run --network mynet nginx

# ─── Container Networking ───────────────────────────────
docker inspect container_name | python3 -m json.tool | grep -A 20 Networks
docker exec -it container_name ip addr
docker exec -it container_name ip route
docker exec -it container_name ss -tlnp

# ─── Port Mapping (DNAT internally) ─────────────────────
docker run -p 8080:80 nginx
# Host port 8080 → container port 80

# ─── Host Network (no isolation) ────────────────────────
docker run --network host nginx
# Container uses host's IP directly

# ─── Inspect docker0 bridge ─────────────────────────────
ip addr show docker0
ip link show type bridge
bridge fdb show
```

---

# PART 4 — Topics Still To Learn (Roadmap)

## Priority Queue

```
Phase 1 (Do Now):
  ✅ Linux Bridge        → Done in Part 3
  ✅ veth Pair           → Done in Part 3
  ✅ Docker Networking   → Done in Part 3
  □  nftables            → Modern iptables replacement
  □  VLANs               → VLAN tagging, 802.1Q

Phase 2 (Next):
  □  Kubernetes Networking  → Pod networking, CNI, kube-proxy
  □  AWS VPC Networking     → Subnets, route tables, SG, NACL
  □  MTU & Fragmentation    → Packet size limits

Phase 3 (Advanced):
  □  VPN Networking      → WireGuard, OpenVPN, IPsec
  □  Service Mesh        → Istio, Linkerd, mTLS
```

## nftables Quick Intro

```bash
# nftables = modern replacement for iptables
sudo nft list ruleset

# Basic rules:
sudo nft add table inet filter
sudo nft add chain inet filter input { type filter hook input priority 0\; }
sudo nft add rule inet filter input tcp dport 22 accept
sudo nft add rule inet filter input drop

# Flush:
sudo nft flush ruleset
```

## VLANs Quick Intro

```
VLAN = Virtual LAN. Separates broadcast domains on same switch.
Tagged frames carry VLAN ID (802.1Q tag, 4 bytes).

VLAN 10 = Engineering
VLAN 20 = HR
VLAN 30 = Guest WiFi
All on same physical switch but isolated!
```

```bash
# Create VLAN interface on Linux:
sudo ip link add link eth0 name eth0.10 type vlan id 10
sudo ip addr add 192.168.10.1/24 dev eth0.10
sudo ip link set eth0.10 up

# Show VLANs:
ip -d link show eth0.10
bridge vlan show
```

---

# Quick Reference Card

```
DEVICE CHEAT SHEET:
  Hub        → L1, broadcasts all, collision domain, obsolete
  Switch     → L2, MAC table, per-port collision, smart
  Router     → L3, IP routing, separates subnets & broadcasts
  Modem      → L1-L2, signal converter, ISP device
  Repeater   → L1, amplifies signal, halves WiFi bandwidth
  AP         → L2, WiFi, PoE powered, ceiling mount
  Bridge     → L2, connects segments, foundation of Docker/VM

SPOT IT:
  Coax port         = Modem
  WAN label         = Router
  Ceiling disc      = Access Point
  Rack + DMZ label  = Firewall
  Small plug-in     = Repeater
  48 ports + SFP    = Managed Switch

CLI TOOLKIT:
  ip addr           → IP addresses
  ip link           → Interfaces & MAC
  ip route          → Routing table
  ip neigh          → ARP table
  ss -tlnp          → Listening ports
  ss -tan           → All TCP + states
  tcpdump           → Packet capture
  dig               → DNS lookup
  curl -v           → HTTP debug
  nc -zv host port  → Port check
  mtr               → Traceroute+ping
  nmap              → Port scan
  bridge fdb show   → Bridge MAC table
  ip netns list     → Network namespaces
```

---

_Last updated: June 2026 | DevOps Networking Reference | Harshith_
