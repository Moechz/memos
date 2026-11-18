# Changelog

All notable user-visible changes to the Memos TOS application package.

## 26.11.1-1 — 2026-11-18

Remediation for the two listing reviews of `0.31.0-1` (x86_64 report 2026-11-11: C3, C4,
C5, C6, C8, T1; aarch64 report 2026-11-16: C2, C3, D16, F9, T1). Both records are published
with conditional approval; this is the version that carries the fixes.

### Changed
- Upstream upgraded **0.31.0 → 26.11.1** (the current Docker Hub release of
  `neosmemo/memos`), still run unmodified and as a non-root container. Existing data keeps
  working: the 0.31.x SQLite database is migrated in place on first start (schema
  `0.31.8 → 26.11.1`, two migrations; verified on hardware).
- The package `help` field (`config.ini`) now points at the privacy policy
  (`https://github.com/Moechz/memos/blob/main/PRIVACY.md`) instead of the issue tracker, so
  the policy ships with the package and is reachable from the App Center entry. Support and
  issue links stay in the README.
- The 23-language `important` and `release_note` texts now state the licence
  (MIT, unmodified) and link the privacy policy, and no longer imply the app is
  account-free — Memos keeps local accounts on the NAS.

### Added
- `PRIVACY.md` rewritten for this application: every data category the app stores (memos,
  attachments/avatars, local accounts and password hashes, session tokens, settings), the
  retention period, user rights, security measures, the deletion channel, and a third-party
  table that states the app makes **no outbound connection of its own** (no telemetry, no
  update check).
- `NOTICE.md` rewritten: MIT licence chain (upstream Memos → image → this packaging),
  image digest, attribution line, trademark statement and an icon/IP statement.
- README `## Privacy`, `## Third-party services`, `## Licence and attributions`,
  `## Support`; the licence table now says MIT (it wrongly said GPL-3.0).
- `docs/REVIEW-REPLY-26.11.1-1.md` — the item-by-item reply for both reports.

### Verified
- Official `TOSAppSelfTestingTool -c`: passes for both architectures.
- On-device E2E (TOS 7, official installer `-i`): 12/12 checks passed — healthy container,
  uid 1000, port 8030 published and listening, HTTP 200, restart and reinstall keep the
  data, absolute bind mount.
- `restart: unless-stopped` persisted on the container (`autoremove=false`), TOS starts
  `DockerEngine` at boot (`/etc/rc*.d/S01DockerEngine`) and the engine runs with
  `live-restore: true`, so the app comes back after a NAS restart.

## 0.31.0-1 — 2026-11-09

### Added
- First release. Memos (`neosmemo/memos:0.31.0`, Docker Hub official image, unmodified)
  packaged as a TOS 7 Docker application, published on port 8030 and running
  as a non-root user (1000:1000).
- Persistent data is kept under `/Volume1/DockerAppData/memosdocker/`.
