# NOTICE — third-party components and attributions

This package (`memosdocker`, TOS 7 Docker application) is an independent packaging by Moechz of
the upstream **Memos** project. It is not affiliated with, sponsored or endorsed by the Memos
authors or by TerraMaster.

## 1. Upstream application — Memos

- Project: <https://github.com/usememos/memos>
- Homepage: <https://www.usememos.com/>
- Licence: **MIT** (`LICENSE` in the upstream repository)
- Relationship: the upstream application is used **unmodified**, inside the container
  image. No upstream source code is redistributed in this package.

## 2. Container image — `neosmemo/memos:26.11.1`

- Registry: Docker Hub (`neosmemo/memos:26.11.1`), digest
  `sha256:5dcd6e6b973a55b964f2fe2ccdaa33319463ab448a23404ebb5d2acbd19299ba`
  (x86_64; the same tag is published for aarch64 and armv7)
- Maintainer: the Memos project (`neosmemo`), built by the upstream release pipeline
- The image is pulled from Docker Hub (or the user's registry mirror) at install time and
  is **not** redistributed inside this package.

## 3. Components inside the image

The image is built by the Memos project: the Go server binary, the compiled web UI assets
and a minimal base system with `wget` (used by this package's healthcheck). Their licences
are documented in the image's own metadata and upstream repository — MIT for Memos itself;
the base system utilities under their respective licences. They are shipped inside the image
only and are not redistributed as separate files by this package.

Attribution: Copyright © the Memos authors. Memos is distributed under the MIT licence; the
full text is available at <https://github.com/usememos/memos/blob/main/LICENSE>.

## 4. This packaging

The files authored for this package — `docker-compose.yml`, `config.ini`,
`memosdocker.lang`, `memosdocker.svg`, the build/verification scripts and this
documentation — are released under the **MIT** licence (see `LICENSE`). The upstream
application stays under MIT; no upstream source code is included here.

## 5. Trademarks

“Memos” is the name of the upstream project; “TerraMaster”, “TOS” and “App Center” are
trademarks or registered trademarks of their respective owners. Their use here is
descriptive only (to identify the software being packaged and the platform it runs on). No
TerraMaster artwork or logo is reproduced in this package.

## 6. Icon

The application icon in `src/memosdocker.svg` is an original mark drawn for this package
(a rounded tile with an abstract note/wave glyph, labelled “Memos”), used to identify the app
it installs. It does not reproduce the upstream Memos logo, nor any TerraMaster or App Center
artwork.

## 7. Contact

Moechz — **moechz@163.com** — <https://www.moechz.com/>
Privacy policy: [PRIVACY.md](PRIVACY.md)
