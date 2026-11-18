# BlazeOS 2026.11.18.01

BlazeOS 13 (Debian 13.7 "trixie", kernel 6.12.117) for Blaze servers and general Docker hosts, as an OVA for vCenter plus the
`blazeos-host` package. **BlazeManager 2026.11.17.08 is preloaded** (stack Blaze 2026.11.29), so a new first server starts on
the current BlazeManager.

**First signed BlazeOS release.** Check a download before you deploy it:

```sh
sha256sum -c SHA256SUMS
openssl dgst -sha256 -verify blazeos-release-signing.pub -signature SHA256SUMS.sig SHA256SUMS   # Verified OK
```

The key's SHA-256 fingerprint starts with `35622ceb0a02ccae` (`openssl pkey -pubin -in blazeos-release-signing.pub -outform DER | sha256sum`).
Compare it with a copy you got another way (stacks/blazeos/release-signing.pub in this repository) before you trust it.

| File | sha256 |
|---|---|
| blazeos-13-2026.11.18.01.ova | 9a682fb1a7e3073a207a3cc1ecd98316b5bdf64af0d300f6166d9ae1e953027f |
| blazeos-host_2026.11.18.01_all.deb | 65d4fd6ec4eb06b29ac3a9c9cc04abbaf74260ac96caa605496916ae9ae49093 |

Tested by deploying the OVA in vCenter: first boot under a minute, BlazeManager 2026.11.17.08 installed and healthy, the
stack versions fetched from this repository with their signature checked, admin sign-in with the deploy password, the data
disk at /var/lib/docker, the host firewall on with Docker-published ports filtered, apply and confirm.

## What's new since 2026.11.15.01

- BlazeManager 2026.11.17.08 preloaded (was 2026.11.15.x).
- `blazeos-firewall apply` keeps `published_ports`, `blaze_nodes` and `backend_from` when firewall.conf leaves them out
  (before, an apply could turn the published-port filter off). `status --json` has a `warnings` list.
- `blazeos-blazemanager status --json`; `blazeos-data-disk status --json`, `grow` and `add` (used by BlazeManager's screens).
- Debian packages: current as of 2026-11-18. Debian published no updates for this package set since 2026.11.15.01.

## Known vulnerabilities without a Debian fix

Trivy rootfs scan: 0 fixable HIGH or CRITICAL. Open, with no fix in Debian 13 yet:

- **CVE-2026-60002 (OpenSSH, CRITICAL at NVD, Important at Red Hat)** affects only the ssh **client** when it connects to a
  malicious server; sshd is not affected. Debian rates it a minor issue with no fix in trixie yet. Don't use ssh/scp from a
  BlazeOS server to hosts you don't trust.
- **CVE-2026-6653 (libxml2, CRITICAL at NVD, Moderate at Red Hat):** a crash on crafted XML. On BlazeOS only VMware's
  guest authentication service (open-vm-tools) uses libxml2, and its XML comes from vCenter guest operations, not from
  the network. No Debian fix yet.
- The five HIGH OpenSSH findings need GSSAPI authentication or tunnel settings that BlazeOS doesn't enable, or a crafted
  username on the ssh client's command line.
- The other HIGH findings (krb5, python3.13, util-linux, expat, containerd/runc, sqlite, …) have no Debian fix yet; they
  come with later Debian updates (unattended-upgrades installs them) and later BlazeOS builds.

From now on BlazeOS is rebuilt with every new Blaze stack: the newest BlazeManager preloaded and current Debian packages.
