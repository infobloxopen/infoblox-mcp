---
description: Find critical assets Tenable cannot identify and check whether Infoblox DNS/DHCP can fill the gap
argument-hint: <subnet name or CIDR>
---
Which critical assets in $ARGUMENTS have no hostname or MAC address
recorded in Tenable? For each, check whether Infoblox can supply that missing
identity from DNS or DHCP. Show me a table of what each system knows.

Use the infoblox MCP server for DNS and DHCP data and the tenable MCP server for
assets. Read-only: do not make any changes.
