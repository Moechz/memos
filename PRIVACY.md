# Privacy Policy — Memos for TerraMaster TOS (`memosdocker`)

**Last updated:** 18 November 2026
**Data controller / contact:** Moechz — moechz@163.com

This policy covers the TOS application package `memosdocker` (the “app”) — the four-file
Docker application archive — and the Memos instance it runs on your TerraMaster NAS. It is
linked from the package itself (the `help` field of `config.ini`, i.e. the “Help” link of
the App Center entry), from the store listing and from the developer website
(<https://www.moechz.com/privacy.html>, section 6).

## 1. Summary

- The app runs **entirely on your NAS**. The developer does not receive, collect or have
  access to any data you write into it.
- **No telemetry, no analytics, no usage reporting, no crash reporting, no update check**
  and no service operated by the developer. The app makes **no outbound connection of its
  own**.
- The only network traffic is the one you initiate (your browser to the app) and the Docker
  Hub pull when the image is installed.
- Memos itself has user accounts because it stores who wrote what; those accounts, their
  passwords and the notes stay in the database on your NAS.

## 2. What data is processed

| Container path | Stored on the NAS at | Content |
|---|---|---|
| `/var/opt/memos` | `/Volume1/DockerAppData/memosdocker/data` | the SQLite database (`memos_prod.db` with its `-wal`/`-shm` files) |

The database contains, because that is what the app is for:

- **Your memos** — text, Markdown, tags, visibility (private / protected / public), creation
  and update timestamps, and the public share tokens of memos you publish.
- **Attachments and resources** — images and other files you upload, and the avatar images
  you set, referenced from the database and kept in the same folder.
- **User records** — the username, optional display name, e-mail address and password hash
  of every account you create, plus the role (the first account becomes the administrator)
  and the session/access tokens issued to browsers that log in.
- **Settings** — instance name, access mode (private / protected / public) and other
  configuration you change in the UI.

Nothing else is collected: there is no device fingerprint, no IP logging by the app, no usage
statistics and no third-party SDK. The container writes nothing outside
`/Volume1/DockerAppData/memosdocker/`; its temporary files live inside the container and are
discarded with it.

## 3. Retention period

- **All application data** (memos, attachments, accounts, settings) stays in
  `/Volume1/DockerAppData/memosdocker/` until **you** delete it. There is no automatic
  expiry, no cloud copy and no upload.
- **Uninstalling the app** without “delete data” keeps the folder, so a reinstall (or an
  upgrade to a later version) reuses your notes.
- **Container logs** (stdout/stderr) are kept by the Docker daemon according to its own log
  rotation settings, not by this package.
- **Website / server logs (`moechz.com`):** short-lived operational logs, unrelated to the
  data the app processes and not used for profiling.

## 4. Your rights and the deletion channel

| Right | How to exercise it |
|---|---|
| **Access / export** | Copy `/Volume1/DockerAppData/memosdocker/` — it is the complete state. The UI also offers an export of all memos (Settings → Data), and the API returns everything your account can see. |
| **Correct** | Edit the memo or the settings in the UI; account details (display name, e-mail, password) are changed in the profile page. |
| **Delete** | Delete individual memos/attachments in the UI (this removes the rows from the database; files no longer referenced can be cleaned from the data folder), or delete everything by uninstalling the app with **“delete data”**, or by stopping the container and removing `/Volume1/DockerAppData/memosdocker/`. |
| **Restrict** | Use the per-memo visibility setting (private / protected / public) and the instance access mode to control who can read what; “private” is the default. |
| **Assistance / complaints** | E-mail **moechz@163.com** with the subject “Memos data request”; a reply follows within 30 days. |

**Deletion channel.** The developer holds no copy of your data, so deletion is completed by
removing the folder on your own NAS (self-service, immediately effective) or by deleting the
records in the UI. The e-mail address above covers questions, and requests about the website,
the store listing and the publishing records. Deleted data cannot be recovered by the
developer, because it was never sent.

## 5. Security measures

- The container runs as **uid 1000**, never root; it is **not** privileged, does **not** use
  host networking, does **not** mount the Docker socket and adds no capabilities.
- Exactly **one** port is published (host `8030` → container `5230`). Nothing else is exposed.
- The image is pinned to `neosmemo/memos:26.11.1` (Docker Hub, used unmodified); the package
  is rebuilt and re-released when upstream ships fixes — this version was produced for
  exactly that reason (the previous listing ran 0.31.0).
- Data is confined to the application data folder and owned by the app user; no secrets are
  baked into the package.
- Passwords are stored as hashes by Memos; sessions use access tokens. Instance
  administration (users, settings, storage) is available to the administrator account only.
- **You** must protect the endpoint: the UI speaks **plain HTTP**, so keep port 8030 on the
  LAN or put it behind a TLS reverse proxy, and keep the NAS share permissions tight — the
  SQLite database is readable by the app user and contains everything listed in §2.
- Back up the data folder before uninstalling with “delete data”; there is no cloud backup.

## 6. Third-party sharing and API disclosure

The developer shares nothing, sells nothing and operates no server in this path. No data is
sent to the upstream Memos project either — Memos has **no update check and no telemetry**.

| Service | Endpoint | Data sent | Destination / region |
|---|---|---|---|
| Your browser → the app | `http://<NAS-IP>:8030` | the memos you write, your login | your own NAS / LAN |
| Optional integrations **you** configure (SSO/OAuth provider, S3-compatible storage, webhook, a Memos instance you connect to) | the URL you enter | only the data that feature needs (e.g. an OAuth subject, the uploaded file) | the provider you chose |
| Docker image pull | `registry-1.docker.io` or your registry mirror | anonymous pull request, no account data | Docker Hub, US / your mirror |
| Update check | **none** — neither Memos nor this package contacts anything on its own | — | — |
| Developer website | <https://www.moechz.com/> | only if you open it in a browser | the site's hosting provider |
| Public share links | the link you create (`/m/<token>`) | the single memo you published | served by your own NAS |

If you leave all optional integrations empty (the default), the app talks to nothing except
your browser and the image registry. Full detail on what is stored where: §2.

## 7. Children

The app is a self-hosted note-taking utility and is not directed at children. It has no
advertising and no user tracking.

## 8. Changes to this policy

Material changes are published in this file and in the repository
(<https://github.com/Moechz/memos>), and are reflected in the “Last updated” date above. The
policy shipped with a given package version is the one that applies to it.

## 9. Contact

Moechz — **moechz@163.com** — <https://www.moechz.com/privacy.html>

Package repository: <https://github.com/Moechz/memos> ·
Upstream project: <https://github.com/usememos/memos> (MIT licence)
