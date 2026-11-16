# Blaze 2026.11.23

**Blaze 2026.11.21 plus NetBlaze 2026.11.16.01: FortiGate VDOMs, anycast gateways in path traces, live telemetry on/off in
Settings, and an SSH terminal in the browser (off by default). BlazeManager 2026.11.15.06.**
NetBlaze built and Trivy-scanned on the build server (0 HIGH/CRITICAL), signed on the release PC.
Tested in the lab (light upgrade test): the two-server lab stack on 2026.11.20 updated to this stack, NetBlaze
2026.11.16 → 2026.11.16.01 healthy with no database migration, the new telemetry container running and telemetry still off,
the SSH terminal off after the upgrade, ProxyBlaze export identical, sign-in by pressing NetBlaze's button 6× for a normal
user and a user in 1,000 groups, all 31 NetBlaze pages with no CSP violation or console error.

| Tool | Version | sha256 |
|---|---|---|
| AuthBlaze | 2026.11.15 | 549796e5e1515329116468b5695ad7ee4cb70d4fab5c509f263c88cdb18be211 |
| ProxyBlaze | 2026.11.15.04 | efbdb73723eb0aa34322312be20e836b38fd2134de36c4c43833614f278a16dc |
| CertBlaze | 2026.11.15.03 | f22f282533ec7e29126a2f10b1d3a5da85bf3eee8d14883f188cbb9ade51a08b |
| NetBlaze | 2026.11.16.01 | b4213e579fa455c494c0ad367500a73b5b688a86746d8a515577f7d6354cca29 |
| APMBlaze | 2026.11.15.03 | 056a80095904a64e38ff87df7b97f68ff131981872d29711e93dd30cf7e1a584 |
| DnsBlaze | 2026.11.15.02 | b6864049a5f6bacb872b9663de363a143b8cc0f0ff914b1f9a269687197d0fe5 |
| TowerBlaze (updates only, not a stack tool) | 2026.11.15.06 | 064867b0b02c9761bd880d4be7a807512f0629652ec9cebd3f561e4bd8f1ecbc |

Bundles: NetBlaze 2026.11.16.01 on this release (`v2026.11.23`, with its .sha256); every other bundle on `v2026.11.19`.

BlazeManager: **2026.11.15.06** or later (204aeb17a94a03e87e2b19a07f178d7e0cbd3f29f9126042a84ebcb3197eeef1).
**Update BlazeManager first**, then **Servers → Update the runner** on every other server (one click does both steps:
the new launcher, then the .NET runner; the server card's runner then lists "dotnet"), then the stack (Stack page →
Update the whole stack to Blaze 2026.11.23). A joined server whose runner is behind blocks the stack update until then.

This is the GitHub copy of the internally published Blaze 2026.11.22 (same bundles, same sha256; its own number because
the signed links differ).

## What's new since 2026.11.21 (the previous GitHub stack)

**NetBlaze 2026.11.16.01:**
- Every FortiGate VDOM is stored and shown.
- Path trace: the anycast gateway and the switches holding it; long traces scroll and wrap; the routing policy card wraps.
- Network map List view with an All/Problems/Up filter; tags in config search.
- **Live telemetry on/off in Administration → Settings** (the telemetry container always runs and idles when off; an older
  install's NETBLAZE_TELEMETRY in .env seeds the setting once). Samples use NetBlaze's clock; it reconnects when samples stop.
- **SSH terminal in the browser** (Console tab on each device, Terminals page, detachable): **off by default**, turned on
  by an admin in Administration → Settings → SSH terminal. People log in with their own device account or a stored
  credential; opening and closing are audited and sent to syslog; only to the pinned SSH host key. Limits by
  NETBLAZE_TERMINAL_IDLE_MINUTES (15), _MAX_HOURS (8), _PER_USER (8), _TOTAL (40), defaults in compose.

Everything else as in Blaze 2026.11.21.

## Not in this stack

DHCPBlaze 2026.11.15.01 and BlazeOS 2026.11.15.01 (their own releases). DnsBlaze's PowerDNS update layer and the labels →
ProxyBlaze design wait for a later stack.
