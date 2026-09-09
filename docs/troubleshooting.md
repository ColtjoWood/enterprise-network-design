# Network Troubleshooting

Throughout the project, several routing, switching, addressing, and simulation issues were encountered. This document records the major problems, troubleshooting methods, and resolutions.

## 1. Duplicate OSPF Router IDs

### Problem
Dallas and Austin initially developed conflicting OSPF router IDs, resulting in incorrect route propagation.

### Troubleshooting
OSPF neighbors, routing tables, and router IDs were inspected using Cisco IOS verification commands.

### Resolution
Unique router IDs were manually assigned:

- HQ-R1 — 1.1.1.1
- DAL-R1 — 2.2.2.2
- AUS-R1 — 3.3.3.3
- HQ-CORE-SW1 — 4.4.4.4
- HQ-EDGE-R1 — 5.5.5.5

OSPF was then reconverged and routing was retested.

---

## 2. OSPF Network-Type Mismatch

### Problem
An OSPF WAN connection had mismatched network types. One interface operated as a broadcast network while the opposite interface operated as point-to-point.

This caused inconsistent OSPF behavior and prevented expected routes from being installed.

### Resolution
Both ends of the branch WAN links were explicitly configured as OSPF point-to-point interfaces.

After OSPF reconverged, the routes appeared correctly.

---

## 3. Missing Austin Route

### Problem
During final validation, the Austin network:

`10.30.10.0/27`

disappeared from the HQ routing table even though the Austin OSPF neighbor still appeared FULL.

### Troubleshooting
The following were inspected:

- OSPF neighbor state
- HQ routing table
- Austin routing table
- Austin LAN interface
- OSPF network statements
- OSPF interface configuration
- OSPF network type

### Resolution
The Austin WAN interface was explicitly returned to the point-to-point OSPF network type and OSPF was reconverged.

The `10.30.10.0/27` route returned to the HQ routing table and end-to-end connectivity was successfully restored.

### Lesson Learned
A FULL OSPF neighbor relationship does not automatically guarantee that every expected route is being installed.

---

## 4. DHCP / APIPA Address

### Problem
During final testing, an IT workstation received a `169.254.x.x` APIPA address instead of an address from the configured DHCP pool.

### Troubleshooting
The DHCP path was checked, including:

- Client VLAN membership
- DHCP pool
- Default gateway
- DHCP relay configuration
- Connectivity to the DHCP server

### Resolution
The configuration was corrected and the workstation successfully obtained a valid address from the IT DHCP pool.

---

## 5. NAT on Multilayer Switch

### Problem
NAT/PAT was originally planned for the HQ multilayer switch.

Cisco Packet Tracer's simulated 3560 did not support the required NAT configuration.

### Resolution
A dedicated `HQ-EDGE-R1` router was added between the HQ core and ISP.

The edge router became responsible for:

- NAT/PAT
- Internet-facing connectivity
- Default routing toward the ISP

### Lesson Learned
Device capabilities and simulator limitations must be considered when designing a network.

---

## 6. EtherChannel Member Failure

### Problem
During final verification, Port-Channel 1 was operational but only one of the two expected FastEthernet interfaces was actively participating.

Resetting individual interfaces caused inconsistent bundling behavior.

### Troubleshooting
Configuration was compared on both sides of the connection, including:

- Physical interface status
- Trunk configuration
- LACP mode
- Channel-group membership
- Port-Channel status

### Resolution
Port-Channel 1 was removed and cleanly rebuilt on both switches.

Final verification showed both physical interfaces participating and an aggregate simulated bandwidth of 200 Mbps.

### Failover Test
One EtherChannel member was physically disconnected.

Traffic continued successfully over the remaining link.

**Result: PASS**

---

## 7. Guest ACL Binding

### Problem
An extended ACL was created to isolate Guest VLAN traffic from internal enterprise networks.

The ACL worked during an earlier stage of the project.

During final validation, Packet Tracer accepted the `ip access-group` command on the VLAN 50 SVI but did not retain the ACL binding.

### Troubleshooting
The ACL itself was verified and remained present in the running configuration.

However, inspection of VLAN 50 showed that no incoming or outgoing ACL was bound to the SVI.

Both named and numbered ACL approaches were tested.

### Outcome
The behavior was determined to be a Cisco Packet Tracer simulation limitation.

The ACL configuration was retained for documentation, but final guest isolation was not reported as successfully validated.

---

## 8. Configuration Loss After Hardware Change

### Problem
During earlier WAN development, hardware modules were changed on a router.

Powering off the Packet Tracer router before saving the running configuration caused configuration changes to be lost.

### Resolution
The required configuration was rebuilt.

Afterward, configurations were regularly saved using:

`copy running-config startup-config`

### Lesson Learned
Configuration should be saved before device reboots, hardware changes, or other potentially disruptive operations.

---

# Useful Troubleshooting Commands

Commands frequently used during the project included:

`show ip route`

`show ip route ospf`

`show ip ospf neighbor`

`show ip ospf interface`

`show ip protocols`

`show interfaces`

`show interfaces trunk`

`show etherchannel summary`

`show interfaces port-channel 1`

`show port-security interface`

`show access-lists`

`show ip nat translations`

`show ip nat statistics`

`show running-config`

These commands were used to verify network state instead of relying only on the Packet Tracer topology view.

---

# Key Takeaway

The most valuable part of this project was troubleshooting situations where a configuration appeared correct but the network still did not behave as expected.

The project reinforced a structured troubleshooting process:

1. Identify the failing path.
2. Verify physical and interface status.
3. Verify Layer 2 configuration.
4. Verify IP addressing and gateways.
5. Inspect routing tables.
6. Inspect routing protocol state.
7. Verify services and security policies.
8. Make one change at a time.
9. Retest end-to-end connectivity.
