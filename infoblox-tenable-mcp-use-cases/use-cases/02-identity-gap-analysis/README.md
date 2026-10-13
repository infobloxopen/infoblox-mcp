# Use Case 2: Identity Gap Analysis

Find critical assets that Tenable can't fully identify, with no hostname or MAC address, and check whether Infoblox DNS and DHCP can supply the missing identity.

| | |
|---|---|
| Starts from | Subnet name or CIDR |
| Tenable provides | Assets missing a hostname or MAC, OS fingerprint, ACR |
| Infoblox provides | DNS A records and DHCP reservation MACs for those IPs |
| Output | A side-by-side table of what each system knows, plus root-cause analysis |
| Lab run | About 32 tool calls across both servers |

## Prompt

```text
Which critical assets in the engineering subnet have no hostname or MAC address
recorded in Tenable? For each, check whether Infoblox can supply that missing
identity from DNS or DHCP. Show me a table of what each system knows.
```

A copy-paste version with placeholders is in [prompt.md](prompt.md).

## Run It

```text
/identity-gaps Engineering Subnet
```

## What Happens

1. Tenable: lists assets in the subnet with no MAC and/or no hostname or FQDN. The lab had 12 with no MAC, and 9 of those also had no hostname.
2. Infoblox: for each IP, looks up DNS A records and DHCP fixed-address reservations.
3. Classifies each asset: name and MAC supplied by Infoblox, FQDN and MAC supplied by Infoblox, or unresolved in both systems.
4. Looks for a pattern behind the gaps. In the lab, the cause was missing scan credentials.

## Sample Output

See [sample-output.md](sample-output.md).

<!-- TODO: add images/output.png (Claude Code screenshot from the use case PDF, page 3) -->

## Why It Matters

Gaps are findings too. In the lab run:

* Infoblox closed the identity gap on 6 of the 12 assets.
* The highest-criticality unresolved asset was an ESXi 9.0 hypervisor that neither system could name. That's the one to chase first.
* The likely subnet gateway and firewall couldn't be attributed by either system.
* The root cause was credential coverage, not data quality. Every one of the 12 was an unauthenticated scan target.

## Adapt It

* Reverse the question: "Which Infoblox DHCP reservations in this subnet have no matching Tenable asset?"
* Add ownership: "...and include the reservation comment or owner from Infoblox so we know who to ask for scan credentials."
