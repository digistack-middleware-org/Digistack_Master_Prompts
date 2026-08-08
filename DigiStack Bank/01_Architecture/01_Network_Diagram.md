ID: ARCHVIZ01
Version: 0.1 (scaffold)
Status: Not Populated

Title: Network Diagram

Imports:
STD (§Ports, §IP/VLAN Addressing)
SOE01 (§8 Firewall)

Populate At: P01 v1 (basic subnet/IP scheme), extended at P01 v8 (IHS
web-tier), P01 v11/v12 (SSL/mTLS hops)

---

## What this will show
Subnet/VLAN layout (DMZ/App/Data zones per STD), IP addressing per host,
firewall allow-rules per SOE01 §8, as a Mermaid or ASCII network diagram.

## Status
Not yet populated. P01 v1 is signed off but this diagram has not been
drawn yet — populate once you're ready, referencing STD's existing
IP/VLAN Addressing table and Ports table directly (no new data to invent,
just visualize what STD already defines).