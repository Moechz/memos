# memos-for-tos

> TerraMaster TOS 7 App Center — **Docker application** package for
> [Memos](https://github.com/usememos/memos).

## What this is

A four-file TOS Docker application archive (`memosdocker.tar.gz`) that installs
Memos on TOS 7 through the App Center:

- `config.ini` — TOS application metadata (`application_type: docker`, id `memosdocker`)
- `memosdocker.lang` — 23-language superset (TOS requires 14)
- `memosdocker.svg` — application icon (1780 bytes, 4 SVG elements)
- `docker-compose.yml` — one service, image `neosmemo/memos:26.11.1`

## How it works

| | |
|---|---|
| Container image | `neosmemo/memos:26.11.1` (Docker Hub, used unmodified) |
| Open | `http://<NAS-IP>:8030/` (container port 5230) |
| Runs as | non-root, `user: "1000:1000"` |
| Persistent data | `/var/opt/memos` → `/Volume1/DockerAppData/memosdocker/data` |
| Logs | `docker logs memosdocker` (stdout/stderr) |

Everything Memos writes lives in `/var/opt/memos`, mounted from `/Volume1/DockerAppData/memosdocker/data`: the SQLite database, uploaded attachments and the instance configuration.

## Privacy

This application runs entirely on your NAS. **The developer does not collect, receive or
have access to any data processed by the application.** There is no telemetry, no analytics,
no usage reporting and no developer-operated account. Memos itself keeps local user accounts
because it stores who wrote which memo; those accounts never leave the NAS.

| Topic | Detail |
|---|---|
| Data processed | The SQLite database in the data folder: your memos (Markdown, tags, visibility, share tokens), uploaded attachments and avatars, local user accounts (username, optional e-mail, password hash, role), session tokens and instance settings. |
| Where it is stored | The application data folder (`/Volume1/DockerAppData/memosdocker/`) on your NAS. Nothing is uploaded to the developer. |
| Retention period | Until **you** delete it — there is no automatic expiry and no cloud copy. Uninstalling keeps the data unless you tick “delete data”. Container logs follow the DockerEngine log rotation. |
| Your rights | **Access / export**: copy the data folder, or use the UI export (Settings → Data). **Correct**: edit the memo/profile in the UI. **Delete**: delete memos in the UI, or uninstall with “delete data”, or remove the folder after stopping the container. **Restrict**: per-memo visibility (private / protected / public) and the instance access mode. |
| Deletion channel | Delete the folder yourself, or email **moechz@163.com** (answered within 30 days). |
| Security measures | Container runs as uid 1000 (never root), no `privileged`, no host network, no `cap_add`, no Docker socket, one published port; image pinned to `neosmemo/memos:26.11.1`; passwords stored as hashes, admin-only administration; the UI is plain HTTP, so keep it on the LAN or behind a TLS reverse proxy. |
| Children | Not directed at children. |

**Full privacy policy:** [PRIVACY.md](PRIVACY.md) —
<https://github.com/Moechz/memos/blob/main/PRIVACY.md> — and the website version at
<https://www.moechz.com/privacy.html>.

## Third-party services (API disclosure)

The developer operates no server in this path, and neither Memos nor this package has an
update check or telemetry: the app makes **no outbound connection of its own**.

| Service | Endpoint | Data sent | Destination / region |
|---|---|---|---|
| Your browser → the app | `http://<NAS-IP>:8030` | memos you write, your login | your own NAS / LAN |
| Optional integrations you configure (SSO/OAuth provider, S3-compatible storage, webhook, another Memos instance) | the URL you enter | only the data that feature needs | the provider you chose |
| Image pull | `registry-1.docker.io` or your registry mirror | anonymous pull | Docker Hub, US / your mirror |
| Update check | none | — | — |

See `PRIVACY.md` §6 for the full table. Disable any integration in the app's settings if you
do not want it.

## Licence and attributions

| Component | Licence | Notes |
|---|---|---|
| Memos | MIT | upstream application, used unmodified inside the image |
| Container image `neosmemo/memos:26.11.1` | Memos MIT; base system utilities under their own licences | pulled from Docker Hub at install time, not redistributed here |
| This packaging | MIT (see `LICENSE`) | compose file, metadata, translations, icon, scripts |

“Memos”, “TerraMaster” and “TOS” are trademarks of their respective owners. This is an
independent community package by Moechz and is not affiliated with the upstream project or
TerraMaster. Full details: [NOTICE.md](NOTICE.md).

## Design highlights

- **Non-root.** `user: "1000:1000"`; verified on real hardware (the container reports uid 1000).
- **Fixed image tag, never `:latest`**, Docker Hub only, no `privileged`, no `network_mode: host`, and no Docker socket, no `cap_add`.
- **Health check** probing the service on container port 5230.
- **Deterministic build**: GNU tar with uid/gid 0 and zeroed timestamps, gzip without a timestamp, plus the review-standards self-check above.
- **No database server.** Memos embeds SQLite; the container starts in a couple of seconds and there is no extra service to supervise.
- **One volume, one port.** Only `/var/opt/memos` is persistent and only container port 5230 is published; no Docker socket, no privileged mode, no host networking.

## Runtime file manifest

Everything the application writes on the NAS lives inside its own data
directory; the container-internal `/tmp` is discarded with the container. The
table lists what is created and how it behaves. Nothing else is written.

| Path | Purpose | Created when | Growth bound | Lifecycle |
|---|---|---|---|---|
| `/Volume1/DockerAppData/memosdocker/data` | Memos application data (everything the container writes outside /tmp) | on first start / on demand | bounded by your own data | persistent; kept on uninstall unless you ask to delete it |

## Build

```bash
scripts/build.sh                  # -> out/memosdocker.tar.gz + .sha256
scripts/build.sh 26.11.1-2  # packaging iteration bump
scripts/build.sh 26.11.1-1 aarch64
```

The build stages the four files, substitutes the version/platform placeholders,
removes CRLF/BOM/AppleDouble, then runs
`tools/verify.py`, a review-standards self-check (archive layout, JSON validity,
required fields, 14+ language coverage, version consistency, Docker-Hub-only
images, fixed tag, reserved/recommended host ports, non-root user, per-service
healthcheck/TZ/restart, data path below the application data root, never the
`/Volume*` wildcard, comment-free sequence blocks, `x-app-meta` position, no
literal secrets and the SVG size/element limits).

## Local verification

```bash
./TOSAppSelfTestingTool -c out/memosdocker.tar.gz   # official spec check
bash test/install-and-verify.sh                  # on-device E2E (needs root)
```

> Docker applications cannot be side-loaded through the App Center's manual
> install page (it accepts `.deb` only); the official self-testing tool's `-i`
> mode or `docker compose` are the only ways to install one for testing.

## Store submission checklist

1. Public GitHub repo (code + README only).
2. Release **tag `v26.11.1-1`**, assets `memosdocker_x86_64.tar.gz` / `.sha256` and
   `memosdocker_aarch64.tar.gz` / `.sha256` (Docker asset naming: `<app_id>_<arch>.tar.gz`;
   the store wants one package per architecture).
3. Developer platform → Add Application: ID `memosdocker`, package type Docker,
   repository URL, architecture `x86_64` and `aarch64`.
4. Version Management → Add Version `26.11.1-1` → automated validation → review.
   In the submission remarks, link the privacy policy
   (<https://github.com/Moechz/memos/blob/main/PRIVACY.md>).


## Limitations

- The web UI is served over plain HTTP on the published port; use a TLS reverse proxy if you expose it outside your LAN.
- The first registered account becomes the administrator - create it right after the install, before opening the port to other people.
- The data folder is not visible in TOS File Manager; back it up over SMB/SSH or add the path to a TOS backup task.

---

Packaged for TOS 7 by Moechz. Upstream image used unmodified from Docker Hub.

## Support

- Issues and questions: <https://github.com/Moechz/memos/issues>
- Email: moechz@163.com
- Privacy: [PRIVACY.md](PRIVACY.md) · Licence: [NOTICE.md](NOTICE.md)
