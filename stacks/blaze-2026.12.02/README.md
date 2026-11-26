# Blaze 2026.12.02

**BlazeManager 2026.11.26.02 (cluster updates, HA, BlazeSDN and install fixes) and CertBlaze 2026.11.25 (Domeneshop
for public certificates). The other tools are unchanged from Blaze 2026.11.30.**
Every tool built on the build server and signed on the release PC.
This is the GitHub copy of the internally published Blaze 2026.12.01 (same bundles and sha256; its own number because the signed links differ).
Tested in the lab:
- **Five-server BlazeOS lab** on the release candidates: whole-stack update, BlazeOS update and Update cluster with
  Ceph and HA, sign-in 36/36.
- **Full install** on three new servers through BlazeManager's Install wizard.
- **Production's upgrade path:** a server from the BlazeOS 2026.11.15.01 image with the released Blaze 2026.11.29,
  BlazeManager updated, then the stack. Sign-in 24/24, every container healthy.

| Tool | Version | sha256 |
|---|---|---|
| AuthBlaze | 2026.11.21 | e3b6a24797af5e45f217772351605f4e6f7f9fabababd63764c1578e6591120d |
| ProxyBlaze | 2026.11.20.03 | 972b21c95fffc6ee50c7427ce5f2c56383bdd10e3404c67b253c27c51df7a850 |
| CertBlaze | **2026.11.25** | cca75ffdd3ca4b25b00928b3679ca380c43a079512ea2b8bb2ee4f2285bbe58e |
| NetBlaze | 2026.11.21 | cb4323425ff906dbe694cc2dc6f19852e33e4ddf138071e89ea84e01f06c5a47 |
| APMBlaze | 2026.11.20.03 | 447aa2bb1474d4d08bc4cc3f338dfa36615b1755b148d7e82ba00ea681162fd7 |
| DnsBlaze | 2026.11.21.02 | b802671a53948ca2531a78e7bf34eab38f8e6352cf88b91c406bc7412136ca89 |
| TowerBlaze | 2026.11.20.03 | b821a34f227b7768143d86aa7e548412a74467aeeabf87e54b27fadc7eb857ab |

Bundles: CertBlaze 2026.11.25 and BlazeManager 2026.11.26.02 on this release (`v2026.12.02`, with .sha256); the other tools on `v2026.11.30`.

BlazeManager: **2026.11.26.02** or later (3db830d17b1a9e0b2386aa5de4e6fc8661810efc7fad03343ae07942ba35b7af).

## How to update

1. **Update BlazeManager first** (Updates page).
2. **Servers → Update the runner** on every other server.
3. **Stack page → Update the whole stack to Blaze 2026.12.02.** Only CertBlaze changes.

This stack has no new BlazeOS baseline; BlazeOS hosts keep getting Debian's security updates daily. The next BlazeOS
baseline comes with the next stack.

## What's new since 2026.11.30

- **BlazeManager:**
  - The Install wizard checks the DNS delegation up front, and a missing delegation is a note, not a failure.
  - `--install resolver` continues a failed install.
  - Tools added after TowerBlaze get their syslog wired.
  - Parallel requests are queued, with quick answers when one fails.
  - Update cluster: a live Ceph health read with a 30-minute wait, the stale copy of a moved VM is cleared, the HA
    rules are pushed on every VM change, and a server counts as lost only after 15 s.
  - An update refused before anything moved ends as Cancelled and doesn't block the next one.
  - The runner version is shown right after Update the runner.
  - A host counts as up to date only with its baseline AND no packages left below it; one extra pass runs if any are.
  - Disks are accepted by SCSI id, and the management network is shown on the network cards.
  - The MCP tools accept looser argument types.
- **CertBlaze:** Domeneshop as a DNS provider for public certificates (Let's Encrypt DNS-01).
