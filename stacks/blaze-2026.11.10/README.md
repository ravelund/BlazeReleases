# Blaze 2026.11.10

**Blaze 2026.11.8 rebuilt on .NET 10.0.15, with labels that keep APMBlaze's container updates away from every Blaze
container, the SSH switch for BlazeOS servers, and fixes in AuthBlaze, DnsBlaze and ProxyBlaze.**
Tested in the lab on two stacks: an update from Blaze 2026.11.8 on two servers (same shape as production) and an
update on three BlazeOS servers with the firewall on (SSH switch, Edge-only and tools-only servers).

| Tool | Version | sha256 |
|---|---|---|
| AuthBlaze | 2026.11.11.01 | 26f325e32505fd286a6ee52df5c2fdb5058f61f86e78c3d6cf5e4844ddf0c4d7 |
| ProxyBlaze | 2026.11.11 | a3bfeb9d8530f02424e48038357fd12816d9da93917957ee019b3070d150d96f |
| CertBlaze | 2026.11.11 | 8727979a79e44bbc04fb2c08ae5e2fef90300306bd2a5ee2cca9a7d3a3302e04 |
| NetBlaze | 2026.11.11 | be8704d962c3a7d971641a795467b57c09476b215bf9b3a89a1f7a2579f46653 |
| APMBlaze | 2026.11.11.01 | fca1f520d4c333d6cd7f5990899b9503bf58153c87203fdc1a479cae512ed168 |
| DnsBlaze | 2026.11.11 | ee59a77039f5c6a40a1ade6042d41409a5cb9e55425535273758637d3c3f5f28 |
| TowerBlaze (updates only, not a stack tool) | 2026.11.11.01 | 1bdd404b20966ba6876be65930d91b09ff30d676b32c67474da71145d16baedf |

Bundles and BlazeManager 2026.11.04.13: assets of this release (v2026.11.10). Same tools as Blaze 2026.11.9 (internal Gitea).

BlazeManager: **2026.11.04.13** or later (447db4c70ea03b85f87175a0b008c513430c4d9bce2dd522649148c2bd0ed6c2).
**Update BlazeManager first**, then **Servers → Update the runner** on every other server, then the stack (Stack page →
Update the whole stack to Blaze 2026.11.10).

## What's new since 2026.11.8

- **.NET 10.0.15** and fresh base images for every tool.
- **`apm.update=false` on every Blaze container** (the tools, their databases, BlazeManager and its runner agents), so
  APMBlaze's container updates never touch a Blaze tool, even when they are switched on for the host. Runner agents
  get the label at their next **Update the runner**.
- **BlazeManager 2026.11.04.13:** the SSH switch (Firewall: the SSH row on/off, `--install firewall-ssh=off|on`; needs
  BlazeOS 2026.11.11.01's firewall on each server; the way back without BlazeManager is the server's console:
  `sudo blazeos-firewall ssh on`). The "tested on Ubuntu" note no longer shows on BlazeOS.
- **AuthBlaze 2026.11.11.01:** a cluster upgrade that skipped a follower (its updater off, the node down) can now be
  finished with Try again: the leader upgrades only the followers still behind.
- **DnsBlaze 2026.11.11:** `update_record` (MCP) changes only what it is given; a missing name no longer moves the record
  to the zone's own name. The REST API refuses a record without a name or data.
- **ProxyBlaze 2026.11.11:** front proxies over MCP and `--cmd apply` (merge and patch); a merge names the keys it left
  out; the same WAF rule allowed twice no longer stops the Edge; Security → **Log blocked clients** (optional, with a
  time limit, syslog `blocking.client.blocked`); a site's **Never blocked here** networks (a group with an older Edge
  holds such a publish back).
- **TowerBlaze 2026.11.11.01:** FortiGate DHCP lines parsed as DHCP, failed FortiGate logins on the firewall dashboard,
  alarm rules keep their search and sort after a save.
- CertBlaze, NetBlaze and APMBlaze: the rebuild and the labels; §5a (sign-in after a VIP move) in every tool that
  signs in through AuthBlaze.

## BlazeOS

BlazeOS servers need baseline **2026.11.11.01** (`blazeos-host`) for the SSH switch. Installing it on a running server
keeps its firewall settings and restarts no container.

## Not in this stack

DHCPBlaze 2026.11.11.01 (its own release: labels, §5a, node links by IP).
