# FastTrack-Network-Engineering-CC251L
Complex Computing Problem (CCP) : FastTrack Retail and Logistics System backed by computer network based on Cisco Packet Tracer TM
<img width="1600" height="788" alt="image" src="https://github.com/user-attachments/assets/2c8177fe-2ff8-4216-963e-d4ac015beb59" />
# FastTrack Retail & Logistics — Enterprise Network
### CCP-CNS2026 | Computer Networks Lab

---

## Project Overview

This repository contains the complete implementation of an enterprise network for **FastTrack Retail & Logistics Co.**, built as a Complex Computing Problem (CCP) for the Computer Networks course (CNS2026) under **Muhammad Fahad Irshad**.

The network was designed and simulated in **Cisco Packet Tracer** using a Multilayer Switch (MLS 3560-24PS) as the core device, providing inter-VLAN routing, DHCP, access control, SSH management, and dual ISP failover.

---

## Team Members

| Name 
|---|
| Muhammad Rehan Bhatti |
| Muhammad Ali Yasir |
| Muhammad Ali Waqar |

---

## Features Implemented

| Feature | Technology | Device |
|---|---|---|
| Inter-VLAN Routing | MLS SVIs (no router-on-a-stick) | MLS_Core (3560-24PS) |
| 6 Department VLANs | VLANs 10/20/30/40/50/60 | MLS_Core + Access Switches |
| DHCP | IOS DHCP Pools | MLS_Core |
| Access Control | Extended ACL (BLOCK_TO_MGMT) | MLS_Core Vlan50 |
| SSH Management | SSHv2 + access-class restriction | MLS_Core VTY lines |
| Dual ISP Failover | Floating static routes (AD 1 / AD 5) | Edge_Router |

---

## Network Topology

```
                        [ Internet ]
                       /            \
               [ ISP1_Primary ]   [ ISP2_Backup ]
               203.0.113.1/30     198.51.100.1/30
                       \            /
                        [ Edge_Router ]
                          10.0.0.1/30
                               |
                          [ MLS_Core ]
                          10.0.0.2/30
                    (3560-24PS — ip routing)
          __________|_________________________|__________
         |      |       |        |       |              |
     Fa0/1  Fa0/2   Fa0/3    Fa0/4   Fa0/5          Fa0/6
       |      |       |        |       |              |
  SW_Sales  SW_WH  SW_Logi  SW_Ship  SW_Mgmt       SW_IT
  VLAN 10  VLAN20  VLAN30   VLAN40   VLAN50        VLAN60
```

---

## VLAN & IP Plan

| VLAN | Department | Subnet | Gateway | ACL |
|---|---|---|---|---|
| 10 | Sales Floor | 192.168.10.0/24 | 192.168.10.1 | Blocked from VLAN 50 |
| 20 | Warehouse | 192.168.20.0/24 | 192.168.20.1 | Blocked from VLAN 50 |
| 30 | Logistics | 192.168.30.0/24 | 192.168.30.1 | Full access |
| 40 | Shipping | 192.168.40.0/24 | 192.168.40.1 | Blocked from VLAN 50 |
| 50 | Management | 192.168.50.0/24 | 192.168.50.1 | Accessible by VLAN 30 & 60 only |
| 60 | IT | 192.168.60.0/24 | 192.168.60.1 | Full access + SSH admin |

---

## Repository Structure

```
CCP-CNS2026/
│
├── README.md
│
├── topology/
│   └── final_Topology.png
│
├── packet-tracer/
│   └── CCP_CN_LAB.pkt
│
├── configs/
│   ├── MLS_Core_running-config.txt
│   ├── Edge_Router_running-config.txt
│   ├── ISP1_Primary_running-config.txt
│   ├── ISP2_Backup_running-config.txt
│   ├── SW_Sales_running-config.txt
│   ├── SW_Warehouse_running-config.txt
│   ├── SW_Logistics_running-config.txt
│   ├── SW_Shipping_running-config.txt
│   ├── SW_Mgmt_running-config.txt
│   └── SW_IT_running-config.txt
│
├── screenshots/
│   ├── step_1.png
│   ├── step_2.png
│   ├── step_3a.png
│   ├── step_3b.png
│   ├── step_4.png
│   ├── step_5.png
│   ├── step_6_DHCP.png
│   ├── step_7_ACL.png
│   ├── Step_8_SSH.png
│   ├── Step_9_SW_management_for_all_switches.png
│   ├── Step_10_Edge_Router.png
│   ├── Step_10_ISP1_primary.png
│   ├── Step_10_ISP2_Backup.png
│   ├── Step_11_DHCP_for_pc.png
│   ├── 4_TESTS_PC_SALES.png
│   ├── 4_TESTS_PC_LOGISTICS.png
│   ├── ACL_test.png
│   └── 8888_ping_success.png
│
└── report/
    └── FastTrack_Final_Report.docx
```

---

## Test Results

| Test | Source | Destination | Result |
|---|---|---|---|
| Inter-VLAN Routing | Sales PC2 | Logistics 192.168.30.10 | PASS — 4/4 replies |
| ACL Block | Sales PC2 | Management 192.168.50.10 | PASS — 100% loss |
| ACL Permit | Logistics PC1 | Management 192.168.50.10 | PASS — 4/4 replies |
| Internet Access | Sales PC2 | 8.8.8.8 | PASS — 4/4 replies |
| ISP Failover | Any PC | 8.8.8.8 via ISP2 | PASS — recovered after 2 lost |
| DHCP | All PCs | MLS DHCP Server | PASS — all PCs got correct IPs |

---

## How to Open

1. Install [Cisco Packet Tracer](https://skillsforall.com/course/getting-started-cisco-packet-tracer)
2. Clone this repository
3. Open `packet-tracer/CCP_CN_LAB.pkt`
4. All configurations are saved — no re-configuration needed
5. Switch to Simulation mode to observe traffic flow

---

## Key Configuration Notes

- MLS model is **3560-24PS** — requires `ip routing` to enable Layer 3 switching
- Trunk ports on 3560 require `switchport trunk encapsulation dot1q` before `switchport mode trunk`
- DHCP is served from the MLS directly — no separate DHCP server needed
- ACL `BLOCK_TO_MGMT` is applied **inbound on Vlan50** — drops traffic before it enters Management subnet
- SSH is restricted via `access-class SSH_ALLOW in` on VTY lines — only 192.168.60.0/24 (IT VLAN) can connect
- ISP failover uses **administrative distance**: AD 1 (primary via ISP1), AD 5 (backup via ISP2)

---

## Tools Used

- Cisco Packet Tracer 8.x
- Cisco IOS 12.2(37)SE1 (3560-24PS)
- Cisco IOS 15.x (2911 Routers)

---

*CCP-CNS2026 — Computer Networks Lab - UMT Lahore*
