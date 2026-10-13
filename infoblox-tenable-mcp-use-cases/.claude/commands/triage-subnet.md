---
description: Rank devices in a subnet by ACR and active critical findings, enriched with Infoblox network identity
argument-hint: <subnet name or CIDR>
---
Which devices in $ARGUMENTS have the highest asset criticality and
active critical vulnerabilities? Rank using the Tenable findings index for active
criticals rather than asset-level counters. For the top 5, look up each one's
network identity in Infoblox — DNS record, DHCP reservation and MAC. One table:
hostname, IP, MAC per Tenable, MAC per Infoblox, identity status, ACR, and the
most serious active critical finding — prefer a named CVE over a rollup KB
where one exists. Where Tenable has no MAC or hostname, mark identity
"Infoblox-only".

Use the infoblox MCP server for IPAM, DNS and DHCP data and the tenable MCP
server for assets and findings. Read-only: do not make any changes.
