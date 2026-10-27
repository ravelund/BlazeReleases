# Blaze 2026.10.15

A tested stack version, the same tool releases as Blaze 2026.10.14 with the bundles in this repository: they passed InstallBlaze's full stack test together on 2026-10-27
(a fresh install on two sites with a VIP each, adding a second server, and the take-over of an existing AuthBlaze
cluster with its leader on the second server).

| Tool | Version | sha256 |
|---|---|---|
| AuthBlaze | 2026.10.27.05 | 68f129471152f45eb32363465ca0480c886ff6b2f1cd01b80716e97d95716f54 |
| ProxyBlaze | 2026.10.26.08 | 95728796075816d3228f8132e261ec986e6d4fed51251827dae2b55b97e326b2 |
| CertBlaze | 2026.10.27 | b2b4035e2675a1455183145b2976192def2415db75fc86dec4efdc8ecadad1cd |
| NetBlaze | 2026.10.26.02 | e510952efbf7c61dca56d6184ca41937b1d0d285aa678939a926535b33ca86b8 |
| APMBlaze | 2026.10.27.02 | 495c5e6d9d64ab2eae9ac839c45ae0b9c6baaaa0dfe10f6767ed7e02b7b8a8ed |
| DnsBlaze | 2026.10.27.03 | 25069663f3ae596613d2bb10fbb6998e94c6775ff7fe55691321f1e2fed31a63 |

BlazeManager: 2026.10.27.06 or later.

## Files

- `manifest.json`: the versions, bundle names, sha256 and download address of each tool bundle (the release `v2026.10.15` here).
- The release also has `blazemanager-2026.10.27.06.tar.gz` and its `.sha256` for installing or updating BlazeManager itself.
- `manifest.json.sig`: its signature with the stack key (ECDSA P-256, SHA-256). The public key is
  `../stack-signing.pub` (key ID 4a3853a0d6fe86a0); BlazeManager has it pinned.

## Using it

- **With internet**: BlazeManager → Releases → upload `manifest.json` and `manifest.json.sig` (or
  `sudo ./install.sh --add-stack manifest.json`); it downloads the bundles from the addresses in the manifest.
- **Offline**: copy the six tool bundles (the files of the GitHub release `v2026.10.15`) and both manifest files
  to the BlazeManager server. Upload the bundles on the Releases page ("Upload a tool's release bundle"), then the
  manifest. BlazeManager checks every bundle against the sha256 in the signed manifest.
- Then update the stack: the Stack page, or `sudo ./install.sh --install "update=Blaze 2026.10.15"`.

Check by hand: `openssl dgst -sha256 -verify ../stack-signing.pub -signature manifest.json.sig manifest.json`.
