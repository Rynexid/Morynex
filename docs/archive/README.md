# Premation Upstream Archive

## Purpose

This directory contains the complete documentation archive of the **Premation** project (originally developed by @isroil01) as it existed at the upstream baseline **Premation 1.2.2**.

This archive implements the **Double Archive Strategy**:

1. **Archive A — Documentation** (this directory): A permanent, human-readable record of the upstream project's history, architecture, features, licensing, and attribution.

2. **Archive B — Codebase** (Git tag `moryhead`): A complete Git snapshot of the verified Premation 1.2.2 codebase at commit `6cf64bac610b237ad0429b41bb2eff1bb936049b`.

## Relationship to Morynex

**Morynex** is a new independent open-source creative software project by **Rynex**, built upon the Premation 1.2.2 foundation.

| Item | Value |
|------|-------|
| New product name | Morynex |
| Maintainer | Rynex |
| Original upstream | Premation |
| Original developer | @isroil01 |
| Upstream repository | https://github.com/isroil01/premation |
| Upstream baseline | Premation 1.2.2 |
| Git archive tag | `moryhead` |
| Documentation archive | `docs/archive/` |
| New application version | `0.1.0` |
| License | AGPL-3.0-only (preserved) |

## Versioning Distinction

- **`moryhead`** — A Git tag identifying the archived upstream codebase (Premation 1.2.2). This tag is permanent and must not be replaced.
- **`0.1.0`** — The first Morynex application version, consolidating the entire upstream development history into a single new baseline.
- **No `v0.1.0` Git tag** is created during this archive task. Morynex begins its own versioning lifecycle from 0.1.0 forward.

## Archive Contents

```
docs/archive/
├── README.md                    # This file
├── PREMATION_HISTORY.md         # Consolidated project history
├── UPSTREAM_VERSIONS.md         # Version evolution (earliest → 1.2.2)
├── UPSTREAM_ARCHITECTURE.md     # Architecture documentation
├── UPSTREAM_FEATURES.md         # Feature inventory by subsystem
├── UPSTREAM_ATTRIBUTION.md      # Original credits and attribution
├── UPSTREAM_LICENSE.md          # AGPL-3.0-only license preservation
└── ARCHIVE_MANIFEST.md          # Technical manifest (commit, tag, date)
```

## Baseline Verification Note

**Important:** The current repository HEAD (commit `6cf64bac`) is tagged as `v1.2.2` but the `package.json` at that commit reports version `1.1.1`. The changelog documents releases up to `1.1.1` (2026-11-05). No changelog entries or release announcements exist for versions `1.2.0`, `1.2.1`, or `1.2.2`.

The `moryhead` tag is placed at the current HEAD (`6cf64bac`) which corresponds to the `v1.2.2` Git tag, representing the latest available codebase state from the upstream `native-core` branch as of 2026-11-13.

This discrepancy is documented in `ARCHIVE_MANIFEST.md` and `UPSTREAM_VERSIONS.md`.

## License

This archive preserves the original **AGPL-3.0-only** license. All upstream copyright notices and attribution requirements remain in effect. See `UPSTREAM_LICENSE.md` and `UPSTREAM_ATTRIBUTION.md`.