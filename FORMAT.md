# Snippet gallery — format and publishing

The gallery is a list of ready-made server commands shown under
**Snippets → Gallery**. One copy ships in the app
(`app/assets/snippet_gallery/`); newer ones are published to
[github.com/tudiapps/portlight-snippets](https://github.com/tudiapps/portlight-snippets)
and fetched from
`https://raw.githubusercontent.com/tudiapps/portlight-snippets/main/v1.json`
(and `v1.json.sig`).

## Trust

A gallery command ends up typed into a terminal on someone's server, so a
catalog is trusted only if:

1. `v1.json.sig` is a valid **ed25519** signature over the exact bytes of
   `v1.json`, made with the key whose public half is
   `GallerySignature.portlightPublicKey`. Checked before parsing. A
   compromised GitHub repository or account cannot publish a command.
2. It parses strictly (below). One bad entry rejects the whole catalog.
3. Its `version` is higher than the catalog in use — no rollback to an
   older catalog, even a correctly signed one.

The app never runs a gallery command on its own. It picks a session, fills
in `{{slots}}` with a preview, then shows the exact command, the server and
the risk tag; a destructive command also needs the server's name typed in.
The risk tag is the higher of the catalog's label and the app's local
classifier on the command as filled in, so a mislabelled entry cannot look
safer than it is.

The download is a plain anonymous GET: no cookies, no identifiers, at most
512 KB, 15 s timeout, no redirects. Offline, the bundled or last verified
copy is shown.

## Format (`format: 1`)

```json
{
  "format": 1,
  "version": 2,
  "categories": [
    { "id": "disk", "title": { "en": "Disk & memory", "tr": "Disk ve bellek" } }
  ],
  "snippets": [
    {
      "id": "du-dir",
      "category": "disk",
      "title": { "en": "Biggest folders", "tr": "En büyük klasörler" },
      "description": { "en": "…", "tr": "…" },
      "command": "du -h --max-depth=1 {{path}} | sort -hr | head",
      "risk": "read"
    }
  ]
}
```

- `id`: `[a-z0-9][a-z0-9-]*`, at most 64 characters, unique, stable across
  versions.
- Text: English (`en`) required, other languages optional; at most 300
  characters; no control, zero-width or bidi characters.
- `command`: at most 1000 characters, one line, same character rules.
  `{{name}}` slots are filled in at run time.
- `risk`: `read`, `write` or `destructive` — label honestly; the app only
  ever raises it.
- At most 500 snippets.

## Publishing a new catalog

1. Edit `app/assets/snippet_gallery/v1.json` and **increase `version`**.
2. Sign: `cd tools/snippet_gallery && dart run bin/sign.dart`. It refuses an
   invalid catalog and a signature the shipped key would not accept.
3. Commit and push the monorepo (the next app release bundles it).
4. `tools/publish-snippets.sh` copies the signed pair to the public
   repository; apps pick it up the next time the gallery opens.

The signing seed lives in `~/.portlight/snippet-gallery-ed25519.key`
(override with `PORTLIGHT_GALLERY_KEY`) and is never committed. Losing it
means shipping an app update with a new public key; leaking it means the
same, urgently.
