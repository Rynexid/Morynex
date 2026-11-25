# Archive Manifest — Premation 1.2.2 Baseline

## Technical Baseline

| Field | Value |
|-------|-------|
| **Archive Tag** | `moryhead` |
| **Archive Commit** | `6cf64bac610b237ad0429b41bb2eff1bb936049b` |
| **Upstream Tag** | `v1.2.2` |
| **Upstream Branch** | `native-core` |
| **Archive Date** | 2026-11-25 |
| **Package.json Version at Commit** | `1.1.1` |
| **Changelog Last Entry** | `1.1.1` (2026-11-05) |
| **Repository** | https://github.com/isroil01/premation |

## Version Discrepancy Notice

**Critical:** The Git tag `v1.2.2` exists at commit `6cf64bac`, but:
- `package.json` reports version `1.1.1`
- `CHANGELOG.md` ends at `1.1.1` (2026-11-05)
- No release announcements exist for `1.2.0`, `1.2.1`, or `1.2.2`
- The commit at `v1.2.2` is "Remove the temporary Linux engine diagnostic" (2026-11-13)

The `moryhead` tag is placed at `6cf64bac` because it represents the **latest verified upstream codebase state** on the `native-core` branch. This discrepancy is fully documented in `UPSTREAM_VERSIONS.md` and `PREMATION_HISTORY.md`.

## Morynex Baseline

| Field | Value |
|-------|-------|
| **New Product** | Morynex |
| **Maintainer** | Rynex |
| **First Application Version** | `0.1.0` |
| **License** | AGPL-3.0-only (preserved) |
| **Upstream Attribution** | @isroil01 (preserved per AGPL) |
| **Git Archive Tag** | `moryhead` (permanent, must not be replaced) |
| **Documentation Archive** | `docs/archive/` |

## Archive Contents

```
docs/archive/
├── README.md                    # Archive overview (this archive)
├── PREMATION_HISTORY.md         # Consolidated project history (185 lines)
├── UPSTREAM_VERSIONS.md         # Version evolution with discrepancy analysis (132 lines)
├── UPSTREAM_ARCHITECTURE.md     # Architecture documentation
├── UPSTREAM_FEATURES.md         # Feature inventory by subsystem
├── UPSTREAM_ATTRIBUTION.md      # Original credits and attribution
├── UPSTREAM_LICENSE.md          # AGPL-3.0-only license preservation
└── ARCHIVE_MANIFEST.md          # This file
```

## File Integrity (SHA-256)

| File | SHA-256 | Size |
|------|---------|------|
| README.md | `6790b65aac25abd6ced11bad88e257e3dd38eab3db262c54e87be9c941fde3ce` | 3,099 B |
| PREMATION_HISTORY.md | `f5985e17e23cbc809f96d0a9ef2e4b216726fe44b3b9a509691a20dfd5ae5f6b` | 15,116 B |
| UPSTREAM_VERSIONS.md | `28201eebcc4d7940632c72c519414f28965f2c1d48a3671d06d5152338081ab2` | 6,683 B |
| UPSTREAM_ARCHITECTURE.md | `fda722979144468caadefdb9bc24d40b640b30ec6b60329c33c4a34957885df2` | 18,602 B |
| UPSTREAM_FEATURES.md | `8992924f8df39cb27c3be62aa59bf6d41efb52f5f22fab62481d6e2ff46991ee` | 19,289 B |
| UPSTREAM_ATTRIBUTION.md | `b65f8452b74aab216ce9413c6eb8353165d1e3ee4ccdf402ed4d5cd9dfd9407b` | 6,897 B |
| UPSTREAM_LICENSE.md | `9aef711c5e20bf44640578c268bd00590404efebbb321945797bbabe7c4cf44f` | 20,680 B |

*Note: Run `sha256sum docs/archive/*.md` to verify. ARCHIVE_MANIFEST.md is excluded from this list as its hash would be self-referential.*

## Source Evidence

This archive was compiled from:

- `CHANGELOG.md` (up to 1.1.1)
- Git tags: `v0.1.0` through `v1.2.2`
- `docs/NATIVE_CORE_PLAN.md` (architecture decisions, 2026-09-22)
- `docs/TS_ENGINE_REMOVAL.md` (one-engine decision, 2026-09-28)
- `docs/ENGINE_API.md` (command/query/event/frame contract)
- `docs/B3_PATTERNS.md` (engine command patterns)
- `docs/B4_MIRROR.md` (document mirror architecture)
- `src/core/config/edition.ts` (edition flags)
- `package.json` (version history, scripts, dependencies)
- `docs/announcements/` (`email-v1.1.0.md`, `discord-v1.1.0.md`)
- Git commit history (`git log --oneline --all`)

## Verification Commands

```bash
# Verify archive tag exists at correct commit
git tag moryhead 6cf64bac610b237ad0429b41bb2eff1bb936049b
git show moryhead --stat

# Verify package.json version at archive commit
git show moryhead:package.json | grep version

# Verify documentation archive exists
ls -la docs/archive/

# Run full test suite at archive baseline
git checkout moryhead
npm ci --ignore-scripts
npm run typecheck && npm test && npm run lint
```

## Relationship to Morynex

**Morynex** begins its own versioning lifecycle at **0.1.0**, consolidating the complete upstream development history up to commit `6cf64bac` (the `v1.2.2` tag). The `moryhead` tag is a permanent reference to the upstream baseline and must never be moved or replaced.

No Git tag `v0.1.0` is created for Morynex during this archive task. Morynex versioning starts fresh from 0.1.0 forward.

## License Preservation

This archive preserves the original **AGPL-3.0-only** license in full. All upstream copyright notices and attribution requirements remain in effect. See `UPSTREAM_LICENSE.md` and `UPSTREAM_ATTRIBUTION.md` for complete license text and attribution details.

---

**Generated:** 2026-11-25  
**Archive Status:** Complete — all 7 manifest files present