# Blaze 2026.11.3

A hotfix stack version: **Blaze 2026.11.1 with NetBlaze 2026.11.01**. Every other tool, BlazeManager 2026.10.31.08 and TowerBlaze
2026.10.31.06 are unchanged.

| Tool | Version | sha256 |
|---|---|---|
| NetBlaze | 2026.11.01 | b633e2fb5e663d55fc9f823105a3d1cd6e345632cd695bd47083e414b2650c92 |
| AuthBlaze | 2026.10.31 | f236aeb071813d63bb86ee8f1b98089ff17dce401722e2731220ec9028c7561d |
| ProxyBlaze | 2026.10.31.05 | 0873d070f28cd72aca3bc5dd63a48a78f23be71b78fc5ce460c96801ab0bfbfc |
| CertBlaze | 2026.10.31.01 | cca09af42d609d71337dfeefa3704ed76574cfa6c437ac018bcba3ae060cafc5 |
| APMBlaze | 2026.10.31.03 | 15618ad27fd7f1e0ae6211323afb69d26f9aba9ec015d51952fc392eb0f5020a |
| DnsBlaze | 2026.10.31.02 | ebb67f57620b9b9d2509b405b6d36e72166fd717fb36ab2a19a16a64036bb8bb |

Bundles: NetBlaze 2026.11.01 on this release `v2026.11.3`; the other tools are unchanged on release `v2026.11.1`.

BlazeManager: **2026.10.31.08** or later. Stack page → **Update the whole stack to Blaze 2026.11.3**: only NetBlaze is
upgraded.

## What's new in NetBlaze 2026.11.01

- **Where advertised: NSX segments are origins.** A segment that is exactly the prefix starts at the tier-0 on each
  edge node when the tier-0 redistributes it into BGP (TIER1_CONNECTED/TIER1_SEGMENT with the tier-1 advertising
  TIER1_CONNECTED; TIER0_* for its own segments); the tier-0's BGP sessions follow. NSX route maps and neighbour route
  filters show as Unknown; a missing advertisement or redistribution is named in a note. The NSX read collects more
  fields with the same GET calls.
- **Network map:** "?" boxes known only from a cable (LLDP neighbours NetBlaze doesn't read: servers, ESXi hosts) are
  hidden unless **Unread cable neighbours** is on. They still count for the status dot of the box they hang off.

## Upgrade note

- An NSX Manager's origins show as **Unknown** ("not read yet") until its next read (**Read now** or the scheduled
  refresh) after the upgrade. No install, compose, database or sign-in changes.

Tested in the lab 2026-11-01: BlazeManager 2026.10.31.08 updated only NetBlaze (on server 2), every ProxyBlaze pool
unchanged, sign-in 42/42 across 7 tools.
