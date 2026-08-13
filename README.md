# Secure Segmented Enterprise Network in Cisco Packet Tracer

> Status: Planning and implementation in progress.

## Overview

This project designs and implements a secure, segmented network for a small enterprise using Cisco Packet Tracer. Business departments are separated with VLANs, routed through a multilayer core switch, provided centralized network services, and protected by access-control and device-hardening measures.

The repository documents the complete workflow from requirements and addressing design to device configuration, verification, troubleshooting, and final test evidence.

## Business Requirements

- Separate IT, Human Resources, Accounting, Employees, Servers, Guest, and Management traffic.
- Automatically assign client addressing through centralized DHCP.
- Provide internal DNS and web services.
- Simulate controlled Internet access through an ISP and NAT overload.
- Prevent Guest devices from reaching internal private networks.
- Isolate Human Resources and Accounting user networks.
- Allow SSH device administration only from the IT VLAN.
- Protect access ports and disable unused switch ports.

## Architecture

The topology follows a hierarchical enterprise design with an access layer,
a multilayer core switch, an enterprise edge router, and a simulated ISP
network. The detailed cabling and interface assignments are documented in
[`docs/interface-map.md`](docs/interface-map.md).

![Physical network topology](diagrams/physical-topology.png)

## Device Inventory

| Quantity | Device | Role |
|---:|---|---|
| 1 | Cisco 2911 | Enterprise edge router and NAT gateway |
| 1 | Cisco 2911 | Simulated ISP router |
| 1 | Cisco 3560 multilayer switch | Core routing and VLAN gateways |
| 3 | Cisco 2960 switches | User access layer |
| 1 | Server-PT | DHCP, DNS, and internal HTTP services |
| 1 | Server-PT | Simulated public web server |
| 5 | PC-PT | Department and Guest test endpoints |

## VLAN and Addressing Plan

| VLAN | Name | Network | Gateway |
|---:|---|---|---|
| 10 | IT | 192.168.10.0/24 | 192.168.10.1 |
| 20 | HR | 192.168.20.0/24 | 192.168.20.1 |
| 30 | ACCOUNTING | 192.168.30.0/24 | 192.168.30.1 |
| 40 | EMPLOYEES | 192.168.40.0/24 | 192.168.40.1 |
| 50 | SERVERS | 192.168.50.0/24 | 192.168.50.1 |
| 60 | GUEST | 192.168.60.0/24 | 192.168.60.1 |
| 99 | MANAGEMENT | 192.168.99.0/24 | 192.168.99.1 |
| 999 | BLACKHOLE | Not routed | None |

## Technologies and Concepts

The following items must only be marked as completed after successful implementation and verification:

- [X] VLANs and access ports
- [X] IEEE 802.1Q trunking
- [X] Inter-VLAN routing with SVIs
- [X] Centralized DHCP and DHCP relay
- [X] Internal DNS and HTTP services
- [X] Static and default routing
- [X] NAT overload/PAT
- [X] Extended ACLs
- [ ] SSH management restriction
- [ ] Switch port security
- [ ] Unused-port shutdown and black-hole VLAN

## Security Policy

| Source | Destination | Policy |
|---|---|---|
| IT | Network management interfaces | Allow SSH |
| Non-IT user VLANs | Network management interfaces | Deny SSH |
| HR | Accounting user network | Deny |
| Accounting | HR user network | Deny |
| Guest | Internal private networks | Deny, except required DNS |
| Guest | Simulated public network | Allow |
| Business VLANs | Internal DNS and web services | Allow |

## Verification Summary

Test results will be published in [`docs/test-results.md`](docs/test-results.md) after each control is implemented.
### Completed Layer 2 Verification

- VLANs 10, 20, 30, 40, 50, 60, 99, and 999 were created successfully.
- Access ports were assigned to their intended departmental VLANs.
- Three IEEE 802.1Q trunks are operational between the core and access switches.
- VLAN 999 is used as the native VLAN on trunk links.

![VLAN verification](screenshots/tc-01-vlan-brief.png)

![Trunk verification](screenshots/tc-02-trunk-status.png)

### Completed Layer 3 Verification

- Seven switched virtual interfaces were configured as departmental default gateways.
- IP routing was enabled on the multilayer core switch.
- The core switch learned all departmental networks as directly connected routes.
- Management VLAN connectivity was verified between the core and access switches.
- Inter-VLAN connectivity was successfully tested between the IT, HR, and Server VLANs.
- No access-control lists are applied at this stage, so inter-VLAN traffic is temporarily permitted for baseline verification.

