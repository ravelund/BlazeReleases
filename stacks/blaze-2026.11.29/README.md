# Blaze 2026.11.29

**Containers publish themselves with labels, TowerBlaze joins the stack, and every tool's database runs on its own image.
BlazeManager 2026.11.17.08.**
Every tool built and Trivy-scanned on the build server in a fresh Docker (every Blaze image and every PostgreSQL image: 0
HIGH/CRITICAL; PowerDNS: 0 fixable), signed on the release PC.
Tested in the lab on a two-server stack (blaze02 + blaze01, one VIP on both Edges) with a third server and a Docker host:
- **Upgrade:** the whole stack from Blaze 2026.11.26 (= GitHub 2026.11.27) in one update. Every database kept its data, ProxyBlaze's configuration
  was identical before and after, and the sites kept answering when the VIP moved between the Edges.
- **Sign-in:** by pressing each tool's button 6× for a normal user and a user in 1,000 groups, on all seven tools (84/84).
- **TowerBlaze:** kept updated only → upgraded; made a stack member; installed new with Add a tool (site, sign-in, the tools'
  log lines wired, also from tools on its own server); removed with its data kept and installed again on it; removed with
  its data.
- **Labels:** a Docker host with labelled containers: websites, sign-in in front, a TCP service, an auto and an approve
  domain, conflicts and wrong labels, a changed port, stop → remove → start again.

| Tool | Version | sha256 |
|---|---|---|
| AuthBlaze | 2026.11.17 | 22ab39eac47f3dc4a82d11a6427903f9c34019d27cd654ada73780b13bdb505a |
| ProxyBlaze | 2026.11.17.01 | 6485cac3bbd34f8082e47bd91034bcef329fbc0c0e52d0b0a6b63a33536ac5c2 |
| CertBlaze | 2026.11.17 | fdf89767dd2004aef7e75b752e85a5e8109f587e62833b11ac48ab6e9988a99a |
| NetBlaze | 2026.11.17.01 | 9e3b96754b0a29cf6eeaa44af41fcbf9a65270abe2a1df55c75c3120c3154cf1 |
| APMBlaze | 2026.11.17.01 | 998e1b85a0c18387c4a9110ed40e70633d5ed06eeec299dbb15ac9e712d0c0f3 |
| DnsBlaze | 2026.11.17.01 | d7a18f1075c37cf09fb7ebbebb836a2a53f1bc9876a89b892df97883671cf0e2 |
| TowerBlaze | 2026.11.17.04 | 503f8c564bff1c6064adbe4d52c1f50c2da2ac23f12eb8362087918d0238fc5c |

Bundles: every tool's bundle and BlazeManager 2026.11.17.08 on this release (`v2026.11.29`, each with its .sha256).

This is the GitHub copy of the internally published Blaze 2026.11.28 (same bundles, same sha256; its own number because
the signed links differ).

BlazeManager: **2026.11.17.08** or later (8e4a88ed98df767000afd5556a307a7c0a244caf335d21254e1c04b66ebfd6df).

## How to update

1. **Update BlazeManager first** (Updates page).
2. **Servers → Update the runner** on every other server. A server whose runner is behind blocks the stack update.
3. **Stack page → Update the whole stack to Blaze 2026.11.29.**

To know before you start:
- **Every tool's database container is recreated once** during its upgrade, on the same data (a backup is taken first as
  always). Each tool's database now runs on the tool's own image, so another tool can no longer change it; backups, settings
  changes and restarts never recreate it. From now on most upgrades recreate it once, inside the upgrade's own restart.
- **ProxyBlaze: every Edge is updated in this stack** (BlazeManager does it). Sites made from labels need all of them.
- **The bundles are larger** (about 110–120 MB more for the PostgreSQL tools): the database image ships inside. NetBlaze and
  APMBlaze pull the exact SQL Server image (2025 CU11) from Microsoft by its digest when the server doesn't have it; a
  server without internet needs it loaded first.
- TowerBlaze, kept updated only: it is upgraded like before. Nothing is wired or changed by itself.

## What's new since 2026.11.27 (the previous GitHub stack)

- **Containers publish themselves (BlazeManager 2026.11.17.08 + ProxyBlaze 2026.11.17.01).**
  - A container with the label `blaze.site.host` (a website) or `blaze.l4.listen` (a TCP/UDP service) shows up under
    **Discovered sites** within seconds. Approved, BlazeManager makes its site in ProxyBlaze; ProxyBlaze puts the name in
    DnsBlaze and gets the certificate.
  - **Allowed domains** decide: *approve* (a person approves each new name, the default) or *auto* (a free name goes live by
    itself, as its labels ask). Conflicts always wait for a person.
  - Labels for access (private networks, public, **sign-in through AuthBlaze** with groups), the WAF mode and level, a
    health path, a profile, the certificate.
  - **Docker hosts**: an app server joins BlazeManager with "Add Docker host": its labels are read, no Blaze tool runs there.
  - A stopped container's site is drained at once; Remove takes the site and its DNS name away.
  - BlazeManager only ever touches sites it made itself ("managed by BlazeManager" in ProxyBlaze).
- **TowerBlaze is a tool of the stack (TowerBlaze 2026.11.17.04).**
  - Install it from the wizard or **Add a tool** (not ticked by default): its server, the log store's disk limit, behind
    ProxyBlaze with sign-in through AuthBlaze. A new install sends every tool's log lines to it and accepts the stack's
    servers. An existing TowerBlaze, or tools already running, are never wired by themselves.
  - **Make it a stack member** for a TowerBlaze kept updated only; **Remove TowerBlaze** (its log data stays unless you ask).
  - TowerBlaze's installer is a program now (`install.sh` starts it; the bash installer stays in the bundle as a fallback):
    `--json` on every command, `--uninstall [--delete-data]`.
  - "Sign in with AuthBlaze" works again (it could hang since 2026.11.15.02).
- **Every tool: the database on its own image.** PostgreSQL ships in each bundle under the tool's own name, as one layer with
  the start-up helper `gosu` replaced by Alpine's `su-exec` (no HIGH or CRITICAL findings left). NetBlaze and APMBlaze pin the
  exact SQL Server image they were tested with. (Before, a nightly backup could restart a database after another tool had
  moved a shared image tag.)
- **AuthBlaze 2026.11.17:** no false "Stuck N change(s) behind" on the Cluster page; clients kept by a tool (BlazeManager's
  label sites): a "Clients only" token with an owner, and search, filter and pages on the Clients page.
- **ProxyBlaze 2026.11.17.01:** health checks of website pools can accept a status range; an agent may pin a pool member's
  certificate that an Edge reported (MCP); a new site's certificate is ordered once its DNS name exists.
- **BlazeManager:** an agent may propose adding or removing TowerBlaze (a person approves; deleting log data stays in the UI
  and on the command line); a fix for the runner stopping when another program held a file.

## Not in this stack

DHCPBlaze 2026.11.15.01 and BlazeOS 2026.11.15.01 (their own releases). Findings of the security review of this release are
planned for the next versions (none critical).
