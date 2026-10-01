# Portlight snippet gallery

The ready-made server commands shown in the
[Portlight](https://tudiapps.com) app's snippet gallery: disk and memory,
services, logs, Docker, nginx, network, packages.

- `v1.json` — the catalog. English and Turkish titles and descriptions, a
  risk label per command (`read`, `write`, `destructive`).
- `v1.json.sig` — its ed25519 signature. The app checks it against a public
  key built into the app and ignores any catalog that does not verify, is
  malformed, or is not newer than the one it has. Editing this repository
  alone cannot put a command on anyone's phone.

The app never runs a gallery command by itself: it shows the exact command
and its risk first, and a destructive one needs a second confirmation. It
downloads the catalog with a plain, anonymous request — no cookies, no
identifiers.

Suggestions are welcome as issues or pull requests; accepted ones are
signed and published from Portlight's main repository. Format:
[FORMAT.md](FORMAT.md).
