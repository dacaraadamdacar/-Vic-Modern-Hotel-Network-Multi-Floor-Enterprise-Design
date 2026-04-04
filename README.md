# 🏨 Vic Modern Hotel Network — Multi-Floor Enterprise Design
> Cisco Packet Tracer | OSPF | Inter-VLAN Routing | DHCP | SSH | Port Security

---

## 📋 Project Overview

A fully functional **Enterprise Hotel Network** designed and simulated in Cisco Packet Tracer. The network spans **3 floors** with **8 departments**, each in its own VLAN. Three Cisco 2911 routers handle inter-floor routing via Serial DCE links running **OSPF**, with each router acting as a **DHCP server** for its floor. SSH is configured on all routers for secure remote access, and port security is enforced on the IT department switch.

---

## 🏗️ Network Architecture

```
[F3-Router] ──── Serial 10.10.10.0/30 ──── [F2-Router]
     |                                           |
Serial 10.10.10.4/30                  Serial 10.10.10.8/30
     |                                           |
[F1-Router] ──────────────────────────────────── ┘
     |                    |                    |
[F1-Switch]          [F2-Switch]          [F3-Switch]
VLAN 60,70,80        VLAN 30,40,50        VLAN 10,20
```

---

## 🗂️ VLAN & IP Plan

### 🟦 Floor 1 — F1-Router

| Department | VLAN | Network | Gateway |
|---|---|---|---|
| Reception | VLAN 80 | 192.168.8.0/24 | 192.168.8.1 |
| Store | VLAN 70 | 192.168.7.0/24 | 192.168.7.1 |
| Logistics | VLAN 60 | 192.168.6.0/24 | 192.168.6.1 |

### 🟩 Floor 2 — F2-Router

| Department | VLAN | Network | Gateway |
|---|---|---|---|
| Finance | VLAN 50 | 192.168.5.0/24 | 192.168.5.1 |
| HR | VLAN 40 | 192.168.4.0/24 | 192.168.4.1 |
| Sales/Marketing | VLAN 30 | 192.168.3.0/24 | 192.168.3.1 |

### 🟥 Floor 3 — F3-Router

| Department | VLAN | Network | Gateway |
|---|---|---|---|
| IT | VLAN 10 | 192.168.1.0/24 | 192.168.1.1 |
| Admin | VLAN 20 | 192.168.2.0/24 | 192.168.2.1 |

### WAN Links (Serial DCE — /30)

| Link | Network | IP End A | IP End B |
|---|---|---|---|
| F2 ↔ F3 | 10.10.10.0/30 | F2: 10.10.10.1 | F3: 10.10.10.2 |
| F3 ↔ F1 | 10.10.10.4/30 | F1: 10.10.10.5 | F3: 10.10.10.6 |
| F2 ↔ F1 | 10.10.10.8/30 | F2: 10.10.10.9 | F1: 10.10.10.9 |

---

## 🔧 Technologies Used

- **Routers:** 3x Cisco 2911 — one per floor, placed in IT server room
- **Switches:** 3x Cisco 2960 — one per floor
- **WAN:** Serial DCE links between all 3 routers using /30 subnets
- **Routing:** OSPF Area 0 — dynamic route advertisement across all floors
- **DHCP:** Each floor router as DHCP server for its VLANs
- **Encapsulation:** IEEE 802.1Q (dot1Q) — Router-on-a-Stick per floor
- **SSH:** Secure remote login configured on all 3 routers
- **Port Security:** Sticky MAC address on Floor 3 IT switch fa0/2
- **Wireless:** Access Points per floor for laptops and smartphones

---

## ⚙️ Key Configurations

### Router-on-a-Stick (F1-Router)
```bash
interface GigabitEthernet0/0.60
 encapsulation dot1Q 60
 ip address 192.168.6.1 255.255.255.0

interface GigabitEthernet0/0.70
 encapsulation dot1Q 70
 ip address 192.168.7.1 255.255.255.0

interface GigabitEthernet0/0.80
 encapsulation dot1Q 80
 ip address 192.168.8.1 255.255.255.0
```

### Serial DCE WAN Links (F1-Router)
```bash
interface Serial0/2/0
 ip address 10.10.10.5 255.255.255.252

interface Serial0/2/1
 ip address 10.10.10.9 255.255.255.252
```

