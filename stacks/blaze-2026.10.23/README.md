# Blaze 2026.10.23

The same tools as Blaze 2026.10.22 (internal, installed in production 2026-10-31), with links to this release. A stack version: every tool with its syslog release (RFC 5424 over UDP, one JSON object per line, the same field names
in every tool), for TowerBlaze or any other syslog server, and BlazeManager 2026.10.31.01 with gratuitous ARP settings
for faster VIP moves.

| Tool | Version | sha256 |
|---|---|---|
| AuthBlaze | 2026.10.30.01 | 09b712ec01345c53c07668c58c838a51bc3e899578f7ad2d39745be0cc5c51d5 |
| ProxyBlaze | 2026.10.31 | 7dd80e44391ece28968c86642ebcd20123a7565bb1abc67e5fac2175307b6238 |
| CertBlaze | 2026.10.31 | c8cc452627a76a0a438e96385f1334b5dc069cf6e685e8eadb132469ffddb649 |
| NetBlaze | 2026.10.31 | 834d626e29358d5da07132c0294779277fa90677663fe2f33fd93c6630bc80ce |
| APMBlaze | 2026.10.31.01 | 44e63ecf6162b05a8ac56078c05eb7f1705ee5c8ff71a10c4cd410163cad5bfc |
| DnsBlaze | 2026.10.31 | db8ab3e381426a96a019e2db51f4480de8103adc72fd87ba1676f8f43face88e |

Bundles: every tool and BlazeManager 2026.10.31.01 on this release `v2026.10.23`; the manifest links to each.

BlazeManager: **2026.10.31.01** or later. **Update BlazeManager first** (Updates page; a big bundle through
break-glass `http://SERVER-1-ADDRESS:8880/`), then **Servers → Update the runner** on every other server, then the
stack (Stack page → Update the whole stack to Blaze 2026.10.23).

## What's new since 2026.10.20

- **ProxyBlaze syslog sends everything** (2026.10.30.01 and later): every Admin and Edge event, not only a selection.

- **Syslog in every tool** (off until you name a server): AuthBlaze, CertBlaze, NetBlaze, APMBlaze, DnsBlaze, and
  ProxyBlaze as before. Each tool's own Syslog page (target, on/off, **Send test line**) or
  `sudo ./install.sh --syslog-target HOST[:PORT]` / `--syslog off` in the tool's folder; for AuthBlaze and DnsBlaze
  on the leader (one setting for the cluster, every node sends its own lines); for ProxyBlaze on Admin's server
  (Admin and every Edge send). `host.name` is always the server's name. Sign-ins, every audit entry, certificates,
  DNS changes, WAF events, upgrades and backups, and the tools' own warnings. No passwords, keys or tokens.
  Point syslog at the syslog server's own address, not at a name that resolves to a ProxyBlaze VIP.
- **Syslog limits are an emergency brake only:** no per-client/per-address limit by default (a password-guessing
  attack from one address is what the syslog server must see), 10000 lines a second per process. A stack that ran
  2026.10.20 keeps ProxyBlaze's older 1000 / 60 per client: set ProxyBlaze → Settings → Syslog to 10000 and 0.
- **BlazeManager 2026.10.31.01:** gratuitous ARPs after a VIP move: 5 at once and one every 5 s
  (`garp_master_repeat 5`, `garp_master_refresh 5`). Updating BlazeManager sets them on server 1; **Update the
  runner** sets them on every other Edge server (one click: keepalived.conf checked with `keepalived -t`, a backup
  kept, keepalived reloaded, the VIP stays). A server whose keepalived.conf is still the older one shows **Apply the
  gratuitous ARP settings** on the Servers page after Check host. Update targets never take a tool back to an older
  version. The Releases page shows each tool's `syslog` capability.

## Known bugs and limits

- **Large uploads through the VIP** (a big bundle on the Updates or Releases page) can stall: upload through
  break-glass `http://SERVER-1-ADDRESS:8880/`.
- **Upgrade gaps, as before:** a single-node tool's site answers 502 for ~9 s while it restarts (CertBlaze in the
  test); each DnsBlaze node gives no DNS answers for ~17 s during its own upgrade (one node at a time).
- **Syslog lines from before syslog was set:** turning syslog on (or a restart) can re-send entries from the last
  minutes (same `blaze.event_id`; a receiver that drops repeats is fine). An upgrade's `update.result` is sent only
  by tools whose syslog was already on. A failed sign-in can also give an extra `app.log.recorded` warning beside
  `auth.signin.failed` (DnsBlaze, CertBlaze, APMBlaze).
