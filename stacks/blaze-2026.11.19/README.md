# Blaze 2026.11.19

**ProxyBlaze's L4 load balancer (TCP, UDP, TLS passthrough) and BlazeManager's runner rewritten in .NET.
BlazeManager 2026.11.15.06.**
Built and Trivy-scanned on the build server (every Blaze image 0 HIGH/CRITICAL), signed on the release PC.
Tested in the lab on a two-server stack like production (blaze02 + blaze01, one VIP on both Edges), starting from Blaze
2026.11.17 with the published-port filter on: BlazeManager .04 → .06 (its own runner switched to .NET), a joined server
switched from the bash runner to .NET with one Update the runner, a fresh join with the .NET runner, the second server
added with `ha=` entirely through the .NET runner, a stack update named in the new same-day form, ProxyBlaze .03 → .04 with
its configuration unchanged; L4: SSH through the VIP to a server behind it, DNS (UDP and TCP) on the VIP's port 53 next
to DnsBlaze and systemd-resolved, the health check down and up, **Edge failover** (SSH, DNS and HTTPS kept working when the
VIP moved), BlazeManager's own site apply leaving L4 untouched; the earlier security checks repeated, sign-in by pressing
each tool's button 6× per tool for a normal user and a user in 1,000 groups (72/72).

| Tool | Version | sha256 |
|---|---|---|
| AuthBlaze | 2026.11.15 | 549796e5e1515329116468b5695ad7ee4cb70d4fab5c509f263c88cdb18be211 |
| ProxyBlaze | 2026.11.15.04 | efbdb73723eb0aa34322312be20e836b38fd2134de36c4c43833614f278a16dc |
| CertBlaze | 2026.11.15.03 | f22f282533ec7e29126a2f10b1d3a5da85bf3eee8d14883f188cbb9ade51a08b |
| NetBlaze | 2026.11.15.01 | 87438973acbece5311a0ad51b4ccd5a807b84591d5c38ef941b4d6357f61abb5 |
| APMBlaze | 2026.11.15.03 | 056a80095904a64e38ff87df7b97f68ff131981872d29711e93dd30cf7e1a584 |
| DnsBlaze | 2026.11.15.02 | b6864049a5f6bacb872b9663de363a143b8cc0f0ff914b1f9a269687197d0fe5 |
| TowerBlaze (updates only, not a stack tool) | 2026.11.15.06 | 064867b0b02c9761bd880d4be7a807512f0629652ec9cebd3f561e4bd8f1ecbc |

Bundles: this release's assets (`v2026.11.19`), with a .sha256 per bundle.

BlazeManager: **2026.11.15.06** or later (204aeb17a94a03e87e2b19a07f178d7e0cbd3f29f9126042a84ebcb3197eeef1).
**Update BlazeManager first**, then **Servers → Update the runner** on every other server (one click does both steps:
the new launcher, then the .NET runner; the server card's runner then lists "dotnet"), then the stack (Stack page →
Update the whole stack to Blaze 2026.11.19). A joined server whose runner is behind blocks the stack update until then.

This is the GitHub copy of the internally published Blaze 2026.11.18 (same bundles, same sha256; its own number because
the signed links differ).

## What's new since 2026.11.10 (the previous GitHub stack)

Everything in the internal stacks 2026.11.13 to 2026.11.18, among them: the security wave from a lab security test (one
strict Content-Security-Policy in every tool, BlazeOS published-port filter labels), the fixes found by the first
vulnerability scan (ProxyBlaze WAF x/net, APMBlaze .NET crypto, TowerBlaze VictoriaLogs), BlazeManager's firewall filter
screen and BlazeOS screens, CertBlaze's next-backup fix on Debian 13, TowerBlaze's progressive search, and below:


- **ProxyBlaze 2026.11.15.04: the L4 load balancer.**
  - A listener can be TCP, UDP or TLS passthrough by name (SNI) instead of HTTP, to a pool like a site's: databases, SSH
    (e.g. git over SSH to Gitea), MQTT, DNS. The HTTP proxy and the WAF are unchanged.
  - New pool health checks: TCP, TLS, send/expect, DNS, and HTTP status ranges.
  - It runs in its own container `l4` (host network), on by default and idle without L4 listeners (`install.sh --l4 off`
    turns it off). Ports below 80 need `--l4-low-ports` (default 53); the container runs with only the right to bind
    low ports.
  - **Bind an L4 listener to the VIP's own address** (port 53 can't be bound on 0.0.0.0 next to DnsBlaze or
    systemd-resolved; the standby Edge binds the VIP before it holds it). Give UDP listeners allowed networks.
  - L4 configuration needs every Edge of its group on 2026.11.15.04; ProxyBlaze holds it back until they are.
- **BlazeManager 2026.11.15.06: the runner in .NET.**
  - The root runner on every server is now one signed .NET program instead of a large bash script: the same requests and
    the same fixed list of actions, typed and tested, with no shell put together from values. Joining a server (`--join`)
    runs in it too. The bash runner stays as a fallback for one release.
  - Same-day hotfix stacks: BlazeManager reads `Blaze YYYY.MM.DD.NN` stack names (update BlazeManager before such a stack).
  - The Updates page names the address and the answer when a release check gets a page instead of a release list.
- **TowerBlaze 2026.11.15.06:** a cancelled search no longer shows "The search took too long" over the newer search's results.

## Not in this stack

DHCPBlaze 2026.11.15.01 and BlazeOS 2026.11.15.01 (their own releases). DnsBlaze's PowerDNS update layer, NetBlaze's live
telemetry switch and the labels → ProxyBlaze design wait for a later stack.
