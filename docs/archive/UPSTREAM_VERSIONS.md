# Upstream Versions — Premation Evolution

## Version Timeline (Verified from Repository Evidence)

This document traces the evolution from the earliest available version through **Premation 1.2.2**, based on git tags, changelog entries, release announcements, and commit history.

**Key:** ✅ = Changelog documented | 🏷️ = Git tag exists | 📢 = Release announcement | ⚠️ = Discrepancy noted

| Version | Date | Tag | Changelog | Announcement | Notes |
|---------|------|-----|-----------|--------------|-------|
| 0.1.0 | — | 🏷️ v0.1.0 | ❌ | ❌ | Earliest tag |
| 0.1.1 | — | 🏷️ v0.1.1 | ❌ | ❌ | |
| 0.1.2 | — | 🏷️ v0.1.2 | ❌ | ❌ | |
| 0.2.1 | — | 🏷️ v0.2.1 | ❌ | ❌ | |
| 0.3.0 | — | 🏷️ v0.3.0 | ❌ | ❌ | |
| 0.3.1 | — | 🏷️ v0.3.1 | ❌ | ❌ | |
| 0.3.2 | — | 🏷️ v0.3.2 | ❌ | ❌ | |
| 0.3.3 | — | 🏷️ v0.3.3 | ❌ | ❌ | |
| 0.3.4 | — | 🏷️ v0.3.4 | ❌ | ❌ | |
| 0.3.5 | — | 🏷️ v0.3.5 | ❌ | ❌ | |
| 0.3.6 | — | 🏷️ v0.3.6 | ❌ | ❌ | |
| 0.3.7 | — | 🏷️ v0.3.7 | ❌ | ❌ | |
| 0.3.8 | — | 🏷️ v0.3.8 | ❌ | ❌ | |
| 0.3.9 | — | 🏷️ v0.3.9 | ❌ | ❌ | |
| 0.3.10 | — | 🏷️ v0.3.10 | ❌ | ❌ | |
| 0.3.11 | — | 🏷️ v0.3.11 | ❌ | ❌ | |
| 0.4.0 | — | 🏷️ v0.4.0 | ❌ | ❌ | |
| 0.4.1 | — | 🏷️ v0.4.1 | ❌ | ❌ | |
| 0.5.0 | — | 🏷️ v0.5.0 | ❌ | ❌ | |
| 0.6.0 | — | 🏷️ v0.6.0 | ❌ | ❌ | |
| 0.7.0 | — | 🏷️ v0.7.0 | ❌ | ❌ | |
| 0.8.0 | — | 🏷️ v0.8.0 | ❌ | ❌ | |
| 0.8.1 | — | 🏷️ v0.8.1 | ❌ | ❌ | |
| 0.8.2 | — | 🏷️ v0.8.2 | ❌ | ❌ | |
| 0.8.3 | 2026-09-14 | 🏷️ v0.8.3 | ✅ | ❌ | RTL/bidirectional, vertical writing, optical kerning |
| 0.8.4 | — | 🏷️ v0.8.4 | ❌ | ❌ | Tag exists, no changelog |
| 0.8.5 | 2026-09-20 | 🏷️ v0.8.5 | ✅ | ❌ | AE import, plugin publishing, timeline bulk edit |
| 0.9.0 | 2026-10-06 | 🏷️ v0.9.0 | ✅ | ❌ | **Native C++ engine**, Premation Cloud, Linux AppImage |
| 1.0.0 | 2026-10-13 | 🏷️ v1.0.0 | ✅ | ❌ | **Plugin SDK 1.2**, AI Assistants, 60 more effects |
| 1.0.1 | 2026-10-13 | 🏷️ v1.0.1 | ✅ | ❌ | ffmpeg in installers, publisher listings, macOS engine |
| 1.0.2 | 2026-10-19 | 🏷️ v1.0.2 | ✅ | ❌ | Linux, panels, text, camera, cache, export, assistants |
| 1.0.3 | — | 🏷️ v1.0.3 | ❌ | ❌ | Tag exists, no changelog |
| 1.0.4 | — | 🏷️ v1.0.4 | ❌ | ❌ | Tag exists, no changelog |
| 1.1.0 | 2026-11-03 | 🏷️ v1.1.0 | ✅ | 📢 email-v1.1.0.md, discord-v1.1.0.md | Multi-core playback, scrubbing, gamma, checkout, Report Problem, Cloud trials, icons, timeline UX |
| 1.1.1 | 2026-11-05 | 🏷️ v1.1.1 | ✅ | ❌ | Composition Settings, pure white, nested comps, Enter, languages, AE import, **SDK 1.4**, character rigging |
| 1.1.2 | — | 🏷️ v1.1.2 | ❌ | ❌ | Tag missing from list (gap) |
| 1.1.3 | — | 🏷️ v1.1.3 | ❌ | ❌ | Tag exists, no changelog |
| 1.2.0 | — | 🏷️ v1.2.0 | ❌ | ❌ | **Tag exists, no changelog, no announcement** |
| 1.2.1 | — | 🏷️ v1.2.1 | ❌ | ❌ | **Tag exists, no changelog, no announcement** |
| **1.2.2** | **2026-11-13** | **🏷️ v1.2.2** | **❌** | **❌** | **Tag at HEAD (6cf64bac). package.json=1.1.1. Only commit: "Remove temporary Linux engine diagnostic"** |

