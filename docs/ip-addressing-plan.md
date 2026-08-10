# IP Addressing Plan

## VLAN Networks

| VLAN | Name | Network | Gateway | Allocation |
|---:|---|---|---|---|
| 10 | IT | 192.168.10.0/24 | 192.168.10.1 | DHCP from .100 |
| 20 | HR | 192.168.20.0/24 | 192.168.20.1 | DHCP from .100 |
| 30 | ACCOUNTING | 192.168.30.0/24 | 192.168.30.1 | DHCP from .100 |
| 40 | EMPLOYEES | 192.168.40.0/24 | 192.168.40.1 | DHCP from .100 |
| 50 | SERVERS | 192.168.50.0/24 | 192.168.50.1 | Static |
| 60 | GUEST | 192.168.60.0/24 | 192.168.60.1 | DHCP from .100 |
| 99 | MANAGEMENT | 192.168.99.0/24 | 192.168.99.1 | Static |

## Transit and Public Networks

| Network | Purpose | Addresses |
|---|---|---|
| 10.255.255.0/30 | Core-to-edge transit | R1 .1, Core .2 |
| 203.0.113.0/30 | Customer-to-ISP simulation | ISP .1, R1 .2 |
| 198.51.100.0/24 | Simulated public server network | ISP .1, Server .10 |

## Static Infrastructure Addresses

| Device | Address | Gateway |
|---|---|---|
| SRV-INTERNAL | 192.168.50.10/24 | 192.168.50.1 |
| SW-ACCESS-01 | 192.168.99.11/24 | 192.168.99.1 |
| SW-ACCESS-02 | 192.168.99.12/24 | 192.168.99.1 |
| SW-ACCESS-03 | 192.168.99.13/24 | 192.168.99.1 |
| SRV-PUBLIC | 198.51.100.10/24 | 198.51.100.1 |

