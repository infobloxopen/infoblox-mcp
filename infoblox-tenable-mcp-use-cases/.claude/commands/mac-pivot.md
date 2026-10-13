---
description: Identify the device behind a MAC address in Infoblox DHCP and pull its full Tenable vulnerability posture
argument-hint: <MAC address>
---
Infoblox has a DHCP reservation for MAC $ARGUMENTS. Work out which
device that is, then pull its full vulnerability posture from Tenable.

Use the infoblox MCP server for DHCP and DNS data and the tenable MCP server for
the asset and its findings. Read-only: do not make any changes.
