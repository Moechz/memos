# memos-for-tos

> TerraMaster TOS 7 App Center — **Docker application** package for
> [Memos](https://github.com/usememos/memos).

## What this is

A four-file TOS Docker application archive (`memosdocker.tar.gz`) that installs
Memos on TOS 7 through the App Center:

- `config.ini` — TOS application metadata (`application_type: docker`, id `memosdocker`)
- `memosdocker.lang` — 23-language superset (TOS requires 14)
- `memosdocker.svg` — application icon (1780 bytes, 4 SVG elements)
- `docker-compose.yml` — one service, image `neosmemo/memos:0.31.0`

## How it works

| | |
|---|---|
| Container image | `neosmemo/memos:0.31.0` (Docker Hub, used unmodified) |
| Open | `http://<NAS-IP>:8030/` (container port 5230) |
| Runs as | non-root, `user: "1000:1000"` |
| Persistent data | `/var/opt/memos` → `/Volume1/DockerAppData/memosdocker/data` |
| Logs | `docker logs memosdocker` (stdout/stderr) |

Everything Memos writes lives in `/var/opt/memos`, mounted from `/Volume1/DockerAppData/memosdocker/data`: the SQLite database, uploaded attachments and the instance configuration.

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
scripts/build.sh 0.31.0-2   # packaging iteration bump
scripts/build.sh 0.31.0-1 aarch64
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
2. Release **tag `v0.31.0-1`**, assets `memosdocker.tar.gz` +
   `memosdocker.tar.gz.sha256` (Docker asset naming: `<app_id>.tar.gz`, **no
   platform suffix**).
3. Developer platform → Add Application: ID `memosdocker`, package type Docker,
   repository URL, architecture `x86_64`.
4. Version Management → Add Version `0.31.0-1` → automated validation → review.

## License / branding

Memos is distributed by its upstream authors under **MIT**. The container image is the official image from Docker Hub (`neosmemo/memos:0.31.0`) and is used unmodified; no upstream source code is redistributed here. This packaging is an independent community submission by Moechz and is not affiliated with the upstream project or TerraMaster.

## Limitations

- The web UI is served over plain HTTP on the published port; use a TLS reverse proxy if you expose it outside your LAN.
- The first registered account becomes the administrator - create it right after the install, before opening the port to other people.
- The data folder is not visible in TOS File Manager; back it up over SMB/SSH or add the path to a TOS backup task.

---

Packaged for TOS 7 by Moechz. Upstream image used unmodified from Docker Hub.
