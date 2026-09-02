# DylNET Product Server

Public, static product metadata for DylNET software.

Each product has its own folder so update metadata can evolve independently and
future files such as MOTD/announcements can be added without changing unrelated
features.

```text
DylNET-Product-Server/
├── Ventara/
│   └── update.json
├── Karmalita/
│   └── update.json
└── README.md
```

## Ventara updater

Ventara's primary update source is GitHub's **Repository Contents REST API**:

```text
https://api.github.com/repos/DylNETGroup/DylNET-Product-Server/contents/Ventara/update.json
```

Ventara intentionally does **not** use a mutable `raw/.../main/...` URL as its
primary source. The API resolves the repository's current default branch and
returns the file data as a Git object response, avoiding the stale raw-CDN
behaviour that can show up during rapid manifest edits.

If the REST API is temporarily unavailable or rate-limited, Ventara falls back
to:

```text
https://raw.githubusercontent.com/DylNETGroup/DylNET-Product-Server/HEAD/Ventara/update.json
```

with no-cache headers and a unique cache-busting query value. `HEAD` means the
fallback also follows the repository's default branch rather than hard-coding
`main`.

No access token is required because this repository is public. Do not put
secrets, private keys, credentials, or private API tokens in this repository.

## `update.json`

Example:

```json
{
  "schemaVersion": 1,
  "product": "Ventara",
  "version": "2.0.0",
  "downloadUrl": "https://github.com/DylNETGroup/DylNET-Product-Server/releases/download/Ventara-v2.0.0/Ventara.zip"
}
```

`version` is the latest published version. `downloadUrl` is required when that
version is newer than the installed Ventara version and must be HTTPS.

Ventara accepts an optional `displayVersion`. Unknown fields are intentionally
ignored so this file can gain future metadata without breaking existing
clients.

## Publishing an update

1. Change Ventara's single local version in `Ventara.csproj`.
2. Build and test that version.
3. Publish the package as a GitHub Release asset.
4. Confirm the release/download exists.
5. Update `Ventara/update.json` **last** and commit it.

For production, prefer an explicit release tag URL, for example:

```text
https://github.com/DylNETGroup/DylNET-Product-Server/releases/download/Ventara-v2.0.0/Ventara.zip
```

This makes the JSON version and binary immutable as a pair. A
`releases/latest/download/...` URL is convenient but can later resolve to a
different release.

Ventara's installed version is sourced once from:

```xml
<Version>2.0.0</Version>
```

in `Ventara.csproj`; About and the updater both read that compiled version.

## Future product files

Keep independent features in independent files. For example, a future layout
could become:

```text
Ventara/
├── update.json
├── motd.json
└── announcements.json
```

No MOTD or announcement functionality is implemented just by adding those
files; this repository structure simply leaves room for them.
