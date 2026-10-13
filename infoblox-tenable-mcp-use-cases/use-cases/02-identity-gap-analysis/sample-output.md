# Sample Output: Identity Gap Analysis

> Captured in a lab environment. Output is lightly cleaned up; wording varies between runs.

Twelve assets in `10.20.30.0/24` have no MAC in Tenable, and nine of those also have no hostname or FQDN. Infoblox closes the gap on six.

| IP | ACR | Tenable: OS Fingerprint | Tenable: Hostname | Tenable: MAC | Infoblox: DNS A Record | Infoblox: DHCP Reservation MAC | Identity Resolution |
|---|---|---|---|---|---|---|---|
| 10.20.30.54 | 8 | VMware ESXi 8.0.3 | – | – | hv-host01.lab.example.com | 00:50:56:b1:5b:01 | Infoblox-only — name + MAC supplied |
| 10.20.30.39 | 8 | vCenter Server 8.0.2 | – | – | vc-mgmt01.lab.example.com | 00:50:56:b1:4a:01 | Infoblox-only — name + MAC supplied |
| 10.20.30.26 | 8 | VMware ESXi 9.0.0 | – | – | none | none | Unresolved |
| 10.20.30.33 | 5 | Linux Kernel 4.x | – | – | none | none | Unresolved |
| 10.20.30.65 | 4 | vCenter Server 7.0.3 | – | – | vc-mgmt02.lab.example.com | 00:50:56:b1:6c:01 | Infoblox-only — name + MAC supplied |
| 10.20.30.24 | 4 | Linux Kernel 6.12 (EL10) | – | – | none | none | Unresolved |
| 10.20.30.63 | 4 | none | – | – | none | none | Unresolved |
| 10.20.30.12 | N/A | SonicOS | – | – | none | none | Unresolved |
| 10.20.30.1 | N/A | none | – | – | none | none | Unresolved |
| 10.20.30.66 | N/A | Darwin | NetBIOS MAC-HOST1 only | – | mac-host1.lab.example.com | supplied | Infoblox supplies FQDN + MAC |
| 10.20.30.67 | N/A | Darwin | NetBIOS MAC-HOST2 only | – | mac-host2.lab.example.com | supplied | Infoblox supplies FQDN + MAC |
| 10.20.30.68 | N/A | Darwin | NetBIOS MAC-HOST3 only | – | mac-host3.lab.example.com | supplied | Infoblox supplies FQDN + MAC |

Only two of these assets have active critical findings: `10.20.30.54` (ESXi, VMSA-2025-0004) and `10.20.30.39` (vCenter, VMSA-2026-0006), with one each. The rest are clean or haven't been scanned.

On where the data came from: `vc-mgmt01`, `hv-host01` and `vc-mgmt02` exist as records in Infoblox only. They were seeded for this lab, not learned from live DNS or DHCP traffic. The IP and MAC join is real; those three identities were configured.

## Analyst Notes

* All three of the highest-criticality unidentified assets are hypervisor management interfaces. Infoblox names two of them. The third, `10.20.30.26` (ESXi 9.0, ACR 8), is missing from both systems: a hypervisor with no owner, no name and no reservation. It's the one to chase first.
* Six assets have no identity in either system, including `10.20.30.1` and `.12` (SonicOS). These are almost certainly the subnet gateway and firewall. Those devices define the blast radius, and neither system can attribute them.
* The pattern is credential coverage, not data quality. Every one of the twelve is an unauthenticated scan target; no authenticated scan has ever run against any of them. That's why Tenable has an OS guess but no hostname or MAC. Adding scan credentials for the ESXi and vCenter hosts would let Tenable resolve identity itself and improve finding fidelity in the same pass.