### OSPF (F1-Router)
```bash
router ospf 10
 network 10.10.10.4 0.0.0.3 area 0
 network 10.10.10.8 0.0.0.3 area 0
 network 192.168.8.0 0.0.0.255 area 0
 network 192.168.7.0 0.0.0.255 area 0
 network 192.168.6.0 0.0.0.255 area 0
```

### DHCP Pools (F1-Router)
```bash
ip dhcp pool Reception
 network 192.168.8.0 255.255.255.0
 default-router 192.168.8.1
 dns-server 192.168.8.1

ip dhcp pool Store
 network 192.168.7.0 255.255.255.0
 default-router 192.168.7.1
 dns-server 192.168.7.1

ip dhcp pool Logistics
 network 192.168.6.0 255.255.255.0
 default-router 192.168.6.1
 dns-server 192.168.6.1
```

### SSH (all routers)
```bash
ip domain-name Dacar
username Dacar password Dacar123
crypto key generate rsa

line vty 0 4
 login local
 transport input ssh
```

### Switch Floor 1 — Trunk + Access Ports
```bash
interface FastEthernet0/1
 switchport mode trunk              ! Uplink to F1-Router

interface FastEthernet0/2
 switchport access vlan 80
 switchport mode access             ! Reception

interface FastEthernet0/4
 switchport access vlan 70
 switchport mode access             ! Store

interface FastEthernet0/6
 switchport access vlan 60
 switchport mode access             ! Logistics
```

### Switch Floor 2 — Trunk + Access Ports
```bash
interface FastEthernet0/1
 switchport mode trunk              ! Uplink to F2-Router

interface FastEthernet0/2
 switchport access vlan 50
 switchport mode access             ! Finance

interface FastEthernet0/4
 switchport access vlan 40
 switchport mode access             ! HR

interface FastEthernet0/6
 switchport access vlan 30
 switchport mode access             ! Sales
```

### Switch Floor 3 — Port Security on IT Port
```bash
interface FastEthernet0/1
 switchport mode trunk              ! Uplink to F3-Router

interface FastEthernet0/2
 switchport access vlan 10
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky
 switchport port-security mac-address sticky 0002.4AB5.E588
                                    ! Only Test-PC allowed on this port

interface FastEthernet0/3
 switchport access vlan 10
 switchport mode access             ! IT department

interface FastEthernet0/4
 switchport access vlan 20
 switchport mode access             ! Admin department
```

---

## ✅ Testing & Verification

| Test | Result |
|---|---|
| Inter-VLAN routing same floor        | ✅ Success |
| Inter-floor communication via OSPF   | ✅ Success |
| DHCP assignment all 8 VLANs          | ✅ Success |
| SSH remote login all routers         | ✅ Success |
| Port security sticky MAC on IT fa0/2 | ✅ Success |
| Wireless connectivity per floor      | ✅ Success |
| All 8 departments reachable          | ✅ Success |

---

## 📁 Project Files

```
02-Hotel-Network/
├── topology.png             ← Full network topology screenshot
├── README.md                ← This file
└── configs/
    ├── F1-Router.txt        ← Floor 1 router config
    ├── F2-Router.txt        ← Floor 2 router config
    ├── F3-Router.txt        ← Floor 3 router config
    ├── F1-Switch.txt        ← Floor 1 switch config
    ├── F2-Switch.txt        ← Floor 2 switch config
    └── F3-Switch.txt        ← Floor 3 switch config
```

---

## 💡 Key Takeaways

- Designing a **multi-floor enterprise network** with dedicated routers per floor
- Configuring **OSPF Area 0** for dynamic routing across 8 VLANs
- Setting up **Serial DCE** WAN links using /30 subnets between routers
- Implementing **SSH** for secure remote management on all routers
- Applying **port security** with sticky MAC and shutdown violation mode
- Deploying **DHCP** on each router as server for multiple VLAN pools
- Segmenting **8 departments** into isolated VLANs across 3 floors

---

## 🚀 How to Open

1. Download and install [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer)
2. Clone this repository
3. Open the `.pkt` file in Packet Tracer
4. Explore the topology and test connectivity!

---

## 👤 Author

**MOHAMED ADAN MOHAMUD**
- GitHub: [@Mohamed-Dacar](https://github.com/Mohamed-Dacar)
- LinkedIn: [www.linkedin.com/in/mohamed-adan-mohamud-97558213a](http://www.linkedin.com/in/mohamed-adan-mohamud-97558213a)

---

*Built with Cisco Packet Tracer as part of a Networking Portfolio — Project 2*
