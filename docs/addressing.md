# Network Addressing Plan

This document contains the IPv4 addressing and VLAN scheme used throughout the enterprise network.

## Lubbock HQ VLANs

| VLAN | Name | Network | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| 10 | USERS | 10.10.0.0/25 | 255.255.255.128 | 10.10.0.1 |
| 20 | IT | 10.10.1.96/28 | 255.255.255.240 | 10.10.1.97 |
| 30 | SERVERS | 10.10.1.112/28 | 255.255.255.240 | 10.10.1.113 |
| 40 | VOICE | 10.10.0.128/25 | 255.255.255.128 | 10.10.0.129 |
| 50 | GUEST | 10.10.1.0/26 | 255.255.255.192 | 10.10.1.1 |
| 99 | MANAGEMENT | 10.10.1.64/27 | 255.255.255.224 | 10.10.1.65 |

## Branch Networks

| Site | Network | Subnet Mask | Default Gateway |
|---|---|---|---|
| Dallas | 10.20.10.0/26 | 255.255.255.192 | 10.20.10.1 |
| Austin | 10.30.10.0/27 | 255.255.255.224 | 10.30.10.1 |

## WAN Links

| Connection | Network | Side A | Side B |
|---|---|---|---|
| HQ-R1 ↔ Dallas | 10.255.0.0/30 | HQ: 10.255.0.1 | Dallas: 10.255.0.2 |
| HQ-R1 ↔ Austin | 10.255.0.4/30 | HQ: 10.255.0.5 | Austin: 10.255.0.6 |
| HQ Core ↔ HQ-R1 | 10.10.255.0/30 | Core: 10.10.255.1 | HQ-R1: 10.10.255.2 |
| HQ Core ↔ Edge | 10.10.255.4/30 | Core: 10.10.255.5 | Edge: 10.10.255.6 |

## Internet Simulation

| Device / Interface | Address |
|---|---|
| HQ Edge Outside | 203.0.113.2/30 |
| ISP Gateway | 203.0.113.1/30 |
| ISP Internet LAN | 198.51.100.1/24 |
| Internet Server | 198.51.100.10/24 |

## Infrastructure Servers

| Server | IP Address | Purpose |
|---|---|---|
| SRV-DHCP-DNS | 10.10.1.114/28 | Centralized DHCP and DNS |
| SRV-WEB | 10.10.1.115/28 | Internal HTTP/Intranet |

Internal DNS maps:

`intranet.lubbock.local` → `10.10.1.115`

## Addressing Strategy

Variable Length Subnet Masking (VLSM) was used to allocate address space based on the expected size of each network.

Larger user and voice networks received /25 subnets, while smaller IT, server, management, guest, and branch networks were assigned appropriately sized subnets.

Point-to-point routed links use /30 networks to minimize address waste.