![SVI status](screenshots/tc-15-svi-status.png)

![Connected routes](screenshots/tc-16-connected-routes.png)

![Inter-VLAN connectivity](screenshots/tc-17-inter-vlan-connectivity.png)

### Completed Network Services Verification

- A centralized DHCP server was deployed in VLAN 50 at `192.168.50.10`.
- Five DHCP pools were created for the IT, HR, Accounting, Employees, and Guest VLANs.
- DHCP relay was configured on the corresponding core switch SVIs.
- Clients in different VLANs successfully received the correct IPv4 address, subnet mask, default gateway, and DNS server.
- Internal DNS successfully resolved `intranet.company.local` to `192.168.50.10`.
- The internal HTTP portal was successfully accessed by hostname from a client VLAN.
- Guest access was temporarily permitted during baseline service verification and was later restricted by the access-control policies documented below.

![IT DHCP lease](screenshots/tc-04-it-dhcp-lease.png)

![Guest DHCP lease](screenshots/tc-04-guest-dhcp-lease.png)

![Internal DNS resolution](screenshots/tc-05-dns-resolution.png)

![Internal HTTP portal](screenshots/tc-05-intranet-http.png)

### Completed Edge and Internet Verification

- A routed `/30` transit link was configured between the multilayer core switch and the enterprise edge router.
- Static summary and default routes were configured to provide bidirectional internal routing and simulated ISP connectivity.
- NAT overload translated private departmental addresses to the edge router address `203.0.113.2`.
- Dynamic ICMP and TCP translations were observed for clients in the IT and Guest VLANs.
- Internal DNS resolved `public.example.test` to the simulated public server at `198.51.100.10`.
- The public web service was successfully accessed from both trusted and Guest client VLANs.
- A four-hop traceroute verified the forwarding path through the core switch, edge router, ISP router, and public server.

![Public website access](screenshots/tc-06-public-web-access.png)

![NAT translations](screenshots/tc-07-nat-translations.png)

![NAT statistics](screenshots/tc-07-nat-statistics.png)

![Core routing table](screenshots/tc-18-core-routing-table.png)

![Edge routing table](screenshots/tc-19-edge-routing-table.png)

![Public traceroute](screenshots/tc-20-public-traceroute.png)

### Completed Access-Control Verification

- Extended inbound ACLs were applied close to the source on the Guest, HR, and Accounting SVIs.
- Guest clients are permitted to obtain DHCP leases and query the centralized DNS server.
- Guest traffic to RFC 1918 private networks is denied while simulated public-network access remains available.
- HR and Accounting user networks are mutually isolated.
- HR and Accounting clients retain access to approved internal services and the simulated public network.
- ACL match counters confirmed that permit and deny entries processed the expected test traffic.

![Guest segmentation](screenshots/tc-08-guest-segmentation.png)

![HR isolation](screenshots/tc-10-hr-isolation.png)

![Accounting isolation](screenshots/tc-11-accounting-isolation.png)

![ACL counters](screenshots/tc-21-acl-counters.png)

## Repository Structure

```text
.
├── packet-tracer/        Packet Tracer project versions and final lab
├── configs/              Sanitized device running configurations
├── docs/                 Requirements, addressing, policies, and test results
├── diagrams/             Logical and physical topology images
├── screenshots/          Verification evidence
├── PROJECT_GUIDE_VI.md   Detailed Vietnamese implementation guide
└── README.md             Public project documentation
```

## How to Use

1. Install Cisco Packet Tracer.
2. Download the final `.pkt` file from `packet-tracer/` after it is published.
3. Open the file and allow device links to converge.
4. Follow the test cases in `docs/test-results.md`.
5. Review sanitized configurations under `configs/`.

## Challenges and Troubleshooting

This section will document real implementation problems, diagnostic commands, root causes, and fixes. It will not be filled with hypothetical issues.

## Limitations

- This project is a Cisco Packet Tracer simulation, not a production deployment.
- Packet Tracer supports only a subset of Cisco IOS features.
- High availability, dynamic routing, wireless infrastructure, firewall appliances, SIEM integration, and centralized AAA are outside the first release.

## Future Improvements

- Centralized NTP and Syslog.
- AAA-based device authentication.
- Firewall integration.
- Redundant core and gateway design.
- Wazuh SIEM monitoring in a separate virtualized lab.

## Author

**Chau Thai Khang**  
Information Technology student, HUFLIT  
GitHub: [Whitepuil](https://github.com/Whitepuil)
