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

Layer 2 Access Switch (SW1)
enable
configure terminal
! Define VLAN database
vlan 10
 name Sales
vlan 20
 name Tech
exit

! Configure trunk port toward DSW1
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown

! Configure access port for PC2
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10
 no shutdown

! Configure access port for PC1
interface FastEthernet0/2
 switchport mode access
 switchport access vlan 20
 no shutdown
exit
4. Verification & Testing
Verify proper operational state and forwarding behavior using the following CLI diagnostic commands:

Verify Active VLANs:
show vlan brief
Verification: Confirm that VLAN 10 and VLAN 20 are listed as active and assigned to their corresponding interface ports[cite: 1].

Verify Layer 3 Routing Table:
show ip route
Verification: Ensure directly connected networks (10.0.0.0/24 and 20.0.0.0/24) appear in the routing table.

End-to-End Connectivity Check:
From PC2 (10.0.0.10), ping across the gateway to PC1[cite: 1]:

Bash
ping 20.0.0.10
Verification: Successful ICMP echo replies confirm that Layer 2 trunking and Layer 3 SVI routing are functioning correctly.


<Elicitations message="What would you like to do next?">
  <Elicitation label="Add OSPF protocol configuration" query="Can you add OSPF routing configuration to this lab write-up?"/>
  <Elicitation label="Configure GitHub Pages site" query="How do I verify that my GitHub Pages deployment is active?"/>
</Elicitations>
