# Blaze 2026.10.20

A stack version with the same tools as Blaze 2026.10.19 (installed in production 2026-10-30): Blaze 2026.10.18 with
ProxyBlaze 2026.10.29.02, DnsBlaze 2026.10.29.01, NetBlaze 2026.10.29 and BlazeManager 2026.10.30.01. Full stack test
green on 2026-10-30 (update from Blaze 2026.10.18 and a fresh install, see Tested).

| Tool | Version | sha256 |
|---|---|---|
| AuthBlaze | 2026.10.27.05 | 68f129471152f45eb32363465ca0480c886ff6b2f1cd01b80716e97d95716f54 |
| ProxyBlaze | 2026.10.29.02 | 26e94f883a0c693a26b9eaa39aa7b6df3a2bad6cf485029c732e5fcb7e4cdc5a |
| CertBlaze | 2026.10.27 | b2b4035e2675a1455183145b2976192def2415db75fc86dec4efdc8ecadad1cd |
| NetBlaze | 2026.10.29 | 71bd6b8351a5392f6ed0b31a42a38db15d1e6677b64539f986ce4e7d256fceb1 |
| APMBlaze | 2026.10.27.02 | 495c5e6d9d64ab2eae9ac839c45ae0b9c6baaaa0dfe10f6767ed7e02b7b8a8ed |
| DnsBlaze | 2026.10.29.01 | 39517d25b04e9979af23f7a09f690ea2bed638fba8c108829e2a911166a03996 |

Bundles: ProxyBlaze, DnsBlaze, NetBlaze and BlazeManager 2026.10.30.01 on this release `v2026.10.20`; AuthBlaze,
CertBlaze and APMBlaze on `v2026.10.15`. The manifest links to each.

BlazeManager: 2026.10.30.01 or later. **Update BlazeManager first** (Updates page), then **Servers → Update the
runner** on every server, then the stack.

## What's new since 2026.10.18

- **Drain (the 2026.10.18 DnsBlaze known bug is fixed).** With Edge groups in more than one site, ProxyBlaze keeps the
  stack's names in DnsBlaze as health-checked load-balanced names: when one site's Edges are down, DnsBlaze stops
  answering that VIP within seconds and takes it back when the Edges are up again.
- **ProxyBlaze syslog** (Administration → Syslog): events from Admin and every Edge to your syslog server.
- **ProxyBlaze "Trust this certificate"** for a pool member whose HTTPS certificate an Edge refuses (pinned per member).
  Plain HTTP behind the Edge stays the recommended setup.
- **BlazeManager: Edge pairs.** More Edges per site, two per VIP (VRRP); a third may join a VIP when you tick Join
  anyway. Edge-only pairs may also serve other, non-Blaze sites.
- **BlazeManager: Take over one tool** that runs beside the stack (roots, behind ProxyBlaze, AuthBlaze sign-in). The
  tool must be at the stack's version first; BlazeManager offers the update.
- **NetBlaze 2026.10.29:** FortiGate panels.
- **DnsBlaze install.sh** always loads the bundle's own images (checks image IDs, not only tags).

## Known bugs and limits

