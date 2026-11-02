# Blaze 2026.11.5

A stack version where **every multi-node tool handles any upgrade order itself**: a new version holds back changes that
an older node can't store yet, and warns when a node stops receiving changes. Also **DnsBlaze EYE rules** (filtering
per client) and BlazeManager keeping a hand-installed **TowerBlaze** updated.

| Tool | Version | sha256 |
|---|---|---|
| AuthBlaze | 2026.11.01.01 | 2af73a441e05e62a48439fd75486726574f6af4ec981c2bfeb793e32726f8268 |
| ProxyBlaze | 2026.11.01 | 45f61929c43c063c474f01cb13072cf25c3b94bcf73d41750703b1d930ac0f99 |
| CertBlaze | 2026.10.31.01 | cca09af42d609d71337dfeefa3704ed76574cfa6c437ac018bcba3ae060cafc5 |
| NetBlaze | 2026.11.01 | b633e2fb5e663d55fc9f823105a3d1cd6e345632cd695bd47083e414b2650c92 |
| APMBlaze | 2026.10.31.03 | 15618ad27fd7f1e0ae6211323afb69d26f9aba9ec015d51952fc392eb0f5020a |
| DnsBlaze | 2026.11.01.05 | cda5ca3eff57a1723bbbf8b37b814b477b32ec0bca0b58a0e17741b9eb9ee6bd |
| TowerBlaze (updates only, not a stack tool) | 2026.11.01.04 | 3fd2a8e6962e833ac069655bf9eb12e1ef5b64a823e6112874346c131383eb21 |

The GitHub copy of Blaze 2026.11.4 (same bundles). Bundles: AuthBlaze, ProxyBlaze, DnsBlaze, TowerBlaze and BlazeManager on this
release `v2026.11.5`; CertBlaze and APMBlaze unchanged on `v2026.11.1`, NetBlaze unchanged on `v2026.11.3`.

BlazeManager: **2026.11.01.04** or later (6f16faee4a75a637c3ec637e34d339254e4a95b57efcd569969f9e613fa1d848).
**Update BlazeManager first** (Updates page; a big bundle through break-glass `http://SERVER-1-ADDRESS:8880/`), then
**Servers → Update the runner** on every other server, then the stack (Stack page → Update the whole stack to
Blaze 2026.11.5).

## What's new since 2026.11.3

- **Any upgrade order is safe (AuthBlaze, DnsBlaze, ProxyBlaze).** Each node reports what it can store. The leader
  (ProxyBlaze: the Admin, per Edge group) won't write a change an older node would lose. It names the node to upgrade
  ("upgrade node X first") until that node catches up. Earlier, an old follower or Edge silently dropped new fields.
  After a migrating upgrade, followers reload a full snapshot.
- **Warning when a node stops receiving changes** on the Cluster/Edges page and in syslog:
  `cluster.replication.stalled` / `.recovered` (reason no_contact, refusing or behind), `cluster.version.held_back` /
  `.caught_up`, ProxyBlaze `edge.config.behind` / `edge.config.caught_up`. TowerBlaze 2026.11.01.04 has alarm examples
  for these.
- **DnsBlaze EYE rules:** source networks and address groups → EYE categories → Allow / Drop / Log, per client
  (blocked: NXDOMAIN with EDE 15; logged: EDE 0). On the first start, the old category levels become "Everyone" rules,
  so clients get the same answers as before. 2026.11.01.05 fixes a .04 bug: deleting every rule sent a follower back to
  the old levels.
- **ProxyBlaze:** the Admin's sign-in (data protection) key is now encrypted at rest; older installs kept it in plain
  text.
- **BlazeManager 2026.11.01.04:** keeps a hand-installed TowerBlaze updated ("updates only": Servers card tick, own
  Stack row; rollback by TowerBlaze if the new version isn't healthy). Also: checks for updates against GitHub (fixes
  the 403), and a CLI fix.
- **TowerBlaze 2026.11.01.04:** clean exit codes for a rollback (3 = old version healthy again, 4 = old version
  unhealthy too) and `Version:` in `--status`. Also the alarm examples above.

## Upgrade notes

- **ProxyBlaze:** admins sign in to the Admin once more after the upgrade (the key is re-encrypted). After a
  rollback to an older ProxyBlaze, admins sign in again too.
- **DnsBlaze:** one node at a time (BlazeManager does it, followers first). Any order is safe now, but until every node
  is on 2026.11.01.05, EYE rules can't be saved and the old levels still apply.
- **TowerBlaze:** not updated by "Update the whole stack" unless you tick **Also TowerBlaze … (updates only), last**.
  Syslog over UDP sent while it restarts (a minute or two) is lost.
- Right after a VIP move, the first new sign-in to a tool can fail once ("connection reset"); trying again works.

Tested in the lab 2026-11-01 (BlazeManager whole-stack updates; hand upgrades in the wrong order for AuthBlaze,
DnsBlaze and ProxyBlaze; forced stalls; TowerBlaze rollbacks through BlazeManager; EYE rules per client; WAF Block;
5 VIP moves; syslog; sign-in), plus a DnsBlaze 2026.11.01.05 retest. Not tested in the lab: TowerBlaze alarms firing,
AuthBlaze failover during an update, DnsBlaze `--remove-node`.
