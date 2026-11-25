# Morynex Rebrand — Master Phase Plan

**Project:** Premation → Morynex  
**Branch:** `native-core` → `morynex-rebrand` (to be created)  
**Target:** Independent open-source creative software by Rynex  
**License:** AGPL-3.0-only (preserved)  
**Upstream Attribution:** @isroil01 (preserved per AGPL)

---

## Phase Overview

| Phase | Title | Risk | Duration | Status |
|-------|-------|------|----------|--------|
| 0 | Repository Audit & Baseline | — | 0.5 day | ✅ Done |
| 1 | Package & Config Identity | Low | 0.5 day | ⏳ Planned |
| 2 | Electron Main & Preload | Medium | 1 day | ⏳ Planned |
| 3 | C++ Engine Namespace | **High** | 2-3 days | ⏳ Planned |
| 4 | SDK & Plugin System | **High** | 1-2 days | ⏳ Planned |
| 5 | Renderer/UI Strings | Medium | 1 day | ⏳ Planned |
| 6 | Build Scripts & CI/CD | Low-Med | 0.5 day | ⏳ Planned |
| 7 | Documentation | Low | 0.5 day | ⏳ Planned |
| 8 | Verification & Release | Low | 1-2 days | ⏳ Planned |

**Total Estimate: 7-10 days**

---

## Critical Constraints (Non-Negotiable)

### 1. Plugin ABI Compatibility
**Existing third-party plugins export `PremationPluginInfo` C symbol.** This MUST continue to work.

**Solution:** Dual-naming strategy
- Keep `PremationPluginInfo` / `PremationPluginExpressions` exported
- Add `MorynexPluginInfo` / `MorynexPluginExpressions` aliases
- Keep `premation-plugin.json` manifest filename
- Keep `PR_PLUGIN_INFO_SYMBOL` = `"PremationPluginInfo"`
- New plugins use `com.mory.*` namespace

### 2. Deep Link / OAuth Backward Compat
- Support `premation://` scheme for 2 major versions
- Support `PREMATION_*` env vars for 2 major versions
- Coordinate with motion-back (not in this repo) for OAuth redirect

### 3. No Breaking Changes to User Data
- `.motion` project format unchanged
- Config directory migration: `Premation` → `Morynex` (documented)
- Recovery keys: `premation.engineRecovery.v1` → `morynex.engineRecovery.v1` (new installs only)

---

## Identity Mapping

| Asset | Current (Premation) | Target (Morynex) |
|-------|---------------------|------------------|
| **Product Name** | Premation | Morynex |
| **Cloud Tier** | Premation Cloud | Morynex Cloud (or Morynex Pro) |
| **Executable** | premation-engine | morynex-engine |
| **App Bundle** | Premation.app / Premation.exe | Morynex.app / Morynex.exe |
| **AppImage** | Premation-Linux-*.AppImage | Morynex-Linux-*.AppImage |
| **App ID** | com.premation.app | com.morynex.app |
| **Deep Link** | premation:// | morynex:// |
| **Protocol** | premation://oauth | morynex://oauth |
| **Uninstall Flag** | --premation-mcp-uninstall | --morynex-mcp-uninstall |
| **Namespace (C++)** | premation:: | mory:: |
| **SDK CMake** | premation_sdk / PremationSdk | mory_sdk / MorySdk |
| **SDK Include** | premation_sdk/ | mory_sdk/ |
| **CLI Tool** | premation-plugins | morynex-plugins |
| **Plugin IDs** | com.premation.* | com.mory.* (new) |
| **Manifest** | premation-plugin.json | **KEEP** (ABI) |
| **Plugin Symbol** | PremationPluginInfo | **KEEP** + MorynexPluginInfo alias |
| **Env Prefix** | PREMATION_ | MORYNEX_ (with fallback) |
| **Window Globals** | __premationEngine* | __morynexEngine* |
| **Recovery Key** | premation.engineRecovery.v1 | morynex.engineRecovery.v1 |
| **GitHub Repo** | isroil01/premation | rynex/morynex |
| **SDK Artifact** | premation-sdk-<platform>.zip | mory-sdk-<platform>.zip |
| **MCP Server** | premation-mcp.cjs | morynex-mcp.cjs |
| **Config Dir** | ~/Premation / %APPDATA%\Premation | ~/Morynex / %APPDATA%\Morynex |

