# Security and maintenance

PlexPort stores real credentials on your NAS and controls a downloader. Never post your
`data/plexport.sqlite`, your Plex token, qBittorrent password or Torznab URLs in an issue — the
database contains all of them in one file.

Use GitHub private vulnerability reporting **if the repository owner has enabled it**. No separate
confidential contact is configured in this repository. If private reporting is unavailable, open a
minimal issue asking for a private contact instead of describing the exploit.

## What this deployment trusts

- **The server binds `127.0.0.1` by default** and refuses to start on another interface without
  `PLEXPORT_PASSWORD` (`plexport/server.py`). Signing in as `admin` with that password is the only
  authentication layer: sessions are in memory (max 12 hours), the cookie is HttpOnly/SameSite=Strict,
  and password attempts are limited to eight per minute per IP. Behind Tailscale or a reverse proxy
  that counter shares one IP, and it resets on restart.
- **Credentials live in plaintext in one SQLite file** (`data/plexport.sqlite`, mode `0600`): the
  downloader login, Torznab keys and the Plex token. Anyone who can read that file, or a backup of
  it, owns your NAS media folders. Backups are your responsibility; the app has no snapshot rotation.
- **The Android app talks to your server over the LAN**, so `usesCleartextTraffic="true"` is set in
  its manifest; `allowBackup="false"` keeps its stored address and credentials out of cloud backup.
  TLS errors and file-access inclusion in the WebView follow the Android guidance recorded in
  `android/VALIDATIE.md`.
- **Downloads are untrusted input.** The importer validates paths, keeps the destination inside the
  configured media folders and only ever deletes a torrent it created itself, checked against its
  original save location.

## Maintenance procedure

- Run the fixture suites before release: `python3 -m unittest discover -s tests`,
  `node --test tests/*.cjs`, `node --check plexport/static/app.js`. CI runs these plus a Docker build.
- Build the release archive from the exact committed revision and publish its checksum
  (`docs/RELEASING.md`); NAS updates verify that checksum before replacing anything.
- Re-test on a real downloader/Plex pair after any change to submission, import or deletion paths —
  fixture tests do not prove that behaviour.

This documents the current design, not an audit. No response SLA is promised.
