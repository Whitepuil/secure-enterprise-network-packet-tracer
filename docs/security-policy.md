# Security Policy

## Access-Control Matrix

| Source | Destination | Service | Policy | Reason |
|---|---|---|---|---|
| IT | Device management IPs | SSH | Allow | Authorized administration |
| HR | Device management IPs | SSH | Deny | Non-administrative network |
| Accounting | Device management IPs | SSH | Deny | Non-administrative network |
| Guest | Device management IPs | Any | Deny | Untrusted network |
| HR | Accounting user network | Any | Deny | Department isolation |
| Accounting | HR user network | Any | Deny | Department isolation |
| Guest | Internal DNS | DNS | Allow | Required name resolution |
| Guest | Other private networks | Any | Deny | Protect internal resources |
| Guest | Simulated public network | Any | Allow | Guest Internet access |

## Device Hardening

- [X] Telnet is not allowed.
- [X] SSH is restricted to VLAN 10.
- [X] Privileged mode uses an enable secret.
- [X] User-facing ports use Port Security.
- [X] Unused ports are placed in VLAN 999 and shut down.
- [X] Device configurations exported to GitHub have secrets redacted.

