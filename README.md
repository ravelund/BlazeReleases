# BlazeReleases

Signed release builds of the Blaze stack. No source code.

- `stacks/blaze-<version>/`: a tested stack version (`manifest.json` + `manifest.json.sig`). Its README lists the
  tool versions.
- Release `v<version>`: the same manifest and signature, plus every tool bundle (`<tool>-<version>.tar.gz`) the
  manifest names. The manifest's `url` fields point at these release files.
- `stacks/stack-signing.pub`: the public half of the stack key (key ID 4a3853a0d6fe86a0). BlazeManager pins it and
  verifies every manifest against it; each bundle is checked against the sha256 in the signed manifest.
