# Interface Map

Update this document whenever a Packet Tracer device uses a different interface from the planned design.

| Local Device | Local Interface | Remote Device | Remote Interface | Mode/Network | Status |
|---|---|---|---|---|---|
| R2-ISP | G0/0 | R1-EDGE | G0/0 | 203.0.113.0/30 | Planned |
| R2-ISP | G0/1 | SRV-PUBLIC | Fa0 | 198.51.100.0/24 | Planned |
| R1-EDGE | G0/1 | SW-CORE-L3 | G0/1 | 10.255.255.0/30 | Planned |
| SW-CORE-L3 | Fa0/1 | SW-ACCESS-01 | G0/1 | 802.1Q trunk | Planned |
| SW-CORE-L3 | Fa0/2 | SW-ACCESS-02 | G0/1 | 802.1Q trunk | Planned |
| SW-CORE-L3 | Fa0/3 | SW-ACCESS-03 | G0/1 | 802.1Q trunk | Planned |
| SW-CORE-L3 | Fa0/24 | SRV-INTERNAL | Fa0 | VLAN 50 access | Planned |
| SW-ACCESS-01 | Fa0/1 | IT-PC-01 | Fa0 | VLAN 10 access | Planned |
| SW-ACCESS-01 | Fa0/2 | HR-PC-01 | Fa0 | VLAN 20 access | Planned |
| SW-ACCESS-02 | Fa0/1 | ACC-PC-01 | Fa0 | VLAN 30 access | Planned |
| SW-ACCESS-02 | Fa0/2 | EMP-PC-01 | Fa0 | VLAN 40 access | Planned |
| SW-ACCESS-03 | Fa0/1 | GUEST-PC-01 | Fa0 | VLAN 60 access | Planned |

