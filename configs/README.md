# Sanitized Device Configurations

Export `show running-config` from each device after final verification:

- `R1-EDGE.txt`
- `R2-ISP.txt`
- `SW-CORE-L3.txt`
- `SW-ACCESS-01.txt`
- `SW-ACCESS-02.txt`
- `SW-ACCESS-03.txt`

Replace all secrets and passwords with `<REDACTED>` before committing. Keep VLANs, documentation IP ranges, routes, ACLs, interface descriptions, DHCP relay, NAT, and SSH policy so reviewers can evaluate the implementation.

