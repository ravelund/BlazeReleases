# Blaze 2026.11.27

**Blaze 2026.11.25 plus NetBlaze 2026.11.17 (NSX Federation firewall) and DnsBlaze 2026.11.17: PowerDNS gets Debian's security updates (a thin layer over each PowerDNS
image), so the 125 fixable vulnerabilities in the PowerDNS images are gone. BlazeManager 2026.11.15.06.**
DnsBlaze and NetBlaze built and Trivy-scanned on the build server (PowerDNS fixable HIGH/CRITICAL 125 → 0; NetBlaze 0), signed
on the release PC.
Tested in the lab: the two-server lab stack updated DnsBlaze 2026.11.15.02 → 2026.11.17 (the follower first, the leader last),
DNS probed every 3 seconds on both nodes during the update (one node always answered: ~15 s and ~20 s each), zones and the
resolver answering on both nodes afterwards, ProxyBlaze export identical, sign-in by pressing DnsBlaze's button 6× for a
normal user and a user in 1,000 groups, all 19 DnsBlaze pages with no CSP violation or console error; then NetBlaze 2026.11.16.02 → 2026.11.17, healthy, 31 pages
clean, 6/6 sign-ins for both users.

| Tool | Version | sha256 |
|---|---|---|
| AuthBlaze | 2026.11.15 | 549796e5e1515329116468b5695ad7ee4cb70d4fab5c509f263c88cdb18be211 |
| ProxyBlaze | 2026.11.15.04 | efbdb73723eb0aa34322312be20e836b38fd2134de36c4c43833614f278a16dc |
| CertBlaze | 2026.11.15.03 | f22f282533ec7e29126a2f10b1d3a5da85bf3eee8d14883f188cbb9ade51a08b |
| NetBlaze | 2026.11.17 | 64e3b98bab00cfb45d11b5a32858de8699988003ec7b46ed6bc62f267da19c48 |
| APMBlaze | 2026.11.15.03 | 056a80095904a64e38ff87df7b97f68ff131981872d29711e93dd30cf7e1a584 |
| DnsBlaze | 2026.11.17 | 6cd84468da28f7087588344762d1c040d0dd088bd67af0735eee9e948e8f57fb |
| TowerBlaze (updates only, not a stack tool) | 2026.11.15.06 | 064867b0b02c9761bd880d4be7a807512f0629652ec9cebd3f561e4bd8f1ecbc |

Bundles: NetBlaze 2026.11.17 and DnsBlaze 2026.11.17 on this release (`v2026.11.27`, with their .sha256); every other bundle on
`v2026.11.19`.

BlazeManager: **2026.11.15.06** or later (204aeb17a94a03e87e2b19a07f178d7e0cbd3f29f9126042a84ebcb3197eeef1).
**Update BlazeManager first**, then **Servers → Update the runner** on every other server (one click does both steps:
the new launcher, then the .NET runner; the server card's runner then lists "dotnet"), then the stack (Stack page →
Update the whole stack to Blaze 2026.11.27). A joined server whose runner is behind blocks the stack update until then.

This is the GitHub copy of the internally published Blaze 2026.11.26 (same bundles, same sha256; its own number because
the signed links differ).

## What's new since 2026.11.25 (the previous GitHub stack)

**NetBlaze 2026.11.17:** NSX Federation firewall: on a Local Manager, the global DFW and gateway policies, groups,
services, the exclusion list and the global gateway scope are read and shown beside the local ones (read-only, as before).

**DnsBlaze 2026.11.17:**
- Each PowerDNS image (Authoritative, Recursor, dnsdist) ships as DnsBlaze's own thin layer over the upstream image:
  Debian's security updates applied, the editor (vim, xxd) removed, the upstream user kept. PowerDNS itself is unmodified.
  Each DnsBlaze release rebuilds the layers, so they pick up Debian's fixes. Tags: dnsblaze-pdns-auth:5.1.5-…,
  dnsblaze-pdns-recursor:5.4.9-…, dnsblaze-dnsdist:2.1.3-….
- PowerDNS Recursor 5.4.8 → 5.4.9.
- **During the update each node restarts its DNS for 15–20 seconds** (followers first). Clients with both DnsBlaze servers
  configured keep resolving; a client with only one sees that pause.

Everything else as in Blaze 2026.11.25.

## Not in this stack

DHCPBlaze 2026.11.15.01 and BlazeOS 2026.11.15.01 (their own releases). PostgreSQL 17-alpine still carries upstream's `gosu`
built with an older Go (scanners flag it; it only runs at container start).
