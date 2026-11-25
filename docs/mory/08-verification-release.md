# Phase 8 — Verification & Release Preparation

**Status:** ⏳ Planned  
**Risk:** Low (verification)  
**Scope:** Full test suite, lint, typecheck, native tests, render tests, benchmarks, packaging

## Verification Checklist

### TypeScript / Renderer
```bash
# Type checking
npm run typecheck                    # Zero errors (root + electron)

# Linting
npm run lint                         # Within warning budget (129)

# Unit tests
npm test                             # ~440 suites, <1 min

# E2E tests (requires built app)
npm run electron:compile
npm run test:e2e                     # Playwright in real Electron
```

### C++ Engine / Native
```bash
# Configure (all presets)
npm run native:configure             # linux-clang, macos-clang, windows-clang-cl, wasm, *-asan, *-tsan

# Build
npm run native:build                 # All targets

# Tests
npm run native:test                  # CTest all green

# Sanitizers (blocking in CI)
npm run native:configure -- --preset linux-clang-asan
npm run native:build
npm run native:test

npm run native:configure -- --preset linux-clang-tsan
npm run native:build
npm run native:test

# Clang-tidy
npm run native:tidy                  # Clean

# WASM build
npm run native:wasm                  # Emscripten build + smoke test
```

### Render Tests (Golden Images)
```bash
# Compare GPU output to committed frames
npm run render-tests                 # Parity gate

# If shaders/passes changed intentionally:
npm run render-tests:update          # Re-bless goldens (AFTER reviewing diff)
```

### Benchmarks
```bash
# Run benchmarks
npm run bench                        # *.bench.test.ts on real engine

# Ratchet check
npm run bench:check                  # Compare to baseline (bench/baseline.json)
```

### Packaging (Local Edition)
```bash
# Fast unpacked test
npm run pack:local                   # release/<version>/ (unpacked dir)

# Full installer build
npm run dist:local                   # Signed installers (requires CI secrets)

# Verify executable names in output
ls release/<version>/win-unpacked/   # Morynex.exe, morynex-engine.exe
ls release/<version>/macOS/          # Morynex.app, morynex-engine
ls release/<version>/linux/          # Morynex-Linux-*.AppImage, morynex-engine
```

### Plugin Compatibility Test
```bash
# Build sample plugins against new SDK
cd native/sdk/samples/ripple
cmake -B build -DCMAKE_PREFIX_PATH=/path/to/mory-sdk
cmake --build build

# Verify plugin loads in engine
# Test existing com.premation.* plugins still work
```

### Deep Link / OAuth Test
```bash
# Test morynex://connections handling
# Test morynex://billing handling
# Test morynex://oauth?code=... handling
# Verify backward compat: premation:// still works (transition period)
```

### Edition Tests
```bash
# Local edition (offline, disk-based)
npm run electron:dev:local           # Full desktop app
npm run dev:local                    # Renderer only in browser

# Server edition (requires backend)
npm run electron:dev                 # Full desktop app
npm run dev                          # Renderer only
```

### Environment Variable Compat Test
```bash
# Test both old and new prefixes work
PREMATION_ENGINE_PATH=/path/to/engine npm run electron:dev:local
MORYNEX_ENGINE_PATH=/path/to/engine npm run electron:dev:local

# Test PREMATION_UI_PLATFORM and MORYNEX_UI_PLATFORM
```

### Window Globals / DevTools Test
```bash
# In devtools console:
console.log(window.__morynexEngineTransport)
console.log(window.__morynexEngineSurface)
console.log(window.__morynexProcessEngineState)
console.log(window.__morynexViewportHud)
console.log(window.__morynexAutomation)

# Verify NO __premation* globals remain
```

## Release Checklist

### Pre-Release
- [ ] All verification steps pass
- [ ] `docs/MIGRATION_GUIDE.md` published
- [ ] GitHub org `rynex` created, repo `morynex` created
- [ ] CI secrets configured for `rynex/morynex` (code signing, publishing)
- [ ] Release workflow tested on `release-candidate` branch
- [ ] Plugin store updated for new SDK (`mory-sdk-*` artifacts)

### Version Bump
```bash
# Choose one:
npm run release:patch   # 1.1.1 → 1.1.2
npm run release:minor   # 1.1.1 → 1.2.0
npm run release:major   # 1.1.1 → 2.0.0
```

### Publish
```bash
# Draft release (CI builds, stages artifacts)
git push origin main --tags

# In GitHub UI: review draft release, verify assets:
# - Morynex-Setup-Windows.exe / .msi
# - Morynex-macOS-arm64.dmg / .zip
# - Morynex-Linux-x64.AppImage
# - morynex-engine (all platforms)
# - mory-sdk-<platform>.zip
# - morynex-mcp.cjs

# Publish release
```

### Post-Release
- [ ] Verify auto-update works for existing users (transition period)
- [ ] Announce release (blog, social, discord)
- [ ] Update documentation site (if separate)
- [ ] Monitor issue tracker for migration issues

## Rollback Plan
If critical issues found post-release:
1. **Do not delete release** — mark as pre-release
2. Patch version: `npm run release:patch`
3. Fix, rebuild, publish new patch
4. Users on broken version will auto-update to patch

## Success Criteria
| Metric | Target |
|--------|--------|
| TypeScript errors | 0 |
| Lint warnings | ≤ 129 (current budget) |
| Jest suites | ~440 passing |
| E2E tests | All passing |
| Native CTest | 100% passing |
| Sanitizers (ASan/UBSan/TSan) | Clean |
| Clang-tidy | Clean |
| Render tests | Golden parity |
| Benchmarks | ≥ baseline (ratchet) |
| Package build | All 3 platforms |
| Plugin compat | Existing + new plugins load |
| Deep links | morynex:// + premation:// (compat) |

## Timeline Estimate
| Phase | Duration |
|-------|----------|
| Phase 1: Package/Config | 0.5 day |
| Phase 2: Electron Main/Preload | 1 day |
| Phase 3: C++ Namespace | 2-3 days |
| Phase 4: SDK/Plugin System | 1-2 days |
| Phase 5: UI Strings | 1 day (scripted) |
| Phase 6: Build Scripts/CI | 0.5 day |
| Phase 7: Documentation | 0.5 day |
| Phase 8: Verification | 1-2 days |
| **Total** | **~7-10 days** |

## Notes
- Phases 3 & 4 are tightly coupled — do together
- Phase 5 (i18n) can be parallelized with script
- Run verification after EACH phase, not just at end
- Keep `PREMATION_*` env var fallback for 2 major versions
- Keep `premation://` deep link handling for 2 major versions
- Coordinate with backend (motion-back) for OAuth scheme change