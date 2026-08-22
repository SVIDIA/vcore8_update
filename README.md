# VCore8 NVR Update Channel

This repository is the auto-update channel for **VCore8 NVR** servers. It carries
two things and nothing else:

- **the feed** — [`json/vcore8_update.json`](json/vcore8_update.json) on `main`.
  A VCore8 server checks it nightly (inside its configured update window) and
  compares the full four-component version against what it is running.
- **the artifacts** — GitHub Release assets named `vcore8-update-<version>.exe`
  under tag `v<version>`. The feed's `URL` always points at exactly one of them.

The feed flip is the go-live: a release asset is uploaded and verified **before**
the feed starts naming it, and reverting the feed to its previous content rolls
the channel back.

## What a server verifies before applying an update

1. **SHA256** — the downloaded artifact must hash to exactly the feed's `SHA256`
   (and match the feed's `Size`).
2. **Authenticode** — the artifact's signature chain must verify, and the leaf
   certificate CN must be **SVIDIA LLC**.

An artifact failing either check is discarded and never executed.

## These artifacts are UPGRADE-ONLY

`vcore8-update-<version>.exe` refuses to run as anything but an upgrade of an
existing VCore8 installation:

- **fresh installation** — refused,
- **migration** of a legacy VCore/VSRV8 server — refused,
- **adoption** of a manually-placed installation — refused,

each with **exit code 3** and a message. The clean-install/migration payloads are
physically absent from these artifacts. For a new deployment or a migration,
obtain the full `vcore8-setup-<version>.exe` installer from SVIDIA — do not try
to bootstrap a box from this channel.

## Feed format

```json
{
  "Name": "VCORE8",
  "Version": "8.0.712.0",
  "URL": "https://github.com/SVIDIA/vcore8_update/releases/download/v8.0.712.0/vcore8-update-8.0.712.0.exe",
  "INFO_URL": "https://github.com/SVIDIA/vcore8_update#readme",
  "SHA256": "<lowercase hex sha256 of the artifact>",
  "Size": 123456789
}
```

`Name` must be `VCORE8`; servers ignore a feed with any other name. `Version` is
the full `x.y.z.w` tuple and only ever moves forward. Publishing is done by
SVIDIA's `publish_update.ps1`, which uploads and hash-verifies the release asset
first and rewrites this feed last.

## Changelog

### 8.0.712.0
- Channel bootstrap: the shipped baseline at the time the update channel was
  created. Later entries are added here with each published update.