---

## Implementation Order Dependencies

```
Phase 0 (Done)
    │
    ├──→ Phase 1 (Package/Config) ──────────────────┐
    │                                                │
    ├──→ Phase 2 (Electron Main/Preload) ───────────┤
    │                                                │
    ├──→ Phase 3 (C++ Namespace) ←──── Phase 4 ─────┤  (Do together)
    │        (SDK/Plugin System)                     │
    │                                                │
    ├──→ Phase 5 (UI Strings) ──────────────────────┤
    │                                                │
    ├──→ Phase 6 (Build Scripts/CI) ────────────────┤
    │                                                │
    └──→ Phase 7 (Documentation) ───────────────────┘
                            │
                            ▼
                     Phase 8 (Verification)
```

**Phases 3 & 4 must be done together** — C++ namespace and SDK install paths are interdependent.

**Phase 2 (deep link scheme) must precede Phase 5 (UI deep link handlers).**

---

## Verification Gates (Per Phase)

| Phase | Commands to Pass |
|-------|------------------|
| 1 | `npm run typecheck`, `npm run lint` |
| 2 | `npm run typecheck`, `npm run test -- electron/main.test.ts` |
| 3 | `npm run native:configure`, `npm run native:build`, `npm run native:test`, `npm run native:tidy` |
| 4 | SDK install test, sample plugin build, existing plugin load |
| 5 | `npm run typecheck`, visual check `npm run dev:local` |
| 6 | `node scripts/stageEngine.cjs`, `npm run pack:local` |
| 7 | Link check, no broken refs |
| 8 | **ALL**: typecheck, test, lint, native:test, render-tests, bench:check, pack:local, dist:local |

---

## Open Decisions Needed

1. **Cloud tier name:** "Morynex Cloud" or "Morynex Pro"?
2. **Plugin namespace:** `com.mory.*` or `com.rynex.*`? (Recommend `com.mory.*`)
3. **GitHub org:** Confirm `rynex` organization exists/created
4. **Config directory:** `~/Morynex` (Linux/macOS), `%APPDATA%\Morynex` (Windows) — standard per platform
5. **Backward compat period:** 2 major versions (recommended) — confirm

---

## Risk Register

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| C++ namespace breaks engine | Medium | Critical | Phase 3+4 atomic, full native test suite |
| Plugin ABI break | Low | Critical | Dual symbols, extensive compat testing |
| Deep link break (OAuth) | High | High | Support both schemes, coordinate with backend |
| CI/CD repo reference fail | Low | Medium | Update all workflows atomically |
| Render test drift | Low | Medium | Run after C++ changes |
| macOS notarization fail | Low | High | Update `mac.binaries` names, re-sign |
| Windows installer art | Low | Medium | Re-render `build/installer*.bmp` |
| User data migration | Medium | Medium | Document config dir change, don't auto-migrate |

---

## File Index (docs/mory/)

| File | Phase | Description |
|------|-------|-------------|
| `00-audit.md` | 0 | Complete repository audit findings |
| `00-phase-overview.md` | 0 | This file — master plan |
| `01-package-config.md` | 1 | Package.json, electron-builder, index.html |
| `02-electron-main-preload.md` | 2 | Deep links, env vars, menu, updater |
| `03-cpp-engine-namespace.md` | 3 | C++ namespace rename (premation→mory) |
| `04-sdk-plugin-system.md` | 4 | SDK headers, CMake, CLI, plugin compat |
| `05-renderer-ui-strings.md` | 5 | i18n, page titles, window globals |
| `06-build-scripts-ci.md` | 6 | Build scripts, realapp tests, CI workflows |
| `07-documentation.md` | 7 | Architecture docs, agent rules, migration guide |
| `08-verification-release.md` | 8 | Full test matrix, release checklist |

---

## Next Steps

1. **Create rebrand branch:** `git checkout -b morynex-rebrand`
2. **Start Phase 1:** Modify `package.json`, `electron-builder.yml`, `index.html`
3. **Run verification:** `npm run typecheck && npm run lint`
4. **Commit Phase 1** atomically
5. **Proceed to Phase 2** (Electron main/preload)

---

**Prepared:** 2026-11-25  
**Status:** Ready for Phase 1 implementation