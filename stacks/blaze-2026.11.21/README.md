# Blaze 2026.11.21

**Blaze 2026.11.18 plus NetBlaze 2026.11.16: small but important NetBlaze fixes. BlazeManager 2026.11.15.06.**
NetBlaze built and Trivy-scanned on the build server (0 HIGH/CRITICAL), signed on the release PC.
Tested in the lab (light test, NetBlaze's install files unchanged): the two-server lab stack on 2026.11.18 updated to this
stack, NetBlaze healthy, ProxyBlaze export identical, sign-in by pressing NetBlaze's button 6× for a normal user and a user
in 1,000 groups, all 30 NetBlaze pages with no CSP violation or console error.

| Tool | Version | sha256 |
|---|---|---|
| AuthBlaze | 2026.11.15 | 549796e5e1515329116468b5695ad7ee4cb70d4fab5c509f263c88cdb18be211 |
| ProxyBlaze | 2026.11.15.04 | efbdb73723eb0aa34322312be20e836b38fd2134de36c4c43833614f278a16dc |
| CertBlaze | 2026.11.15.03 | f22f282533ec7e29126a2f10b1d3a5da85bf3eee8d14883f188cbb9ade51a08b |
| NetBlaze | 2026.11.16 | d2bb26337d6260503d3879e74591b3458dda7b0fb8bd1ee673f6fb361797b208 |
| APMBlaze | 2026.11.15.03 | 056a80095904a64e38ff87df7b97f68ff131981872d29711e93dd30cf7e1a584 |
| DnsBlaze | 2026.11.15.02 | b6864049a5f6bacb872b9663de363a143b8cc0f0ff914b1f9a269687197d0fe5 |
| TowerBlaze (updates only, not a stack tool) | 2026.11.15.06 | 064867b0b02c9761bd880d4be7a807512f0629652ec9cebd3f561e4bd8f1ecbc |

Bundles: NetBlaze 2026.11.16 on this release (`v2026.11.21`, with its .sha256); every other bundle on `v2026.11.19`.

BlazeManager: **2026.11.15.06** or later (204aeb17a94a03e87e2b19a07f178d7e0cbd3f29f9126042a84ebcb3197eeef1).
**Update BlazeManager first**, then **Servers → Update the runner** on every other server (one click does both steps:
the new launcher, then the .NET runner; the server card's runner then lists "dotnet"), then the stack (Stack page →
Update the whole stack to Blaze 2026.11.21). A joined server whose runner is behind blocks the stack update until then.

This is the GitHub copy of the internally published Blaze 2026.11.20 (same bundles, same sha256; its own number because
the signed links differ).

## What's new since 2026.11.19 (the previous GitHub stack)

**NetBlaze 2026.11.16:**
- Backup runs and change targets are no longer left open after an unexpected error; a stale job that gives up finishes its
  change targets and software updates.
- FortiGate: the route lookup reads FortiOS 7.0–7.4 answers (fixes "No route" in path traces on 7.4); every VDOM's BGP
  sessions and IPsec tunnels are on the network map.
- Network map: a List view and an All/Down/Up filter. Tags in config search.
- Fabric: refresh doesn't report disabled members; statistics widgets and MCP get_history count group members.
- Switch health counts switches only; Busiest links shows port channels with their LLDP neighbour; the dashboard reads the
  last runs per device (faster); history filters run in the database; agents' scriptlet proposals leave out devices that
  aren't ready.

Everything else as in Blaze 2026.11.19 (ProxyBlaze's L4 load balancer, BlazeManager's runner in .NET).

## Not in this stack

DHCPBlaze 2026.11.15.01 and BlazeOS 2026.11.15.01 (their own releases). NetBlaze's live telemetry switch, DnsBlaze's
PowerDNS update layer and the labels → ProxyBlaze design wait for a later stack.
