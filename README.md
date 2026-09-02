# DylNET Product Server

A small public GitHub-hosted product-data source for DylNET software.

Each product has its own folder. Ventara currently uses only `update.json`,
but the layout intentionally leaves room for other product-specific files in
the future without changing the update-check design.

```text
DylNET-Product-Server/
├── Ventara/
│   └── update.json
├── Karmalita/
│   └── update.json
└── README.md
```

## Product folders

Ventara only has to fetch `Ventara/update.json`, Karmalita only has to fetch
`Karmalita/update.json`, and future products can be added as another folder.
It also keeps product-specific metadata independent if you later add release
notes, hashes, minimum versions, or update channels.

## One-time setup

1. Create a **public** GitHub repository named `DylNET-Product-Server`.
2. Upload the contents of this ZIP to the repository root on the `main` branch.
3. Replace `YOUR-GITHUB-USERNAME` in each product's `update.json` with the
   GitHub account or organisation that owns the repository.
4. In Ventara, open `Updates/UpdateConfiguration.cs` and set `GitHubOwner` to
   the same account/organisation name.
5. Create GitHub Releases and upload each product's update package as a release
   asset. The sample files expect `Ventara.zip` and `Karmalita.zip`.

## Ventara

Ventara reads:

```text
https://raw.githubusercontent.com/<owner>/DylNET-Product-Server/main/Ventara/update.json
```

A Ventara update file looks like this:

```json
{
  "schemaVersion": 1,
  "product": "Ventara",
  "version": "2.0.0",
  "downloadUrl": "https://github.com/YOUR-GITHUB-USERNAME/DylNET-Product-Server/releases/latest/download/Ventara.zip"
}
```

Ventara's installed version has one source of truth: the `<Version>` value in
`Ventara.csproj`.

For example:

```xml
<Version>2.0.0</Version>
```

is displayed in Ventara as `V2.00`.

To publish V2.00:

1. Build/package Ventara V2.00.
2. Create a GitHub Release and upload it as `Ventara.zip`.
3. Change `Ventara/update.json` to `"version": "2.0.0"` and make sure the
   `downloadUrl` points at the release asset.
4. Commit the updated file.

A V1.00 Ventara install will then show `V1.00 > V2.00` and its update button
will become **Download update**.

## Ventara button states

- Initial: **Check for updates**
- While checking: **Checking...** (disabled)
- Up to date: **Check again**
- Update available: **Download update**
- Server/network failure: **Try again**

`Download update` opens the configured link in a normal Ventara browser tab.
If the URL is a direct release-asset link, Ventara's download manager handles
that download.

## Optional fields

Ventara currently only requires `version`. `downloadUrl` is required when a
newer version is available. It also accepts an optional `displayVersion` if a
future release needs custom display text instead of Ventara's normal version
formatter.

Because each product has its own folder, you can later add other independent
product files alongside `update.json` without changing Ventara's updater.
No MOTD or other remote-content feature is implemented by the supplied Ventara
build; this repository layout simply leaves room for those additions later.

Within `update.json`, useful future fields could include `releaseNotes`,
`sha256`, `minimumVersion`, and `channel`.
