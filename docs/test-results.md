# Verification and Test Results

Do not mark a test as Pass until it has been executed. Add the evidence filename for every completed test.

| ID | Source | Destination/Check | Expected | Actual | Result | Evidence |
|---|---|---|---|---|---|---|
| TC-01 | SW-CORE-L3 | `show vlan brief` | VLANs 10-60, 99, 999 exist | Required VLANs are active and Fa0/24 is assigned to VLAN 50 | Pass | [View evidence](../screenshots/tc-01-vlan-brief.png) |
| TC-02 | SW-CORE-L3 | `show interfaces trunk` | Three active trunks | Fa0/1, Fa0/2 and Fa0/3 are trunking with native VLAN 999 | Pass | [View evidence](../screenshots/tc-02-trunk-status.png)  |
| TC-03 | IT-PC-01 | Default gateway 192.168.10.1 | Reachable | Gateway replied successfully with 0% packet loss | Pass | [View evidence](../screenshots/tc-17-inter-vlan-connectivity.png) |
| TC-04 | IT-PC-01 and GUEST-PC-01 | Centralized DHCP | Clients receive addresses from their corresponding VLAN pools | IT received `192.168.10.101/24` and Guest received `192.168.60.100/24` from `192.168.50.10` with the correct gateways and DNS server | Pass | [IT lease](../screenshots/tc-04-it-dhcp-lease.png), [Guest lease](../screenshots/tc-04-guest-dhcp-lease.png) |
| TC-05 | EMP-PC-01 | `intranet.company.local` | DNS resolves and HTTP opens | DNS resolved the hostname to `192.168.50.10` and the internal portal loaded successfully | Pass | [DNS evidence](../screenshots/tc-05-dns-resolution.png), [HTTP evidence](../screenshots/tc-05-intranet-http.png) |
| TC-06 | EMP-PC-01 | SRV-PUBLIC | Reachable through NAT | Pending | Pending | Pending |
| TC-07 | R1-EDGE | NAT table | Dynamic translation appears | Pending | Pending | Pending |
| TC-08 | GUEST-PC-01 | SRV-INTERNAL | Denied | Pending | Pending | Pending |
| TC-09 | GUEST-PC-01 | SRV-PUBLIC | Allowed | Pending | Pending | Pending |
| TC-10 | HR-PC-01 | ACC-PC-01 | Denied | Pending | Pending | Pending |
| TC-11 | ACC-PC-01 | HR-PC-01 | Denied | Pending | Pending | Pending |
| TC-12 | IT-PC-01 | SSH to SW-ACCESS-01 | Allowed | Pending | Pending | Pending |
| TC-13 | HR-PC-01 | SSH to SW-ACCESS-01 | Denied | Pending | Pending | Pending |
| TC-14 | Access switch | Port Security status | Secure-up on used access ports | Pending | Pending | Pending |
| TC-15 | SW-CORE-L3 | `show ip interface brief` | VLAN 10-60 and 99 SVIs are up/up | Seven routed SVIs are operational with their assigned gateway addresses | Pass | [View evidence](../screenshots/tc-15-svi-status.png) |
| TC-16 | SW-CORE-L3 | `show ip route` | Department networks appear as connected routes | Seven VLAN networks are installed as directly connected routes | Pass | [View evidence](../screenshots/tc-16-connected-routes.png) |
| TC-17 | IT-PC-01 | HR-PC-01 and SRV-INTERNAL | Inter-VLAN destinations are reachable | IT-PC-01 reached VLAN 20 and VLAN 50 with 0% packet loss | Pass | [View evidence](../screenshots/tc-17-inter-vlan-connectivity.png) |

## Troubleshooting Record

For each real issue, record:

### Issue ID

- Symptom:
- Affected devices:
- Diagnostic commands:
- Root cause:
- Fix:
- Verification after fix:
- Evidence:

