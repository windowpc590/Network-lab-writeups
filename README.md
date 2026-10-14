# Network-lab-writeups
# Enterprise Network Lab: Inter-VLAN Routing & SVI Configuration

## 1. Network Topology Overview
This lab simulates an enterprise local area network featuring a Layer 3 Distribution Switch (`DSW1`), a Layer 2 Access Switch (`SW1`), and two endpoint workstations (`PC1` and `PC2`) operating across isolated broadcast domains. Inter-VLAN routing is provisioned via Switched Virtual Interfaces (SVIs) to facilitate inter-subnet communication.

* **Topology Components:**
  * **DSW1 (WS-C3560-24TS):** Primary Layer 3 distribution switch managing gateway interfaces.
  * **SW1 (WS-C2960-24TT-L):** Layer 2 access switch connected to `DSW1` via a trunk link (`Gi0/1`)[cite: 1].
  * **PC1 (`20.0.0.10/24`):** Connected to `SW1` on port `FastEthernet0/2` (VLAN 20 / Tech)[cite: 1].
  * **PC2 (`10.0.0.10/24`):** Connected to `SW1` on port `FastEthernet0/1` (VLAN 10 / Sales)[cite: 1].

---

## 2. VLAN & Subnet Allocation Table

| VLAN ID | Name | Subnet / Mask | Gateway IP | Connected Port |
| :--- | :--- | :--- | :--- | :--- |
| **VLAN 10** | Sales | `10.0.0.0/24` | `10.0.0.1` | `SW1` / `Fa0/1` (PC2)[cite: 1] |
| **VLAN 20** | Tech | `20.0.0.0/24` | `20.0.0.1` | `SW1` / `Fa0/2` (PC1)[cite: 1] |

---

## 3. Switch Configurations

### Layer 3 Distribution Switch (`DSW1`)
```ios
enable
configure terminal
! Enable global IP routing
ip routing

! Define VLAN database
vlan 10
 name Sales
vlan 20
 name Tech
exit

! Configure SVI for VLAN 10 (Sales Gateway)
interface Vlan10
 ip address 10.0.0.1 255.255.255.0
 no shutdown

! Configure SVI for VLAN 20 (Tech Gateway)
interface Vlan20
 ip address 20.0.0.1 255.255.255.0
 no shutdown
exit
