# Reliable Data Transfer System for Online Banking

A Project-Based Learning (PBL) networking project analyzing and simulating error detection, error control, flow control, and resilient routing mechanisms for secure online banking transactions.

**Institution:** Brainware University — School of Engineering (Dept. of CSE - Cyber Security & Data Science)  
**Course:** Network Fundamentals and Practical Applications (`BTY50112`)  
**Supervisor:** Dr. Arnab Kundu, Asst. Professor, CSE-CS & DS  

---

## Overview

In financial transaction systems, even a single flipped bit in transit can corrupt an account number or transaction amount. This project combines mathematical Data Link Layer error detection with a segmented client-server network simulation in **Cisco Packet Tracer** to demonstrate end-to-end reliability:

* **Error Detection (CRC):** Cyclic Redundancy Check using modulo-2 binary division to detect single-bit, double-bit, and burst errors.
* **Error Control (ARQ):** Automatic Repeat Request mechanisms to discard corrupted frames and trigger retransmissions.
* **Flow Control (Sliding Window):** Regulating transmission rates across mismatched LAN (100 Mbps / 1 Gbps) and WAN serial (64 kbps) links.
* **Resilient Routing & Security:** Dynamic routing via **EIGRP (AS 100)** with `passive-interface` configured on all LAN-facing ports to prevent unauthorized routing adjacencies, alongside isolated Primary and Backup banking servers.

---

## Network Topology

![Network Topology](assets/topology-diagram.png)

### Architecture Highlights
* **Client Branch (`Router0` - Cisco ISR4331):** Segments client workstations into two LANs (`192.168.1.0/24` and `192.168.2.0/24`) via Catalyst 2960-24TT switches.
* **Banking Data Center (`Router1` - Cisco ISR4331):** Hosts an isolated Primary Banking Server (`210.1.1.0/24`) and a Backup Banking Server (`211.1.1.0/24`) for high availability and disaster recovery.
* **WAN Backbone:** Point-to-point serial link (`10.0.0.0/30`) clocked at `64000 bps` on the DCE interface (`Router1`).

---

## IP Addressing & Subnetting Table

| Device | Interface | IPv4 Address | Subnet Mask | Default Gateway | DNS Server | Role |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Router0** | Gig0/0/0 | `192.168.1.1` | `255.255.255.0` | N/A | N/A | Client LAN 1 Gateway |
| **Router0** | Gig0/0/1 | `192.168.2.1` | `255.255.255.0` | N/A | N/A | Client LAN 2 Gateway |
| **Router0** | Serial0/1/0 | `10.0.0.1` | `255.255.255.0` | N/A | N/A | WAN Link to Router1 |
| **Router1** | Gig0/0/0 | `210.1.1.1` | `255.255.255.0` | N/A | N/A | Primary Server Gateway |
| **Router1** | Gig0/0/1 | `211.1.1.1` | `255.255.255.0` | N/A | N/A | Backup Server Gateway |
| **Router1** | Serial0/1/0 | `10.0.0.2` | `255.255.255.252` | N/A | N/A | WAN Link (DCE, 64000) |
| **Server0** | FastEthernet0 | `210.1.1.10` | `255.255.255.0` | `210.1.1.1` | `210.1.1.10` | Primary Banking Server |
| **Server1** | FastEthernet0 | `211.1.1.10` | `255.255.255.0` | `211.1.1.1` | `211.1.1.10` | Backup Banking Server |
| **PC0 / PC1** | FastEthernet0 | `192.168.1.10–11` | `255.255.255.0` | `192.168.1.1` | `210.1.1.10` | Client Terminals (LAN 1) |
| **PC2–PC6** | FastEthernet0 | `192.168.2.10–14` | `255.255.255.0` | `192.168.2.1` | `210.1.1.10` | Client Terminals (LAN 2) |

---

## CRC Numerical Analysis Summary

* **Dataword ($D$):** `1101011011` (10 bits)
* **Generator Polynomial ($G$):** `10011` ($x^4 + x + 1$)
* **Computed CRC Remainder ($R$):** `1110` (4 bits)
* **Transmitted Codeword ($T$):** `11010110111110` (14 bits)

| Scenario | Received Frame | Receiver Syndrome | Decision |
| :--- | :--- | :--- | :--- |
| **Error-Free** | `11010110111110` | `0000` (All Zeros) | **ACCEPTED** — CRC trailer stripped; payload forwarded to upper layers. |
| **Corrupted (7th bit flipped)** | `11010100111110` | `1011` (Non-Zero) | **REJECTED** — Frame discarded immediately; retransmission requested via ARQ. |

---

## Repository Structure

```text
online-banking-reliable-data-transfer/
├── assets/
│   └── topology-diagram.png
├── configs/
│   ├── router0-config.txt
│   └── router1-config.txt
├── docs/
│   └── Reliable-Data-Transfer-Banking-Report.pdf
├── simulation/
│   └── PBL1.1.pkt
└── README.md