---

## Discrepancy Analysis: v1.2.2

### Evidence

| Source | Version Reported |
|--------|------------------|
| Git tag | `v1.2.2` |
| `package.json` at tag | `1.1.1` |
| `package-lock.json` at tag | `1.1.1` |
| `CHANGELOG.md` | Ends at `1.1.1` (2026-11-05) |
| `docs/announcements/` | Only `email-v1.1.0.md`, `discord-v1.1.0.md` |
| Commit at tag (6cf64bac) | "Remove the temporary Linux engine diagnostic" (2026-11-13) |
| Commits between v1.1.1 and v1.2.2 | 6 commits, all "Temporary: diagnose Linux engine toolchain" + 1 release commit |

### Interpretation

The `v1.2.2` tag appears to mark the **latest codebase state on the `native-core` branch** as of 2026-11-13, incorporating:
- Post-1.1.1 Linux engine diagnostic commits (6 commits from 2026-11-10 to 2026-11-13)
- A release commit "Release: engines no longer cancel each other; remove the toolchain diagnostic" (15f62882)
- Final cleanup "Remove the temporary Linux engine diagnostic" (6cf64bac)

However, **no formal version bump** was made in `package.json` (remains 1.1.1), **no changelog entry** was written, and **no release announcement** was created.

### Conclusion for Archive

The `moryhead` archive tag is placed at commit `6cf64bac` (the `v1.2.2` tag commit) because it represents the **latest verified upstream codebase state** on the `native-core` branch. The version discrepancy is documented here and in `ARCHIVE_MANIFEST.md`.

Morynex 0.1.0 consolidates **all development up to this commit**, regardless of the version number discrepancy.

---

## Version Categories

### Fully Documented Releases (Changelog + Tag)
- 0.8.3, 0.8.5, 0.9.0, 1.0.0, 1.0.1, 1.0.2, 1.1.0, 1.1.1

### Tagged but Undocumented (Tag Only)
- 0.1.0–0.8.2, 0.8.4, 1.0.3, 1.0.4, 1.1.3

### Tagged but No Changelog/Announcement (1.2.x Series)
- 1.2.0, 1.2.1, 1.2.2

### Missing Tag (Gap)
- 1.1.2 (tag not in list, gap between 1.1.1 and 1.1.3)

---

## Major Architectural Transitions

| Version Range | Transition |
|---------------|------------|
| 0.1.0–0.8.x | TypeScript engine era (pre-native) |
| 0.9.0 | **Native C++ engine process introduced** (phase A complete) |
| 1.0.0 | **Plugin SDK 1.2**, AI Assistants, 60+ effects |
| 1.1.0 | Multi-core playback, scrubbing UX, gamma blending, browser checkout |
| 1.1.1 | **Plugin SDK 1.4** (layer types, simulations, ML, OpenFX/CLAP, audio, hot reload) |
| 1.2.x | Linux engine diagnostic/fixes (internal, no user-facing features) |

---

## Edition Divergence (Since 0.9.0)

Since version 0.9.0, two editions exist:

| Edition | Build Flag | Characteristics |
|---------|------------|-----------------|
| **Local** | `VITE_EDITION=local` | No accounts, disk-based `.motion` bundles, BYOK AI |
| **Server** | (default) | Accounts required, cloud projects, hosted AI gateway |

All versions from 0.9.0 onward support both editions. The `:local` scripts (`npm run electron:dev:local`, etc.) are used for development.

---

## Notes for Morynex

- Morynex 0.1.0 inherits the **complete codebase at commit 6cf64bac (v1.2.2 tag)**.
- The version discrepancy (tag says 1.2.2, package.json says 1.1.1) is preserved in the archive.
- Morynex begins its own versioning at **0.1.0**, consolidating all upstream history.
- No separate Morynex releases are created for historical Premation versions.