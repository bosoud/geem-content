# geem-content

Public distribution channel for the [Geem iOS app](https://apps.apple.com/sa/app/geem/id6759722911)
question packs. Hosts the manifest and the binary `.sqlite.gz` artifacts the
app downloads at runtime. All packs are free.

## What's here

- **`manifest.json`** — the source of truth for the catalog. The iOS app
  fetches it on launch from
  `https://raw.githubusercontent.com/bosoud/geem-content/main/manifest.json`
  and uses it to populate the in-app Packs page.
- **GitHub Releases** — each pack version is published as a release tagged
  `<id>-v<N>` (e.g. `ramadan-v2`) with a single asset:
  `pack-<id>.sqlite.gz`.

## How the app uses this repo

```
launch
  → GET manifest.json
  → for each entry where `published: true && bundled: false`:
      offer "Download" in the catalog
  → on tap, GET downloadURL
  → verify sha256
  → gunzip → SQLite → import into the app's question DB
```

## Manifest fields per pack

| field | meaning |
|---|---|
| `packID` | stable id (e.g. `ramadan`) |
| `version` | bump to force re-download on installed clients |
| `bundled` | `true` ⇒ ships inside the app; the URL is ignored |
| `published` | `false` ⇒ shown as "قريباً" in the app, not downloadable |
| `downloadURL` | release asset URL |
| `sha256` | integrity check; install fails if mismatched |
| `sizeBytes`, `questionCount` | informational, used in UI |

Authoring + build pipeline lives in the private iOS repo; this repo is the
distribution surface only.