- **`user.name` for host commands differs between tools** (`host`, `install`, `install.sh`, or the sudo user);
  `blaze.audit.via` is `host` in all of them.
- **Releasing a tool or removing it: cleanup is yours** (as before). Ticks can't be taken back on a running
  stack. A tool removed by hand still needs its AuthBlaze client deleted by hand before it can be added again.
- **Go back after a failed second take-over** of a tool can turn its trusted CAs off, although the tool trusted the
  stack's roots before. Its AuthBlaze sign-in then fails; set the trusted CAs again (the tool's `install.sh
  --trusted-cas`, or Take over again with the roots ticked). Fix in the next BlazeManager.
- **Firewall openings for a taken-over tool** (its syslog port, the Edges to its UI port) are shown only on the
  wizard's Check, not after a take-over. Open them by hand.
- **Servers page right after adding an Edge:** the new server can still show "Add to the stack" until BlazeManager's
  next restart or **Check host** on that server. Click Check host before adding the next Edge.
- **Firewall openings for a new Edge** don't list the tools-only server: the Edge must also reach that server's tool
  ports.
- A site deleted with `--cmd apply --replace` keeps its DNS record in DnsBlaze (delete it in DnsBlaze, or delete the
  site on ProxyBlaze's Sites page and answer Delete for its DNS records).
- The end-of-run admin list can show **NetBlaze's default password** after it has been changed.
- **Connect BlazeManager** with an empty name box uses `blazemanager.<domain>`. Type the name.
- **The VIP on VMware NSX:** with the new gratuitous ARP settings, clients on the VIP's own subnet saw no failed
  request in 5 forced moves; a routed client saw at most one failed request (~2 s), against 11–24 s before. NSX's
  IP-discovery binding still follows the VIP late (seconds to over a minute) but didn't cost requests in the test.

## Files

- `manifest.json`: the versions, bundle names, sha256 and download address of each tool release.
- `manifest.json.sig`: its signature with the stack key (ECDSA P-256, SHA-256). The public key is
  `../stack-signing.pub` (key ID 4a3853a0d6fe86a0); BlazeManager has it pinned.

## Using it

- **With internet**: BlazeManager → Releases → upload `manifest.json` and `manifest.json.sig` (or
  `sudo ./install.sh --add-stack manifest.json`); it downloads the bundles from the addresses in the manifest.
- **Offline**: copy the six tool bundles (each tool's release page, the `url` in the manifest) and both manifest files
  to the BlazeManager server. Upload the bundles on the Releases page ("Upload a tool's release bundle"), then the
  manifest. BlazeManager checks every bundle against the sha256 in the signed manifest. Big bundles: break-glass.
- Then update the stack: the Stack page, or `sudo ./install.sh --install "update=Blaze 2026.10.23"`.

Check by hand: `openssl dgst -sha256 -verify ../stack-signing.pub -signature manifest.json.sig manifest.json`.

## Tested

Full stack test on 2026-10-31 (two lab servers with one VIP plus a tools-only server; the published bundles, every
sha256 and signature checked):

- The previous internal stack installed fresh with BlazeManager 2026.10.30.01 (wizard on server 1 without APMBlaze, server 2 as
  the second Edge with AuthBlaze and DnsBlaze nodes, then Add a tool APMBlaze), the way production runs today.
- BlazeManager updated to 2026.10.31.01 on its Updates page; Update the runner applied the GARP settings with the VIP
  kept and no failed request; Apply the gratuitous ARP settings tested on an older keepalived.conf.
- Update the whole stack to the new stack from the Stack page: every tool upgraded, certificates and data kept.
- Every tool's syslog to a TowerBlaze server: test lines from every node and Edge, and real events (sign-ins, a
  publish, WAF, DNS records, a certificate, NetBlaze and APMBlaze changes), `host.name` right everywhere.
- Take over one tool (TowerBlaze on a tools-only server, from a lab-only stack version): the stack's roots, behind
  ProxyBlaze, AuthBlaze sign-in with admin/editor/viewer roles; Release and take over again.
- Full AuthBlaze sign-in for every tool through the VIP (21/21, twice); 5 forced VIP moves measured; a WAF Block check
  (XSS and path traversal in a parameter: 403, not forwarded, logged to syslog; no false positives on normal requests).
