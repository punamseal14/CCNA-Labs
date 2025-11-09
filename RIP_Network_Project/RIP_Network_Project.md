# 🛰️ RIP-Based Multi-LAN Network Project

## 📘 Objective
The goal of this activity was to **design, configure, and verify a multi-LAN network using RIP (Routing Information Protocol)** in Cisco Packet Tracer.  
The setup demonstrates **dynamic routing, VLAN trunking, and inter-VLAN communication** between multiple routers, switches, and PCs.

---

## 🧩 Topology Overview

The network consists of **three LANs** connected through **three routers** using RIP Version 2.  
Each LAN includes one router, one switch, and two PCs. VLAN 10 was used on all switches for consistency.

```
LAN 1 (R1)
 ├── R1 (10.10.10.1/8)
 ├── SW1 (10.10.10.2/8)
 ├── PC0 (10.10.10.3/8)
 └── PC1 (10.10.10.4/8)

LAN 2 (R2)
 ├── R2 (20.20.20.1/8)
 ├── SW2 (20.20.20.2/8)
 ├── PC2 (20.20.20.3/8)
 └── PC3 (20.20.20.4/8)

LAN 3 (R3)
 ├── R3 (30.30.30.1/8)
 ├── SW3 (30.30.30.2/8)
 ├── PC4 (30.30.30.3/8)
 └── PC5 (30.30.30.4/8)

WAN Links
 ├── R1 ↔ R2 : 40.40.40.0/8
 └── R2 ↔ R3 : 50.50.50.0/8
```

---

## ⚙️ Configuration Summary

### 🖥️ Routers
Each router performs:
- Subinterface configuration for VLAN 10
- RIP v2 dynamic routing
- Serial link connections for WAN

#### Example (R1)
```bash
interface fa0/0.10
 encapsulation dot1Q 10
 ip address 10.10.10.1 255.0.0.0
!
interface s2/0
 ip address 40.40.40.1 255.0.0.0
 clock rate 2000000
!
router rip
 version 2
 no auto-summary
 network 10.0.0.0
 network 40.0.0.0
```

#### Example (R2)
```bash
interface fa0/0.10
 encapsulation dot1Q 10
 ip address 20.20.20.1 255.0.0.0
!
interface s2/0
 ip address 40.40.40.2 255.0.0.0
!
interface s3/0
 ip address 50.50.50.1 255.0.0.0
 clock rate 2000000
!
router rip
 version 2
 no auto-summary
 network 20.0.0.0
 network 40.0.0.0
 network 50.0.0.0
```

---

### 🖧 Switches
All switches are configured for VLAN 10 with a trunk to the router.

#### Example (SW2)
```bash
vlan 10
 name LAN10
!
interface fa0/1
 switchport mode trunk
!
interface fa1/1
 switchport access vlan 10
 switchport mode access
!
interface vlan10
 ip address 20.20.20.2 255.0.0.0
!
ip default-gateway 20.20.20.1
```

---

## 🌍 Verifications

### ✅ Interface Status
```bash
show ip interface brief
```
All subinterfaces and serial links are **up/up**.

### ✅ RIP Routes
```bash
show ip route
```
All LAN networks (10.0.0.0, 20.0.0.0, 30.0.0.0) are visible as **R** routes on all routers.

### ✅ End-to-End Connectivity
All PCs across different LANs can successfully ping each other:
- PC0 → PC2 → PC4 ✅
- PC4 → PC0 ✅
- Routers can reach all LANs via RIP ✅

---

## 🧠 Key Learnings
- Configured **RIP v2** for dynamic routing between routers.
- Implemented **VLAN trunking and subinterfaces** for inter-VLAN routing.
- Used **simulation mode** in Packet Tracer to analyze packet flow.
- Verified **end-to-end communication** across all networks.

---

## 📁 Files Included
| File | Description |
|------|--------------|
| [`RIP.pkt`](./RIP.pkt) | Cisco Packet Tracer project file |
| `RIP_Network_Project.md` | Project documentation (this file) |

---

## 🏁 Conclusion
This project successfully demonstrates how RIP dynamically shares route information among routers in a multi-LAN environment.  
It showcases complete LAN segmentation using VLANs and WAN interconnection using serial interfaces, providing a realistic model of enterprise-level inter-network communication.
