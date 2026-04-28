# CLAUDE.md

## Project purpose

Helper library for local mapping of TACACS+-authenticated users that have no `/etc/passwd` entry. Stores per-session mappings (uid/gid/home) so `libnss-tacplus` can answer `getpwnam` for users that came in via `libpam-tacplus`. Ships `libtacplus-map1` (runtime) and `libtacplus-map-dev` (headers); also includes `dumpmap` (debug tool) and a sudoers fragment.

## Tech stack

- C, GNU autotools (`configure.ac`, `Makefile.am`, `auto.sh`).
- Debian packaging in `debian/` (debhelper >= 9, `dh-autoreconf`, `autoconf-archive`, `libaudit-dev`).
- License: GPL-2.0 (per `COPYING`).

## Build / test / run

```sh
./auto.sh
make
dpkg-buildpackage -us -uc
```

No bundled tests; verified end-to-end via `libnss-tacplus` + `libpam-tacplus` on a VyOS image.

## Repository layout

- `map_tacplus_user.c`, `map_tacplus_user.h` — mapping API.
- `dumpmap.c` — diagnostic tool that dumps the active mapping.
- `tacplus.sudo` — sudoers integration snippet.
- `debian/` — packaging.

## Cross-repo context

Sits between `libpam-tacplus` (which authenticates the user and creates the mapping) and `libnss-tacplus` (which reads the mapping for NSS lookups). Built via `VyOS-Networks/vyos-build-packages`; consumed by `vyos/vyos-build`. Sibling auth packages: `libnss-mapuser`, `libpam-radius-auth`.

## Conventions

- Default branch `master`.
- Commit / PR title format: `component: T12345: description` (Phorge task ID at https://vyos.dev).
- Keep API stable — `libnss-tacplus` and `libpam-tacplus` link against this and are version-coupled.

## Mirror relationship

Mirror twin: `VyOS-Networks/libtacplus-map`. Canonical side is here.

## Notes for future contributors

- README dates to 2016 (`libtacplus_map v1.0.0`). Tracking patches against the original Cumulus / Dell drop is the maintenance posture, not active development.
- Bump `libtacplus-map1` soname on any ABI change — both `libnss-tacplus` and `libpam-tacplus` build-depend on the dev package.

---

This file is mirrored on Confluence: [`vyos/libtacplus-map`](https://internal.confluence.vyos.com/wiki/spaces/VYOS/pages/818479396). The Confluence page also carries the per-repo audit data (settings, workflows, secret counts, hygiene) that complements this CLAUDE.md. Edit either side; resync via the documentation pipeline.
