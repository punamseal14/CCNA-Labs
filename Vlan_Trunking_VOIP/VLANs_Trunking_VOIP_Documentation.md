# VLANs, Trunking, and VoIP Configuration using Cisco Packet Tracer

## Objective
To design and implement a network in Cisco Packet Tracer that demonstrates:
- VLAN segmentation (Data, Voice, and Native VLANs)
- Router-on-a-stick inter-VLAN routing
- DHCP configuration for automatic IP allocation
- VoIP setup using Cisco Unified CME (Call Manager Express)
- Successful IP phone registration and inter-VLAN communication

## Devices Used
| Device Type | Model | Quantity | Purpose |
|--------------|--------|-----------|----------|
| Router | Cisco 2811 | 1 | Inter-VLAN routing, DHCP, CME configuration |
| Switch | Cisco 2960 | 1 | VLAN creation and trunk configuration |
| PC | Generic | 2 | End-user data devices (VLAN 10) |
| IP Phone | Cisco 7960 | 2 | VoIP endpoints (VLAN 20) |

## VLAN Configuration
| VLAN ID | Name | Purpose | Subnet |
|----------|-------|----------|---------|
| 10 | DATA | For PC communication | 192.168.10.0/24 |
| 20 | VOICE | For IP Phones | 192.168.20.0/24 |
| 99 | NATIVE | Trunk native VLAN | 192.168.99.0/24 |

## Router Configuration (Router-on-a-Stick)
```bash
interface FastEthernet0/0
 no shutdown

interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface FastEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0

interface FastEthernet0/0.99
 encapsulation dot1Q 99 native
 ip address 192.168.99.1 255.255.255.0
```

## DHCP Configuration
```bash
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.20.1 192.168.20.10
ip dhcp excluded-address 192.168.99.1 192.168.99.10

ip dhcp pool DATA
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 option 150 ip 192.168.10.1
 dns-server 8.8.8.8

ip dhcp pool VOICE
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 option 150 ip 192.168.20.1
 dns-server 8.8.8.8
```

## Telephony Configuration (Cisco CME)
```bash
telephony-service
 max-ephones 5
 max-dn 5
 ip source-address 192.168.20.1 port 2000
 auto assign 1 to 2

ephone-dn 1
 number 1010

ephone-dn 2
 number 1020

ephone 1
 mac-address <Phone1-MAC>
 type 7960
 button 1:1

ephone 2
 mac-address <Phone2-MAC>
 type 7960
 button 1:2
```

## Switch Configuration
```bash
vlan 10
 name DATA
vlan 20
 name VOICE
vlan 99
 name NATIVE

interface FastEthernet0/1
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,99

interface range FastEthernet0/2-3
 switchport mode access
 switchport access vlan 10
 switchport voice vlan 20
 spanning-tree portfast
```

## Network Topology Overview
```
     [PC1]     [PC2]
       |         |
      Fa0/2    Fa0/3
        \       /
       [Switch 2960]
            |
        Fa0/1 (Trunk)
            |
       [Router 2811]
         |Fa0/0.10 → VLAN 10 (DATA)
         |Fa0/0.20 → VLAN 20 (VOICE)
         |Fa0/0.99 → VLAN 99 (NATIVE)
```

## Verification
| Test | Result |
|------|---------|
| PCs received IPs from VLAN 10 DHCP | ✅ Successful |
| IP Phones received IPs from VLAN 20 DHCP | ✅ Successful |
| Phones registered with CME | ✅ Successful |
| Inter-VLAN routing between VLAN 10 & VLAN 20 | ✅ Working |
| Successful VoIP call between two IP phones | ✅ Working |

## What I Achieved
- Implemented VLAN segmentation and trunking in a single-switch environment.
- Configured router-on-a-stick for inter-VLAN routing.
- Automated IP allocation using DHCP for both data and voice VLANs.
- Integrated Cisco Unified CME to provide IP telephony service.
- Verified full VoIP functionality — IP phones registered and communicated successfully.
