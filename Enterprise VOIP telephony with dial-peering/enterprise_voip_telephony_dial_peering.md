# **Enterprise Networking Project – IP Telephony (VoIP) Implementation**

## **📌 Project Overview**

This project focuses on designing and implementing a complete **Enterprise Network with Voice over IP (VoIP)** using Cisco routers, switches, IP phones, and servers.

The objective was to build a fully functional IP Telephony system across **Finance, HR, Sales, and ICT departments**, including:

-   Multi-VLAN network architecture
    
-   Router-on-a-Stick inter-VLAN routing
    
-   OSPF routing across WAN links
    
-   DHCP for both **data** and **voice networks**
    
-   IP Phone deployment & registration
    
-   Dial-peer configuration for inter-department voice communication
    
-   Proper device security (SSH, passwords, banners)
    

----------

# **🎯 Purpose of the Project**

The main business requirement was to deploy a scalable, secure, and fully functioning **VoIP-enabled enterprise network** for _Total Consultancy Ltd_.

The network needed to support:

-   20 PCs + 20 IP Phones + 1 Printer per department
    
-   Central server room with DHCP, DNS, Email, and Web servers
    
-   Unique dialing ranges for each department
    
-   Seamless VoIP communication between all departments
    
-   DHCP-based IP allocation for both data and IP phones
    
-   OSPF-based dynamic routing across all routers
    
-   Cisco 2811 Routers and 2960 Switches
    

This project replicates a **real-world enterprise VoIP deployment** from scratch.

----------

# **🏛️ Network Topology Summary**

### **Departments**

Department

Devices

Data VLAN

Voice VLAN

Router

Finance

20 PCs, 20 Phones, 1 Printer

VLAN 10

VLAN 100

FIN-RTR

HR

20 PCs, 20 Phones, 1 Printer

VLAN 20

VLAN 100

HR-RTR

Sales

20 PCs, 20 Phones, 1 Printer

VLAN 30

VLAN 100

SALES-RTR

ICT

20 PCs, 20 Phones, 1 Printer

VLAN 40

VLAN 100

ICT-RTR

### **Servers (Static IPs)**

-   DHCP Server
    
-   DNS Server
    
-   HTTP Server
    
-   Email Server
    
-   All reside in **Server VLAN 50**
    

### **WAN Connectivity**

-   All routers connected using **Serial DTE/DCE** interfaces
    
-   WAN links use network block: `10.10.10.0/30`
    

----------

# **🧱 Step-by-Step Implementation Details**

----------

# **1️⃣ Topology Design & Device Placement**

-   Placed **four Cisco 2811 routers** (one per department).
    
-   Installed **HWIC-2T serial modules** on routers for WAN connections.
    
-   Connected **Cisco 2960 switches** as access-layer switches.
    
-   Added **20 IP Phones**, **20 PCs**, and **1 Printer** per department.
    
-   Connected PC → IP Phone → Switch as required.
    
-   Designed server room with all required servers.
    
-   Organized topology using shapes and labels for professional documentation.
    

----------

# **2️⃣ Basic Device Configuration**

Configured on all routers & switches:

-   Hostnames
    
-   Console & VTY passwords
    
-   Enable secret
    
-   Login banners
    
-   Password encryption (`service password-encryption`)
    
-   Disabled domain lookup
    
-   SSH remote access on all routers
    
-   Local user authentication for VTY
    

> SSH configuration included RSA key generation and VTY restrictions.

----------

# **3️⃣ VLAN & Switchport Configuration**

For each switch:

### **Created Data & Voice VLANs:**

```bash
vlan 10
 name DATA
vlan 100
 name VOICE

```

### **Assigned Ports**

-   Ports connecting to routers → **Trunk**
    
-   Ports to IP phones/PCs → **Access + Voice VLAN**
    

Example:

```bash
interface fa0/1
 switchport mode trunk

interface range fa0/2 – 24
 switchport mode access
 switchport access vlan 10
 switchport voice vlan 100

```

----------

# **4️⃣ Subnetting & IP Addressing**

Performed subnetting for:

-   Four data VLANs (each `/27`)
    
-   Four voice VLANs (each `/27`)
    
-   Server VLAN `/29`
    
-   WAN `/30`
    

Assigned:

