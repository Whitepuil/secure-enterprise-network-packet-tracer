# Business and Technical Requirements

## Scenario

A small company needs a structured network for IT, Human Resources, Accounting, general employees, internal servers, guests, and network-management traffic.

## Business Requirements

- [X] Department traffic is separated.
- [X] Users receive addressing automatically.
- [X] Internal name resolution and web services are available.
- [X] Business users can access a simulated public network.
- [ ] Guest devices cannot access internal private networks.
- [ ] Only IT administrators can remotely manage network devices.

## Technical Requirements

- [X] A multilayer core switch performs inter-VLAN routing.
- [X] Access switches connect endpoints through access VLANs.
- [X] Trunks carry approved VLANs between access and core layers.
- [X] A central server provides DHCP, DNS, and HTTP.
- [X] An edge router provides default routing and NAT overload.
- [ ] Extended ACLs enforce departmental access policy.
- [ ] SSH replaces Telnet for device administration.
- [ ] Port Security protects user-facing access ports.
- [ ] Unused ports are assigned to VLAN 999 and administratively disabled.

## Acceptance Criteria

Every requirement must have at least one repeatable test in `test-results.md`.

