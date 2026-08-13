# Verification and Test Results

Do not mark a test as Pass until it has been executed. Add the evidence filename for every completed test.

| ID | Source | Destination/Check | Expected | Actual | Result | Evidence |
|---|---|---|---|---|---|---|
| TC-01 | SW-CORE-L3 | `show vlan brief` | VLANs 10-60, 99, 999 exist | Required VLANs are active and Fa0/24 is assigned to VLAN 50 | Pass | [View evidence](../screenshots/tc-01-vlan-brief.png) |
| TC-02 | SW-CORE-L3 | `show interfaces trunk` | Three active trunks | Fa0/1, Fa0/2 and Fa0/3 are trunking with native VLAN 999 | Pass | [View evidence](../screenshots/tc-02-trunk-status.png)  |
| TC-03 | IT-PC-01 | Default gateway 192.168.10.1 | Reachable | Gateway replied successfully with 0% packet loss | Pass | [View evidence](../screenshots/tc-17-inter-vlan-connectivity.png) |
| TC-04 | IT-PC-01 and GUEST-PC-01 | Centralized DHCP | Clients receive addresses from their corresponding VLAN pools | IT received `192.168.10.101/24` and Guest received `192.168.60.100/24` from `192.168.50.10` with the correct gateways and DNS server | Pass | [IT lease](../screenshots/tc-04-it-dhcp-lease.png), [Guest lease](../screenshots/tc-04-guest-dhcp-lease.png) |
| TC-05 | EMP-PC-01 | `intranet.company.local` | DNS resolves and HTTP opens | DNS resolved the hostname to `192.168.50.10` and the internal portal loaded successfully | Pass | [DNS evidence](../screenshots/tc-05-dns-resolution.png), [HTTP evidence](../screenshots/tc-05-intranet-http.png) |
| TC-06 | IT-PC-01 and GUEST-PC-01 | `public.example.test` | Public server is reachable through edge routing and NAT | DNS resolved `198.51.100.10`, ICMP completed with 0% loss, and the public website loaded successfully | Pass | [Website](../screenshots/tc-06-public-web-access.png), [Traceroute](../screenshots/tc-20-public-traceroute.png) |
| TC-07 | R1-EDGE | NAT translation table | Dynamic translations appear for internal clients | IT and Guest private addresses were translated to `203.0.113.2` for ICMP and TCP traffic | Pass | [Translations](../screenshots/tc-07-nat-translations.png), [Statistics](../screenshots/tc-07-nat-statistics.png) |
| TC-08 | GUEST-PC-01 | SRV-INTERNAL `192.168.50.10` | Denied | DNS remained available, but ICMP and HTTP access to the internal server were blocked | Pass | [ICMP evidence](../screenshots/tc-08-guest-segmentation.png), [HTTP evidence](../screenshots/tc-08-guest-intranet-http-denied.png) |
| TC-09 | GUEST-PC-01 | SRV-PUBLIC `198.51.100.10` | Allowed | ICMP completed with 0% packet loss and the public website remained accessible | Pass | [View evidence](../screenshots/tc-09-guest-public-web-allowed.png) |
| TC-10 | HR-PC-01 | ACC-PC-01 | Denied | The HR gateway rejected traffic to the Accounting VLAN while internal server access remained available | Pass | [View evidence](../screenshots/tc-10-hr-isolation.png) |
| TC-11 | ACC-PC-01 | HR-PC-01 | Denied | The Accounting gateway rejected traffic to the HR VLAN while internal server access remained available | Pass | [View evidence](../screenshots/tc-11-accounting-isolation.png) |
| TC-12 | IT-PC-01 | SSH to SW-ACCESS-01 | Allowed | The IT client authenticated successfully through SSH version 2 and reached the privileged device prompt | Pass | [View evidence](../screenshots/tc-12-it-ssh-allowed.png) |
| TC-13 | HR-PC-01 | SSH to SW-ACCESS-01 | Denied | The connection was rejected before authentication by the VTY source restriction | Pass | [View evidence](../screenshots/tc-13-hr-ssh-denied.png) |
| TC-14 | SW-ACCESS-01 | Port Security status | Secure-up with one sticky MAC per used access port | Fa0/1 and Fa0/2 remained secure-up with one learned SecureSticky address and restrict mode enabled | Pass | [View evidence](../screenshots/tc-14-port-security-status.png) |
| TC-15 | SW-CORE-L3 | `show ip interface brief` | VLAN 10-60 and 99 SVIs are up/up | Seven routed SVIs are operational with their assigned gateway addresses | Pass | [View evidence](../screenshots/tc-15-svi-status.png) |
| TC-16 | SW-CORE-L3 | `show ip route` | Department networks appear as connected routes | Seven VLAN networks are installed as directly connected routes | Pass | [View evidence](../screenshots/tc-16-connected-routes.png) |
| TC-17 | IT-PC-01 | HR-PC-01 and SRV-INTERNAL | Inter-VLAN destinations are reachable | IT-PC-01 reached VLAN 20 and VLAN 50 with 0% packet loss | Pass | [View evidence](../screenshots/tc-17-inter-vlan-connectivity.png) |
| TC-18 | SW-CORE-L3 | Core routing table | A default route points to `10.255.255.1` | All VLAN routes are connected and the default route points to R1-EDGE | Pass | [View evidence](../screenshots/tc-18-core-routing-table.png) |
| TC-19 | R1-EDGE | Edge routing table | Internal summary and ISP default routes exist | `192.168.0.0/16` points to the core and `0.0.0.0/0` points to `203.0.113.1` | Pass | [View evidence](../screenshots/tc-19-edge-routing-table.png) |
| TC-20 | IT-PC-01 | `tracert 198.51.100.10` | Trace crosses the core, edge, ISP, and public server | Four hops completed through `192.168.10.1`, `10.255.255.1`, `203.0.113.1`, and `198.51.100.10` | Pass | [View evidence](../screenshots/tc-20-public-traceroute.png) |
| TC-21 | SW-CORE-L3 | `show access-lists` | Permit and deny counters increase for tested traffic | Guest, HR, and Accounting ACL entries matched the expected DHCP, DNS, private-deny, and public-permit traffic | Pass | [View evidence](../screenshots/tc-21-acl-counters.png) |
| TC-22 | SW-ACCESS-01 | SSH version and VTY ACL | SSHv2 is enabled and only VLAN 10 is permitted | SSH version 2 was active and both permit and deny ACL counters increased during testing | Pass | [View evidence](../screenshots/tc-22-ssh-v2-acl.png) |
| TC-23 | IT-PC-01 | Telnet to SW-ACCESS-01 | Denied | Telnet was rejected because the VTY lines accept only SSH | Pass | [View evidence](../screenshots/tc-23-telnet-denied.png) |
| TC-24 | Unauthorized test endpoint | SW-ACCESS-01 Fa0/1 | Traffic is dropped and the violation counter increases | The unauthorized MAC address was rejected while the port remained secure-up in restrict mode | Pass | [View evidence](../screenshots/tc-24-port-security-violation.png) |
| TC-25 | SW-ACCESS-01 | Unused interface status | Unused ports are assigned to VLAN 999 and administratively disabled | Fa0/3-Fa0/24 and G0/2 were disabled while the PC ports and G0/1 uplink remained operational | Pass | [View evidence](../screenshots/tc-25-access-unused-ports.png) |
| TC-26 | SW-CORE-L3 | Black-hole VLAN assignment | Unused core ports are assigned to VLAN 999 without affecting active links | Fa0/4-Fa0/23 and G0/2 were placed in VLAN 999 while Fa0/24 remained in VLAN 50 and trunk links remained operational | Pass | [View evidence](../screenshots/tc-26-core-blackhole-vlan.png) |

## Troubleshooting Record

For each real issue, record:

### TR-01 – Public Hostname Could Not Be Resolved

- **Symptom:** `IT-PC-01` could reach `198.51.100.10` by IP address, but `nslookup public.example.test` returned `Non-existent domain` and the web browser displayed `Host Name Unresolved`.
- **Affected service:** Internal DNS name resolution.
- **Diagnostic commands:** `ping 198.51.100.10`, `nslookup public.example.test`, and `ipconfig /all`.
- **Root cause:** The DNS A record for `public.example.test` was mistakenly created on `SRV-PUBLIC`, while all clients were configured to use `SRV-INTERNAL` at `192.168.50.10` as their DNS server.
- **Fix:** Removed the incorrect DNS configuration from `SRV-PUBLIC` and created the A record `public.example.test → 198.51.100.10` on `SRV-INTERNAL`.
- **Verification:** `nslookup` returned `198.51.100.10`, hostname-based ping completed with 0% packet loss, and the public website loaded successfully by hostname.

### Issue ID

- Symptom:
- Affected devices:
- Diagnostic commands:
- Root cause:
- Fix:
- Verification after fix:
- Evidence:

