# Use Case 1: Risk-Ranked Asset Triage

Rank the devices in a subnet by asset criticality and active critical vulnerabilities, then attach each device's authoritative network identity from Infoblox.

| | |
|---|---|
| Starts from | Subnet name or CIDR |
| Tenable provides | Assets in range, ACR, active critical findings from the findings index |
| Infoblox provides | Subnet resolution (name → CIDR), DNS A record, DHCP reservation, MAC address |
| Output | One ranked table plus analyst notes |
| Lab run | About 50 tool calls across both servers |

## Prompt

```text
Which devices in our engineering subnet have the highest asset criticality and
active critical vulnerabilities? Rank using the findings index for active
criticals rather than asset-level counters. For the top 5, look up each one's
network identity in Infoblox — DNS record, DHCP reservation and MAC. One table:
hostname, IP, MAC per Tenable, MAC per Infoblox, identity status, ACR, and the
most serious active critical finding — prefer a named CVE over a rollup KB
where one exists. Where Tenable has no MAC or hostname, mark identity
"Infoblox-only".
```

A copy-paste version with placeholders is in [prompt.md](prompt.md).

## Run It

```text
/triage-subnet Engineering Subnet
```

or

```text
/triage-subnet 10.20.30.0/24
```

## What Happens

1. Infoblox: resolves "engineering subnet" from IPAM to a CIDR (`10.20.30.0/24` in the lab).
2. Tenable: finds the assets in that range (57 in the lab).
3. Tenable: counts active critical findings per asset straight from the findings index. In the lab, three ACR-8 assets dropped out because they had zero active criticals.
4. Ranks by ACR, then by active critical count.
5. Infoblox: for the top five, looks up the DNS A record, the DHCP fixed-address reservation and the MAC.
6. Joins the two on IP and MAC, then labels each asset's identity status.
7. Returns one table and a short analysis.

## Sample Output

See [sample-output.md](sample-output.md).

<!-- TODO: add images/output.png (Claude Code screenshot from the use case PDF, page 2) -->

## Why It Matters

The ranking tells you what to fix first. Infoblox tells you whether you can trust what you're looking at. In the lab run, the analysis showed:

* a domain controller with Log4Shell, which is the sharpest exposure.
* hypervisor management planes that are reachable and unpatched.
* a high-criticality host with no DNS record or DHCP reservation at all. It won't appear in any address-based inventory or policy scoped from Infoblox.

## Adapt It

* Change `top 5` to a different number, or rank by VPR instead of ACR.
* Point it at a different subnet by name or CIDR.
* Add Infoblox Threat Defense context: "...and flag any of these hosts with DNS security events in the last 7 days."
