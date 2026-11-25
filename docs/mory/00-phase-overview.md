# Phase 0 — Repository Audit & Baseline

**Status:** ✅ Complete  
**Date:** 2026-11-25  
**Branch:** `native-core` (tracking `origin/native-core` → https://github.com/isroil01/premation.git)

## Objective
Complete inventory of all `Premation`/`premation` branding references across the codebase to plan a safe, non-breaking rebrand to **Morynex**.

## Deliverable
- `docs/mory/00-audit.md` — Comprehensive audit findings (see file)

## Key Findings Summary

| Layer | Files | Risk | Notes |
|-------|-------|------|-------|
| Package/Config | 5 | Low | `package.json`, `electron-builder*.yml`, `index.html` |
| Electron Main/Preload | 12+ | Medium | Deep links, 12 `PREMATION_*` env vars, menu, updater |
| C++ Engine | 200+ | High | `namespace premation` everywhere, C ABI symbols |
| SDK/Plugin ABI | 15+ | **Critical** | `PremationPluginInfo` symbol, `premation-plugin.json` — **must preserve** |
| Renderer/UI | 100+ | Medium | i18n (all locales), window globals, routes |
| Build Scripts | 10+ | Medium | Executable names, MCP, CLI tool, temp dirs |
| CI/Docs | 8+ | Low | GitHub repo refs, architecture docs |

## Critical Constraint: Plugin ABI Compatibility
**Existing third-party plugins export `PremationPluginInfo` C symbol.** Cannot break this.

**Strategy:** Dual-naming — keep old symbols as aliases, add new `MorynexPluginInfo` / `MorynexPluginExpressions`, keep `premation-plugin.json` manifest filename.

## Next Phase
→ Phase 1: Package & Config Identity (low risk, no code logic changes)