- **Large uploads through the VIP** (e.g. a big release bundle on BlazeManager's Updates or Releases page) can stall:
  the file goes over Blazor's WebSocket, and no ProxyBlaze setting helps. Upload through break-glass instead:
  `http://SERVER-1-ADDRESS:8880/` (BlazeManager's own port on server 1, local admin). BlazeManager shows the hint after
  60 s without progress. A plain HTTP upload comes in a later BlazeManager.
- **Releasing a tool or removing it: cleanup is yours.** Release (Take over one tool) only removes the tool from
  BlazeManager's records and lists what you may undo by hand (its site and pool in ProxyBlaze, choose Delete for their
  DNS records; its AuthBlaze client; the settings BlazeManager changed, with the exact `install.sh` command for each).
  Ticks can't be taken back on a running stack. A tool removed by hand still needs its AuthBlaze client deleted by
  hand before it can be added again.
- **The VIP can take ~30 s to move on VMware NSX.** keepalived takes the VIP within a second, but NSX's IP discovery
  (ARP snooping/binding limit, ARP suppression) may keep the VIP bound to the old VM until a later gratuitous ARP.
  Fix on NSX (allow the VIP on both VMs' IP discovery, binding limit ≥ 2, SpoofGuard allow-list), or add to each
  `vrrp_instance` in `/etc/keepalived/keepalived.conf` (worst case ~5 s):

  ```
  garp_master_delay 1
  garp_master_repeat 5
  garp_master_refresh 5
  garp_master_refresh_repeat 1
  ```

  DNS drain covers a whole site going down regardless; this is about the VIP moving inside a pair.
- **DnsBlaze restarts all its containers on any `install.sh` run that isn't an upgrade** (e.g. `--site`): about 25 s
  without DNS answers on that node. BlazeManager skips the step when the node's site is unchanged; run such commands
  one node at a time.
- **The first hour after the update (or a fresh install): drain only for clients that ask DnsBlaze directly.** Until
  now the stack's names were plain A records with the zone's TTL (3600 s); the update turns them into health-checked
  names with TTL 20, but a DNS server that cached a name before (e.g. your AD DNS forwarding to DnsBlaze) keeps the old
  answer up to an hour. After that hour drain works through them too.
- **Servers page right after adding an Edge:** the new server can still show "Add to the stack", and its pair "room
  for 1", until BlazeManager's next restart or **Check host** on that server. Click Check host before adding the next
  Edge.
- **Firewall openings for a new Edge** don't list the tools-only server (e.g. APMBlaze on server 3): the Edge must also
  reach that server's tool ports.
- A site deleted with `--cmd apply --replace` keeps its DNS record in DnsBlaze (delete it in DnsBlaze, or delete the
  site on ProxyBlaze's Sites page and answer Delete for its DNS records).
- The end-of-run admin list can show **NetBlaze's default password** after it has been changed.
- **Connect BlazeManager** with an empty name box uses `blazemanager.<domain>`. Type the name.

## Files

- `manifest.json`: the versions, bundle names, sha256 and download address of each tool release.
- `manifest.json.sig`: its signature with the stack key (ECDSA P-256, SHA-256). The public key is
  `../stack-signing.pub` (key ID 4a3853a0d6fe86a0); BlazeManager has it pinned.

## Using it

- **With internet**: BlazeManager → Releases → upload `manifest.json` and `manifest.json.sig` (or
  `sudo ./install.sh --add-stack manifest.json`); it downloads the bundles from the addresses in the manifest.
- **Offline**: copy the six tool bundles (each tool's release page, the `url` in the manifest) and both manifest files
  to the BlazeManager server. Upload the bundles on the Releases page ("Upload a tool's release bundle"), then the
  manifest. BlazeManager checks every bundle against the sha256 in the signed manifest. Big bundles: use break-glass
  (see Known bugs).
- Then update the stack: the Stack page, or `sudo ./install.sh --install "update=Blaze 2026.10.20"`.

Check by hand: `openssl dgst -sha256 -verify ../stack-signing.pub -signature manifest.json.sig manifest.json`.

## Tested

Full stack test on 2026-10-30 (five lab servers, the published bundles, all sha256 and signatures checked):

- **Update from Blaze 2026.10.18** (as production): 10.18 installed with BlazeManager 2026.10.27.09 (two sites, VIPs,
  APMBlaze with an uploaded certificate), BlazeManager updated on its Updates page (break-glass), the second server's
  runner with the one-time command the Servers page shows, the stack updated from the Stack page. Every tool at
  10.19, every site certificate unchanged (the uploaded one too), names load-balanced, DnsBlaze not restarted for
  unchanged sites, 36/36 AuthBlaze sign-ins.
- **Fresh install:** wizard on server 1, server 2 as site b, server 3 tools-only with APMBlaze added there, an Edge
  joining server 1's pair (Join anyway gate for a third), an Edge pair with its own VIP. Drain (server 1's pair down
  60 s: DnsBlaze answers the other VIPs at once, a client through AD DNS lost ~5 s), ProxyBlaze syslog from Admin and
  every Edge, a real ACME renewal, Trust this certificate, break-glass, Docker's pools, a no-op update without DNS
  timeouts, 42/42 sign-ins through three VIPs.
