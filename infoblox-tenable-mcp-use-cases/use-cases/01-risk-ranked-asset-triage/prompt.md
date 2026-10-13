# Prompt: Risk-Ranked Asset Triage

Replace `<SUBNET>` with an Infoblox IPAM subnet name (for example `engineering subnet`) or a CIDR (for example `10.20.30.0/24`). Replace `<N>` with how many assets to enrich.

```text
Which devices in our <SUBNET> have the highest asset criticality and
active critical vulnerabilities? Rank using the findings index for active
criticals rather than asset-level counters. For the top <N>, look up each one's
network identity in Infoblox — DNS record, DHCP reservation and MAC. One table:
hostname, IP, MAC per Tenable, MAC per Infoblox, identity status, ACR, and the
most serious active critical finding — prefer a named CVE over a rollup KB
where one exists. Where Tenable has no MAC or hostname, mark identity
"Infoblox-only".
```