-   IP subnets
    
-   Router default gateways
    
-   DHCP scopes
    
-   Static server IPs
    

----------

# **5️⃣ Server Room Configuration**

Each server received **static IPs** in VLAN 50:

-   Example:
    

```
DNS Server: 192.168.100.131/29
Default Gateway: 192.168.100.129

```

Configured services:

-   DHCP (for data VLANs)
    
-   DNS
    
-   HTTP
    
-   Email
    

----------

# **6️⃣ DHCP Configuration**

### **Data DHCP (Central Server)**

Created separate pools for Finance, HR, Sales, ICT.

Example:

```
Pool: FIN-DATA
Default Gateway: 192.168.100.1
Network: 192.168.100.0/27
DNS: 192.168.100.131

```

### **Voice DHCP (On Routers per Department)**

Example (Finance Router):

```bash
service dhcp
ip dhcp excluded-address 172.16.100.1

ip dhcp pool FIN-VOICE
 network 172.16.100.0 255.255.255.224
 default-router 172.16.100.1
 option 150 ip 172.16.100.1

```

> **Option 150** specifies the TFTP server used by IP Phones.

----------

# **7️⃣ Router-on-a-Stick (Inter-VLAN Routing)**

Created subinterfaces on each departmental router:

### **Data Subinterface**

```bash
interface fa0/0.10
 encapsulation dot1Q 10
 ip address 192.168.100.1 255.255.255.224
 ip helper-address <DHCP SERVER IP>

```

### **Voice Subinterface**

```bash
interface fa0/0.100
 encapsulation dot1Q 100
 ip address 172.16.100.1 255.255.255.224

```

> Voice VLAN does **not** use IP helper because each router provides DHCP for voice.

----------

# **8️⃣ OSPF Routing**

Enabled OSPF on all routers for WAN & LAN advertisement:

```bash
router ospf 10
 network 10.10.10.0 0.0.0.3 area 0
 network 192.168.100.0 0.0.0.31 area 0
 network 172.16.100.0 0.0.0.31 area 0

```

Ensured full adjacency across the topology.

----------

# **9️⃣ VoIP Service Configuration (Telephony-Service)**

Enabled the telephony service:

```bash
telephony-service
 max-dn 20
 max-ephone 20
 ip source-address <VOICE-GW-IP> port 2000
 auto assign 1 to 20

```

Created directory numbers:

```bash
ephone-dn 1
 number 101
ephone-dn 2
 number 102
...

```

Dial plan per department:

-   **Finance:** `1xx`
    
-   **HR:** `2xx`
    
-   **Sales:** `3xx`
    
-   **ICT:** `4xx`
    

----------

# **🔟 VoIP Dial-Peer Configuration**

Configured voice routing between routers:

### **Example: Finance → HR**

```bash
dial-peer voice 1 voip
 destination-pattern 2..
 session target ipv4:<HR-Router-IP>

```

Done similarly for all departments to ensure full communication matrix.

----------

# **1️⃣1️⃣ Testing & Validation**

Performed complete verification:

### **Connectivity**

-   PCs received DHCP successfully
    
-   IP phones obtained IP from router DHCP
    
-   Servers reachable from all VLANs
    

### **VoIP Validation**

-   Phones registered with the correct gateway
    
-   Inter-department calling tested:
    
    -   Finance → HR
        
    -   Sales → ICT
        
    -   Etc.
        

### **Routing**

-   OSPF neighbors formed successfully
    
-   All subnets visible in routing table
    

----------

# **📦 Deliverables Created**

This project produced:

-   Complete `.pkt` Packet Tracer file
    
-   Full technical configuration document
    
-   Network diagrams
    
-   VLAN design table
    
-   VoIP dial plan documentation
    
-   Subnetting sheet
    

----------

# **🚀 Final Outcome**

A fully functional enterprise-level **Voice over IP (VoIP)** system was deployed with:

✔ Scalable multi-VLAN architecture  
✔ Secure router configurations with SSH  
✔ OSPF WAN routing  
✔ Centralized & distributed DHCP  
✔ Fully working IP phones  
✔ Cross-site calling using dial peers  
✔ Professional documentation

This complete project demonstrates enterprise-grade networking design, configuration, and troubleshooting skills applicable in real corporate environments.