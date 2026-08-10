# Verification and Test Results

Do not mark a test as Pass until it has been executed. Add the evidence filename for every completed test.

| ID | Source | Destination/Check | Expected | Actual | Result | Evidence |
|---|---|---|---|---|---|---|
| TC-01 | SW-CORE-L3 | `show vlan brief` | VLANs 10-60, 99, 999 exist | Pending | Pending | Pending |
| TC-02 | SW-CORE-L3 | `show interfaces trunk` | Three active trunks | Pending | Pending | Pending |
| TC-03 | IT-PC-01 | Default gateway | Reachable | Pending | Pending | Pending |
| TC-04 | HR-PC-01 | DHCP | Receives 192.168.20.0/24 lease | Pending | Pending | Pending |
| TC-05 | EMP-PC-01 | `intranet.company.local` | DNS resolves and HTTP opens | Pending | Pending | Pending |
| TC-06 | EMP-PC-01 | SRV-PUBLIC | Reachable through NAT | Pending | Pending | Pending |
| TC-07 | R1-EDGE | NAT table | Dynamic translation appears | Pending | Pending | Pending |
| TC-08 | GUEST-PC-01 | SRV-INTERNAL | Denied | Pending | Pending | Pending |
| TC-09 | GUEST-PC-01 | SRV-PUBLIC | Allowed | Pending | Pending | Pending |
| TC-10 | HR-PC-01 | ACC-PC-01 | Denied | Pending | Pending | Pending |
| TC-11 | ACC-PC-01 | HR-PC-01 | Denied | Pending | Pending | Pending |
| TC-12 | IT-PC-01 | SSH to SW-ACCESS-01 | Allowed | Pending | Pending | Pending |
| TC-13 | HR-PC-01 | SSH to SW-ACCESS-01 | Denied | Pending | Pending | Pending |
| TC-14 | Access switch | Port Security status | Secure-up on used access ports | Pending | Pending | Pending |

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

