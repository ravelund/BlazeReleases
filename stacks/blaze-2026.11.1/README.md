# Blaze 2026.11.1

The same tools as Blaze 2026.11 (internal, installed in production 2026-11-01), with links to this release.

A stack version that fixes the slow sign-ins through AuthBlaze in every tool (Blaze conventions §4d), carries the
security fixes from the 2026-10-31 code reviews, and makes BlazeManager's moves safe to finish after a failure.
TowerBlaze 2026.10.31.06 is attached for TowerBlaze's own updater; it isn't a stack tool.

| Tool | Version | sha256 |
|---|---|---|
| AuthBlaze | 2026.10.31 | f236aeb071813d63bb86ee8f1b98089ff17dce401722e2731220ec9028c7561d |
| ProxyBlaze | 2026.10.31.05 | 0873d070f28cd72aca3bc5dd63a48a78f23be71b78fc5ce460c96801ab0bfbfc |
| CertBlaze | 2026.10.31.01 | cca09af42d609d71337dfeefa3704ed76574cfa6c437ac018bcba3ae060cafc5 |
| NetBlaze | 2026.10.31.01 | 29dfa5da02978b75d76ad9237660446ca8ce773ee1c7c4993ce13eab10f074d3 |
| APMBlaze | 2026.10.31.03 | 15618ad27fd7f1e0ae6211323afb69d26f9aba9ec015d51952fc392eb0f5020a |
| DnsBlaze | 2026.10.31.02 | ebb67f57620b9b9d2509b405b6d36e72166fd717fb36ab2a19a16a64036bb8bb |
| TowerBlaze (not in the stack) | 2026.10.31.06 | b4cbeee7b6b33966776e33afdc952583738a0e4f48a73d0afec8c950b373870b |

Bundles: every tool, BlazeManager 2026.10.31.08 and TowerBlaze 2026.10.31.06 on this release `v2026.11.1`; the manifest links to each. Each bundle carries its user guide.

BlazeManager: **2026.10.31.08** or later
(13a3f0c271691c6dfbc61e753a17493faaf096e3b43040a7a10fe897e4966599). **Update BlazeManager first** (Updates page; an online update downloads it, an upload by hand goes through break-glass
`http://SERVER-1-ADDRESS:8880/` because an upload through the VIP can stall), then
**Servers → Update the runner** on every other server, then the stack (Stack page → Update the whole stack to
Blaze 2026.11.1).

**A move that failed earlier: finish it first.** If a Move to … stopped half-way (Stack page shows it), press
**Try again** on BlazeManager 2026.10.31.08 before anything else, with both servers on. From .06 on BlazeManager holds
the other actions until it is done.

## What's new since 2026.10.23

- **From Blaze 2026.10.24 (internal only, now here too):** BlazeManager finds new stack versions by itself
  (Releases → Stack source, checked every 6 hours, never installs on its own) and updates runners before the stack;
  ProxyBlaze body size limits for uploads (Sites → Defaults, per site, per route) and Allowed networks per site;
  DnsBlaze EYE ("Every Yikes, Explained": block or log known-bad and unwanted names by category); APMBlaze syslog
  fixes; TowerBlaze's updater finds a bundle on any release.
- **Sign-ins no longer stall ~15 s** (all tools, conventions §4d). .NET downloaded a missing CA certificate from the
  address in a certificate (AIA, plain HTTP) before the tool's own trust check; where a firewall drops that, every new
  connection to AuthBlaze stalled. Every tool's outgoing TLS now skips the download and the online revocation check,
  requires a server certificate (serverAuth), and keeps its AuthBlaze connection open 5–10 minutes.
  **A server that sends only its own certificate without its intermediate is now refused:** add the intermediate on
  the tool's Trusted CAs. The Blaze tools and ProxyBlaze send the whole chain.
- **AuthBlaze:** a refresh token issued by a node that is down gets "503, try again in 30 s" instead of a sign-out;
  a session race on the leader fixed; security review fixes.
- **BlazeManager 2026.10.31.08:**
  - Try again of a move takes a fresh backup (before, data written after the first backup could be lost).
  - Moving a tool back to a server with an older stopped copy upgrades that copy in place first.
  - Updating the stack fetches tools that run on another server (NetBlaze on server 2 after a move).
  - Servers that use the stack's own DnsBlaze as DNS servers no longer stop an update at the DNS check.
  - A half-finished move holds the other actions with a clear text; Try again finishes it and only touches that
    tool's ProxyBlaze pool.
- **ProxyBlaze:** failed proxy requests are logged with pool, member and address, and retries too; more known-bad lists
  (Emerging Threats compromised, blocklist.de, CINS Army, GreenSnow); security review fixes.
- **NetBlaze (security release):** unmasked configs over the API need the new **Secrets** scope (others get
  `********`); API token rules (local tokens stop while local login is off, AuthBlaze users' tokens need a sign-in
  within 30 days, new tokens expire within 365 days); change scripts refuse control characters and exec lines except
  `do show`; optional "a second person approves agents' requests".
- **CertBlaze, DnsBlaze, APMBlaze:** §4d only.
- **TowerBlaze 2026.10.31.06** (attached): §4d; the AIA fetches made every sign-in 2–3 s slower.

## Upgrade notes

- **DnsBlaze:** one node at a time (BlazeManager does it); each node gives about 17 s without DNS answers.
- **ProxyBlaze:** Admin first, then the Edges (BlazeManager's order).
- **NetBlaze API users:** scripts that read configs with secrets need a token with the Secrets scope.
- Right after a VIP move, the first new sign-in to a tool can fail once ("connection reset"); trying again works.

Tested in the lab 2026-11-01 (update path from a stack like 2026.10.24, failover, VIP moves, half-failed move repair).
