# Blaze 2026.11.30

**Every tool installs with the family's .NET installer, the database images take a libxml2 security fix, and
DnsBlaze's host resolver works under a search domain. BlazeManager 2026.11.21.01.**
Every tool built on the build server and signed on the release PC.
Tested in the lab:
- **Upgrade, two-server stack** (blaze02 + blaze01, one VIP on both Edges): every tool upgraded to these versions,
  ProxyBlaze and DnsBlaze as clusters (`--upgrade-cluster`, ProxyBlaze also with a rollback on a broken build); then
  BlazeManager's stack update (every pool as planned, every site through both Edges) and the Stack page compliant.
- **Fresh install** on a new server through BlazeManager: the whole stack, BlazeManager moved behind ProxyBlaze,
  APMBlaze added afterwards, a tool removed with its data kept and installed again.
- **Sign-in:** a full AuthBlaze sign-in 6× on every tool.

| Tool | Version | sha256 |
|---|---|---|
| AuthBlaze | 2026.11.21 | e3b6a24797af5e45f217772351605f4e6f7f9fabababd63764c1578e6591120d |
| ProxyBlaze | 2026.11.20.03 | 972b21c95fffc6ee50c7427ce5f2c56383bdd10e3404c67b253c27c51df7a850 |
| CertBlaze | 2026.11.20.03 | f16793b213c5d6e82de62e2f226841a4e7aef4121e7753209643d5513e8a3463 |
| NetBlaze | 2026.11.21 | cb4323425ff906dbe694cc2dc6f19852e33e4ddf138071e89ea84e01f06c5a47 |
| APMBlaze | 2026.11.20.03 | 447aa2bb1474d4d08bc4cc3f338dfa36615b1755b148d7e82ba00ea681162fd7 |
| DnsBlaze | 2026.11.21.02 | b802671a53948ca2531a78e7bf34eab38f8e6352cf88b91c406bc7412136ca89 |
| TowerBlaze | 2026.11.20.03 | b821a34f227b7768143d86aa7e548412a74467aeeabf87e54b27fadc7eb857ab |

Bundles: every tool's bundle and BlazeManager 2026.11.21.01 on this release (`v2026.11.30`, each with its .sha256).

This is the GitHub copy of the internally published Blaze 2026.11.29 (same bundles, same sha256; its own number because
the signed links differ, and GitHub's Blaze 2026.11.29 is already the copy of the internal 2026.11.28).

BlazeManager: **2026.11.21.01** or later (3edc878340a87353ff1f5a70be6485e24b47b774f29c0395242275037d13f0ae).

## How to update

1. **Update BlazeManager first** (Updates page).
2. **Servers → Update the runner** on every other server. A server whose runner is behind blocks the stack update.
3. **Stack page → Update the whole stack to Blaze 2026.11.30.**

To know before you start:
- **The installers are .NET programs now.** `install.sh` starts the tool's own installer from the bundle, with the same
  options; renamed options keep their old names for one more release (they print "renamed to"). The bash installer
  comes along once as `install-legacy.sh`. Every command takes `--json`; the exit codes are the family's: 0 done,
  1 failed, 2 a wrong option, 3 rolled back, 4 the rollback failed too, 5 refused. Scripts that read the old
  "Healthy." line or exit code 1 need a look: `--status` now ends with `Health: Healthy`.
- **Every tool's database container restarts once** in its upgrade (same data, a backup first): the database image
  is rebuilt with Alpine's fixed packages.
- **Upgrading by hand:** copy a release over the folder with `cp -a --remove-destination NEW/. FOLDER/` (a plain
  `cp -a` stops at the running installer).

## What's new since Blaze 2026.11.29 (GitHub)

- **One installer for the family.** Every tool's install.sh runs a .NET installer on the shared installer kit:
  `--json` on every command, `--status --json` in snake_case throughout, a "Last upgrade" sentence in `--status`,
  `./install.sh --uninstall [--delete-data]` (keeps the data and the images unless asked), `.env` written by copy,
  fsync and rename (a full disk can't empty it), a clear `ERROR: …` instead of a crash.
- **Security:** every PostgreSQL image takes Alpine's fixed packages (libxml2 2.13.9-r3, CVE-2026-86144);
  PostgreSQL stays 17.12. The installers read the files the containers can write more carefully. BlazeManager shows
  its master key only to a person at a terminal (a captured log names the file instead).
- **DnsBlaze:** `--host-resolver on` routes every zone DnsBlaze holds beside `~.`, so a network card's search domain
  no longer sends names in DnsBlaze's zones to the network's DNS servers (kept up to date by every root run and the
  hourly certificate timer; `--host-resolver preview` shows it). A follower's sync never stops on the updater's files.
- **ProxyBlaze:** a rollback after `--upgrade-cluster` puts the old release's files back too.
- **AuthBlaze:** a node removed from the cluster says so on `/health/detail`; mixed clusters (new and bash installer)
  stay in sync and upgrade together.
- **BlazeManager:** reads both spellings of a tool's cluster result, says in words when the runner bundle can't be
  read or a sign-in client ID is taken.

Also running in production since 2026-11-21 (upgraded from Blaze 2026.11.28 through BlazeManager).
