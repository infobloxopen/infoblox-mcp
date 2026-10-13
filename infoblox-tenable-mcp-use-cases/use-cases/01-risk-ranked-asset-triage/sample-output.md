# Sample Output: Risk-Ranked Asset Triage

> Captured in a lab environment. Output is lightly cleaned up; wording varies between runs.

`10.20.30.0/24` "Engineering Subnet" (resolved from Infoblox IPAM), 57 Tenable assets. Ranked by ACR, then by active critical count from the findings index. Three ACR-8 assets (`ws-lnx01`, `ws-lnx02`, `10.20.30.26`) dropped out with zero active criticals.

| # | Hostname | IP | MAC per Tenable | MAC per Infoblox | Identity Status | ACR | Active Crits | Most Serious Active Critical |
|---|---|---|---|---|---|---|---|---|
| 1 | srv-app02 | 10.20.30.56 | 00:50:56:b1:2d:9e | 00:50:56:b1:2d:9e | Confirmed in both systems | 9 | 21 | WinVerifyTrust signature validation – CVE-2013-3900 (EnableCertPaddingCheck) |
| 2 | srv-lnx08.lab.example.com | 10.20.30.17 | 00:50:56:b1:3c:11 | – (no record) | Tenable-only | 8 | 23 | polkit pkexec local root – CVE-2021-4034 (PwnKit), via ELSA-2022-0267 |
| 3 | vc-mgmt01 | 10.20.30.39 | – | 00:50:56:b1:4a:01 | Infoblox-only | 8 | 1 | VMware vCenter 8.0 < 8.0 U3k directory traversal (VMSA-2026-0006) |
| 4 | hv-host01 | 10.20.30.54 | – | 00:50:56:b1:5b:01 | Infoblox-only | 8 | 1 | VMware ESXi 7.0/8.0 multiple vulnerabilities (VMSA-2025-0004) |
| 5 | ad-dc01 | 10.20.30.10 | 00:50:56:b1:7d:47 | 00:50:56:b1:7d:47 | Confirmed in both systems | 7 | 65 | Apache Log4j < 2.15.0 RCE – CVE-2021-44228 |

All five have an A record in `lab.example.com` and a MAC-matched DHCP fixed-address reservation in Infoblox, except `srv-lnx08`, which has neither.

## Analyst Notes

* The domain controller is the sharpest exposure. `ad-dc01` is Windows Server 2008 R2 with 65 active criticals, including Log4Shell. That's unauthenticated remote code execution on the host that owns authentication for the whole engineering zone. Compromising it is a direct path to domain-wide credential access.
* Both hypervisor management planes are reachable and unpatched. `vc-mgmt01` has a pre-authentication directory traversal and `hv-host01` has a hypervisor-escape-class vulnerability. An attacker who lands anywhere in the subnet can reach vCenter, and from there every guest it manages, including `ad-dc01`.
* `srv-lnx08` is the identity gap to close. It has ACR 8, 23 active criticals and unpatched PwnKit, but no Infoblox DNS record or reservation. It holds `10.20.30.17` outside IPAM's system of record, so it won't appear in any address-based inventory or policy scoped from Infoblox.
