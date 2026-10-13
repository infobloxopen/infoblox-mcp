# Prompting Tips

The prompts in this repository follow a few patterns that make cross-platform answers more accurate and easier to check.

## 1. Say Which Data Source to Count From

> "Rank using the findings index for active criticals rather than asset-level counters."

Asset-level counters can lag behind. Asking for counts straight from the findings index changes the ranking. In use case 1, three assets with ACR 8 dropped out because they had no active critical findings.

## 2. Name the Join Keys

> "Look up each one's network identity in Infoblox — DNS record, DHCP reservation and MAC."

When you ask for both IP and MAC, the model confirms that an IP address still belongs to the same device. Matching on IP alone can miss reassigned addresses.

## 3. Say What to Do with Missing Data

> "Where Tenable has no MAC or hostname, mark identity 'Infoblox-only'."

Explicit labels such as *Confirmed in both systems*, *Tenable-only*, *Infoblox-only* and *Unresolved* make gaps easy to see. Without them, the model may drop incomplete rows.

## 4. Choose the Most Useful Finding

> "Prefer a named CVE over a rollup KB where one exists."

A cumulative-update KB number is less useful than a named CVE when you prioritize or communicate risk.

## 5. Ask for One Table Plus a Short Analysis

> "One table: hostname, IP, MAC per Tenable, MAC per Infoblox, identity status, ACR..."

Listing the columns you want keeps output consistent between runs and easy to paste into a ticket.

## 6. Ask Where Each Value Came From

Follow up with questions such as:

* "Which system did each value in this table come from?"
* "Which of these Infoblox records were learned from DHCP or DNS activity, and which were configured manually?"

## 7. Keep It Read-Only

Ask the model to propose actions, not carry them out:

> "Recommend remediation steps but do not make any changes."

## More Prompt Ideas (Not Yet Validated)

* "Are any IP addresses recently allocated by Infoblox DHCP in the engineering subnet tied to assets with a Tenable VPR of 9 or higher?"
* "Cross-reference assets with critical vulnerabilities in Tenable against Infoblox DNS security events, and show which vulnerable devices are contacting known malicious or command-and-control domains." (Requires Infoblox Threat Defense.)
* "List Tenable assets in this subnet that have never had an authenticated scan, together with their Infoblox DHCP reservation owner, so we can request scan credentials."
* "Which Infoblox DHCP reservations in this subnet have no matching Tenable asset?" This finds devices that may never have been scanned.
