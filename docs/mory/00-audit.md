# Morynex Rebrand Audit — Phase 0 Findings

**Date:** 2026-11-25  
**Branch:** `native-core` (tracking `origin/native-core` → https://github.com/isroil01/premation.git)  
**Scope:** Complete repository audit for `Premation` → `Morynex` rebrand

---

## Executive Summary

The Premation codebase contains **extensive branding references** across all layers. The rebrand touches:

| Layer | Files Affected | Complexity |
|-------|----------------|------------|
| **Package/Config** | `package.json`, `electron-builder*.yml`, `vite.config.ts`, `tsconfig.json` | Low (strings, metadata) |
| **Electron Main/Preload** | `electron/main.ts`, `electron/preload.ts`, `electron/engine*.ts`, `electron/updater.ts` | Medium (deep links, IPC, process names) |
| **C++ Engine** | `native/**/*.{cpp,hpp,h}`, CMake targets, SDK headers | High (namespaces, symbols, ABI) |
| **Renderer/UI** | `src/**/*.{ts,tsx}`, i18n JSON, `index.html` | Medium (strings, constants, routes) |
| **Build Scripts** | `scripts/**/*.{cjs,mjs}` | Medium (paths, env vars, artifact names) |
| **Documentation** | `docs/**/*.md`, `CLAUDE.md`, `AGENTS.md` | Low (prose) |
| **CI/CD** | `.github/workflows/*.yml` | Low (repo references) |

**Critical constraint:** The C++ ABI (`PremationPluginInfo` symbol, `premation_sdk` CMake package, `premation-plugin.json` manifest) must remain compatible for **existing third-party plugins**. This requires **alias/dual-naming strategy**, not a breaking rename.

---

## 1. Package & Config Identity

### `package.json`
| Field | Current | Target |
|-------|---------|--------|
| `name` | `motion-editor` | `@mory/morynex` (or `morynex`) |
| `repository.url` | `git+https://github.com/isroil01/motion-editor.git` | `git+https://github.com/rynex/morynex.git` |
| `homepage` | `https://github.com/isroil01/motion-editor#readme` | `https://github.com/rynex/morynex#readme` |
| `bugs.url` | `https://github.com/isroil01/motion-editor/issues` | `https://github.com/rynex/morynex/issues` |
| `author` | `Premation contributors` | `Rynex contributors` |
| `copyright` (in electron-builder) | `© Premation contributors` | `© Rynex contributors` |

### `electron-builder.yml` / `.selfhosted.yml`
| Field | Current | Target |
|-------|---------|--------|
| `appId` | `com.premation.app` | `com.morynex.app` |
| `productName` | `Premation` | `Morynex` |
| `copyright` | `© Premation contributors — GNU AGPL v3.0` | `© Rynex contributors — GNU AGPL v3.0` |
| `protocols.schemes` | `premation` | `morynex` |
| `fileAssociations.ext` | `pplugin` | Keep `.pplugin` (user data) |
| `fileAssociations.name` | `Premation Plugin` | `Morynex Plugin` |
| `fileAssociations.mimeType` | `application/x-premation-plugin` | `application/x-morynex-plugin` |
| `win.artifactName` | `Premation-Setup-Windows.${ext}` | `Morynex-Setup-Windows.${ext}` |
| `mac.artifactName` | `Premation-macOS-${arch}.${ext}` | `Morynex-macOS-${arch}.${ext}` |
| `linux.artifactName` | `Premation-Linux-${arch}.${ext}` | `Morynex-Linux-${arch}.${ext}` |
| `mac.binaries` | `premation-engine`, `premation-host-bridge.node` | `morynex-engine`, `morynex-host-bridge.node` |
| `publish.owner/repo` | `isroil01/premation` | `rynex/morynex` |

### `electron-builder.selfhosted.yml`
- Same as base, plus `extraResources` filters for `premation-engine*`, `premation-mcp.cjs`

---

## 2. Electron Main Process (`electron/main.ts`)

### Deep Link Scheme
```typescript
const DEEP_LINK_SCHEME = 'premation'  // → 'morynex'
const OAUTH_SCHEME = DEEP_LINK_SCHEME
app.setAsDefaultProtocolClient(OAUTH_SCHEME, ...)
argv.find(a => a.startsWith(`${OAUTH_SCHEME}://`))
```

### Uninstall Flag
```typescript
if (process.argv.includes('--premation-mcp-uninstall'))  // → '--morynex-mcp-uninstall'
```

### Environment Variables Referenced
| Variable | Used In | Purpose |
|----------|---------|---------|
| `PREMATION_DEV_URL` | `main.ts:93` | Dev server override |
| `PREMATION_UI_PLATFORM` | `main.ts:864,873` | Chrome preview |
| `PREMATION_PLUGIN_PATH` | `main.ts:1346` | Additional plugin dirs |
| `PREMATION_LIBRARY_DEV_DIR` | `main.ts:1544` | Dev library path |

### Window Title / Menu
```typescript
// nativeMenu.ts → label: 'Premation' (line 91 in nativeMenu.test.ts)
```

---

## 3. Engine Host & Supervisor (`electron/engineHost.ts`, `engineSupervisor.ts`)

### Executable Names
| Current | Target | Notes |
|---------|--------|-------|
| `premation-engine` | `morynex-engine` | Main engine binary |
| `premation-host-bridge.node` | `morynex-host-bridge.node` | macOS IOSurface bridge |
| `premation-plugin-host` | `morynex-plugin-host` | Out-of-process plugin host (SDK 1.4) |
| `premation-plugins` | `morynex-plugins` | SDK CLI tool (list/render/test) |

### Engine Environment Variables (passed to engine process)
| Variable | Purpose |
|----------|---------|
| `PREMATION_VIEWPORT_ROUTE` | Route A/C selection |
| `PREMATION_FONTS_MANIFEST` | Fonts JSON path |
| `PREMATION_ENGINE_ARGS` | Extra engine flags |
| `PREMATION_PLUGIN_PATH` | Plugin search paths |
| `PREMATION_FFMPEG` | FFmpeg binary path |
| `PREMATION_SESSION_FOOTAGE` | Session footage dir |
| `PREMATION_SAM_USER_DIR` | SAM model user dir |
| `PREMATION_SAM_GPU_DIR` | SAM GPU model dir |
| `PREMATION_FACE_USER_DIR` | Face landmarks dir |
| `PREMATION_DEPTH_USER_DIR` | Depth model dir |
| `PREMATION_HAND_USER_DIR` | Hand landmarks dir |
| `PREMATION_SAM2_USER_DIR` | SAM2 model dir |
| `PREMATION_VITMATTE_USER_DIR` | ViTMatte model dir |

### Engine Supervisor Resolution Order
```typescript
// engineSupervisor.ts:572-587
1. PREMATION_ENGINE_PATH (env override)
2. <resources>/engine/premation-engine*
3. <app>/../native/build/<preset>/engine/premation-engine*
4. <app>/../native/build/<preset>/engine/premation-engine.exe
```

---

## 4. Preload Bridge (`electron/preload.ts`)

### IPC Channels
- `engine:pixels`, `engine:pixelsRelease` — pixel pipe (route A)
- `deepLink:connections`, `deepLink:billing` — deep link handlers
- `premation://` scheme handling

### Exposed API (`window.motionEditor`)
- No direct `premation` references in the typed surface, but deep link handlers use the scheme.

---

## 5. C++ Engine (`native/`)

### Namespace & Symbols
| Current | Target | Risk |
|---------|--------|------|
| `namespace premation` | `namespace mory` | **HIGH** — all engine code |
| `namespace premation::api` | `namespace mory::api` | Generated protocol code |
| `namespace premation::wire` | `namespace mory::wire` | Protocol wire format |
| `namespace premation::frames` | `namespace mory::frames` | Frame channel |
| `namespace premation::pixels` | `namespace mory::pixels` | Pixel channel |
| `PremationPluginInfo` (C symbol) | `MorynexPluginInfo` + alias | **CRITICAL** — plugin ABI |
| `PremationPluginExpressions` | `MorynexPluginExpressions` + alias | Plugin ABI |
| `PR_PLUGIN_INFO_SYMBOL` = `"PremationPluginInfo"` | `"MorynexPluginInfo"` + keep old | Plugin ABI |

### CMake Targets
| Current | Target |
|---------|--------|
| `premation_sdk` | `mory_sdk` (alias `mory::sdk`) |
| `PremationSdk` (config) | `MorySdk` |
| `premation_add_plugin()` | `mory_add_plugin()` |
| `premation_embed_wgsl()` | `mory_embed_wgsl()` |
| `premation_sample_plugin()` | `mory_sample_plugin()` |

### Install Paths (SDK release)
| Current | Target |
|---------|--------|
| `include/premation_sdk/` | `include/mory_sdk/` |
| `lib/cmake/PremationSdk/` | `lib/cmake/MorySdk/` |
| `bin/premation-plugins` | `bin/morynex-plugins` |
| `share/premation-sdk/` | `share/mory-sdk/` |

### Protocol Generated Code
- `native/protocol/include/premation/protocol/*.hpp` → `mory/protocol/`
- `native/protocol/generated/engine_api_*.cpp` namespaces
- Codegen from `packages/engine-api/codegen/` (schema → TS + C++)

### Sample Plugin IDs (must preserve for compat)
```cpp
// All samples use com.premation.samples.*
"com.premation.samples.ofxinvert"
"com.premation.samples.meshgen"
"com.premation.samples.rings"
"com.premation.samples.statspanel"
"com.premation.samples.bounce"
"com.premation.samples.ppmseq"
"com.premation.samples.mlstyle"
"com.premation.samples.particles"
"com.premation.samples.ripple"
"com.premation.samples.scatter"
"com.premation.samples.checkout"
"com.premation.samples.clapgain"
"com.premation.samples.silkwaves"
"com.premation.sample.fxkit"
```
**Strategy:** Keep `com.premation.*` IDs for existing plugins; new plugins use `com.mory.*` or `com.rynex.*`

### SDK Headers (`native/sdk/include/premation_sdk/`)
| File | Key Exports |
|------|-------------|
| `pr_types.h` | `PR_SDK_VERSION`, `PR_EXPORT`, `PR_CALL` |
| `pr_effect.h` | `PremationPluginInfo`, `PremationPluginExpressions` symbols |
| `pr_command.h` | `premation-engine` references in comments |
| `pr_io.h` | `premation-engine` references in comments |
| `pr_host.h` | `premation-plugins` in comments |
| `pr_scene.h` | `premation_material` WGSL function name |
| `premation_sdk.h` | Umbrella header |

---

## 6. Renderer / UI (`src/`)

### Environment Variables (Vite `import.meta.env`)
| Variable | Used In | Purpose |
|----------|---------|---------|
| `VITE_EDITION` | `main.tsx:34`, `package.json` scripts | Edition selector |
| `PREMATION_UI_PLATFORM` | `main.tsx:26`, `uiPlatform.ts:73` | Chrome preview |
| `VITE_MCP_DEV_PLAN` | `main.tsx:43` | Dev MCP plan |
| `VITE_LOCAL_FIRST` | `main.tsx:63` | Local-first flag |
| `VITE_BACKEND_ORIGIN` | CSP, API base | Server backend |
| `VITE_MEDIA_ORIGINS` | CSP | Media origins |

### Constants & Keys
| Location | Current | Target |
|----------|---------|--------|
| `engineOwnedSession.tsx:49` | `premation.engineRecovery.v1` | `morynex.engineRecovery.v1` |
| `engineOwnedSession.tsx:211` | `__premationEngineTransport` | `__morynexEngineTransport` |
| `engineOwnedSession.tsx:211` | `__premationEngineSurface` | `__morynexEngineSurface` |
| `engineOwnedSession.tsx:211` | `__premationProcessEngineState` | `__morynexProcessEngineState` |
| `engineOwnedSession.tsx:211` | `__premationViewportHud` | `__morynexViewportHud` |
| `engineOwnedSession.tsx:211` | `__premationAutomation` | `__morynexAutomation` |

### Dashboard / Auth / Billing Strings (i18n)
**Files:** `src/core/i18n/locales/*.json` (en.json, es.json, zh-CN.json, etc.)

**Patterns to replace:**
- `"Premation"` → `"Morynex"`
- `"Premation Cloud"` → `"Morynex Cloud"` (or just `"Morynex"` for the product)
- `"Premation Store"` → `"Morynex Store"` / `"Morynex Plugin Store"`
- `"Premation account"` → `"Morynex account"`
- `"Premation app"` → `"Morynex app"`
- `"Premation 插件"` (zh-CN) → `"Morynex 插件"`
- `"Premation 云"` (zh-CN) → `"Morynex 云"`

### Page Titles (`PopoutRoute.tsx`)
```typescript
viewport: 'Viewport Preview — Premation'  // → 'Viewport Preview — Morynex'
timeline: 'Timeline — Premation'
presentation: 'Presentation Mode — Premation'
panel: '{name} — Premation'
detached: 'Detached Window'
```

### Index.html
```html
<title>Premation</title>  // → <title>Morynex</title>
```

### Deep Link Routes (`AppRouter.tsx`, `deepLink.ts`)
```typescript
premation://connections  // → morynex://connections
premation://billing      // → morynex://billing
premation://oauth        // → morynex://oauth
```

---

## 7. Build Scripts (`scripts/`)

### `stageEngine.cjs`
- Stages `premation-engine*`, `premation-host-bridge.node`, `premation-plugin-host*`
- Output dir: `build/engine/`

### `buildMcp.cjs`
- Output: `build/mcp/premation-mcp.cjs` → `build/mcp/morynex-mcp.cjs`

### `pack-plugin.mjs` / `sign-plugin.mjs` / `create-premation-plugin.mjs`
- Manifest: `premation-plugin.json` → **KEEP** (plugin ABI)
- Embedded sig: `premation-plugin.sig` → **KEEP**
- CLI tool: `premation-plugins` → `morynex-plugins`
- SDK paths: `share/premation-sdk/` → `share/mory-sdk/`

### `beforePack.cjs`
- Calls `stageEngine.cjs`

### Real-app Test Scripts (`scripts/realapp/`)
- Temp dirs: `premation-default-*`, `premation-transport-*`, `premation-rq-*`, `premation-d5-*`
- Window globals: `__premationEngineTransport`, `__premationEngineSurface`, `__premationProcessEngineState`, `__premationViewportHud`, `__premationAutomation`

---

## 8. Documentation (`docs/`, `CLAUDE.md`, `AGENTS.md`)

### Key Files
| File | References |
|------|------------|
| `docs/NATIVE_CORE_PLAN.md` | `premation-engine` throughout (architecture doc) |
| `docs/PLUGIN_SDK.md` | `premation_sdk`, `PremationSdk`, `premation-plugin.json`, `premation-plugins` |
| `docs/PLUGIN_STORE.md` | Store workflow, `Premation` branding |
| `docs/ENGINE_API.md` | Protocol docs, `premation` namespace |
| `docs/TS_ENGINE_REMOVAL.md` | History, `premation-engine` |
| `docs/B3_PATTERNS.md` | Engine command patterns |
| `docs/VIEWPORT_ROUTE.md` | Route A/B/C, `premation-engine` |
| `docs/MCP_PLAN.md` | `premation-mcp.cjs`, `premation://` scheme |
| `docs/SCRIPTING.md` | `premation` API, `Premation` in paths |
| `docs/AE_PARITY_PLAN.md` | `premation-engine`, `premation-plugin` |
| `CLAUDE.md` | Architecture rules, `premation-engine` |
| `AGENTS.md` | Agent instructions, `premation-engine` |

### Announcements
- `docs/announcements/email-v1.1.0.md` — marketing email, keep as historical

---

## 9. CI/CD (`.github/workflows/`)

### Repository References
| Workflow | Current | Target |
|----------|---------|--------|
| `ci.yml` | `isroil01/premation` | `rynex/morynex` |
| `native.yml` | `isroil01/premation` | `rynex/morynex` |
| `release.yml` | `isroil01/premation`, `premation-engine` artifacts | `rynex/morynex`, `morynex-engine` |
| `render-tests.yml` | `isroil01/premation` | `rynex/morynex` |

### Artifact Names
- `premation-engine` binary → `morynex-engine`
- SDK release: `premation-sdk-<platform>.zip` → `mory-sdk-<platform>.zip`

---

## 10. Legal / License

### License Headers
- All source files: `Premation contributors` → `Rynex contributors`
- AGPL-3.0-only preserved
- **Must retain upstream attribution** to `@isroil01` per AGPL

### Copyright
- `electron-builder.yml`: `© Premation contributors` → `© Rynex contributors`
- Package.json: `author: "Premation contributors"` → `"Rynex contributors"`

---

## 11. Plugin ABI Compatibility Strategy (Critical)

### Must Preserve (Breaking Change = No)
| Symbol/File | Reason |
|-------------|--------|
| `PremationPluginInfo` (C symbol) | Every existing plugin exports this |
| `PremationPluginExpressions` (C symbol) | Plugin expression tables |
| `PR_PLUGIN_INFO_SYMBOL` = `"PremationPluginInfo"` | Host looks up this exact string |
| `premation-plugin.json` manifest name | Engine scans for this filename |
| `premation-plugin.sig` embedded signature | Verification format |
| `com.premation.*` plugin IDs | Installed plugins keep their IDs |

### Dual-Naming Approach
```cpp
// In pr_effect.h — keep old symbol as weak alias
PR_EXPORT const PrPluginInfo* PR_CALL PremationPluginInfo(void);
PR_EXPORT const PrPluginInfo* PR_CALL MorynexPluginInfo(void) __attribute__((alias("PremationPluginInfo")));

// CMake: install both config names
install(EXPORT MorySdkTargets NAMESPACE mory:: DESTINATION ...)
install(FILES cmake/PremationSdkConfig.cmake ...)  // Keep for compat
```

### New Plugin Namespace
- New plugins: `com.mory.*` or `com.rynex.*`
- SDK install path: `include/mory_sdk/`, `lib/cmake/MorySdk/`
- CLI tool: `morynex-plugins`

---

## 12. Edition-Specific Considerations

### Local Edition (`VITE_EDITION=local`, `MOTION_EDITION=local`)
- No backend, no accounts
- Projects = `.motion` bundles on disk
- AI = BYOK (OS keystore)
- All rebrand applies equally

### Server Edition (default)
- Cloud backend (motion-back, not in this repo)
- `PREMATION_*` env vars still used by engine process
- Deep link `premation://oauth` used by backend redirect

---

## 13. File Tree Summary (High-Level)

```
/
├── package.json                          ← name, repo, author, scripts
├── electron-builder.yml                  ← appId, productName, artifacts, publish
├── electron-builder.selfhosted.yml       ← extends base, extraResources
├── index.html                            ← <title>
├── vite.config.ts                        ← aliases, defines
├── tsconfig.json                         ← paths, references
├── CLAUDE.md                             ← agent rules
├── AGENTS.md                             ← agent instructions (NEW)
├── docs/MORYNEX_AUDIT.md                 ← THIS FILE
├── src/
│   ├── main.tsx                          ← env vars, edition, deep link report
│   ├── App.tsx                           ← editor shell
│   ├── routes/AppRouter.tsx              ← premation:// routes
│   ├── pages/                            ← Dashboard, Auth, Editor, Welcome
│   ├── providers/Providers.tsx           ← About menu, export
│   ├── core/
│   │   ├── config/edition.ts             ← edition logic
│   │   ├── config/uiPlatform.ts          ← PREMATION_UI_PLATFORM
│   │   ├── config/flags.ts               ← LOCAL_FIRST
│   │   ├── engine/engineOwnedSession.tsx ← window.__premation* globals
│   │   ├── i18n/locales/*.json           ← ALL UI strings
│   │   └── ...
│   ├── layout/                           ← EditorLayout, PopoutRoute, Plugins
│   ├── hooks/                            ← useConnectionsDeepLink
│   └── types/motionEditor.d.ts           ← preload contract
├── electron/
│   ├── main.ts                           ← app setup, deep links, menus
│   ├── preload.ts                        ← IPC bridge, premation:// scheme
│   ├── engineHost.ts                     ← engine env vars, executable resolution
│   ├── engineSupervisor.ts               ← engine process management
│   ├── nativeMenu.ts                     ← app menu label
│   ├── deepLink.ts                       ← premation:// routing
│   ├── cliArgs.ts                        ← --premation-mcp-uninstall
│   ├── mcp/ipc.ts                        ← premation-mcp.cjs
│   ├── nativePluginStore.ts              ← plugin dirs
│   ├── pluginFileInstall.ts              ← .pplugin handling
│   ├── updater.ts                        ← "Updated to Premation X"
│   └── ipc/                              ← various IPC handlers
├── native/
│   ├── CMakeLists.txt                    ← project(MotionNative), premation_* cmake
│   ├── engine/
│   │   ├── CMakeLists.txt                ← premation-engine target
│   │   ├── src/main.cpp                  ← entry point
│   │   ├── src/core/*.cpp                ← namespace premation
│   │   ├── src/gpu/*.cpp                 ← namespace premation
│   │   ├── src/render/*.cpp              ← namespace premation
│   │   ├── proto/*.hpp                   ← namespace premation
│   │   └── shaders/*.hpp                 ← namespace premation
│   ├── protocol/
│   │   ├── include/premation/protocol/   ← wire, framing, frame_channel, pixel_channel
│   │   └── generated/                    ← engine_api_*.cpp (namespace premation::api)
│   ├── sdk/
│   │   ├── CMakeLists.txt                ← premation_sdk target, install
│   │   ├── include/premation_sdk/        ← C ABI headers (pr_effect.h, etc.)
│   │   ├── samples/*/                    ← sample plugins (com.premation.*)
│   │   └── fxkit/                        ← WGSL→C++ generator
│   └── libs/motion_eval/                 ← namespace premation::eval
├── scripts/
│   ├── stageEngine.cjs                   ← copies premation-engine*
│   ├── buildMcp.cjs                      ← builds premation-mcp.cjs
│   ├── pack-plugin.mjs                   ← premation-plugin.json manifest
│   ├── sign-plugin.mjs                   ← premation-plugin.sig
│   ├── create-premation-plugin.mjs       ← plugin scaffolding
│   ├── beforePack.cjs                    ← electron-builder hook
│   ├── native.mjs                        ← CMake configure/build/test
│   └── realapp/                          ← E2E test scripts (temp dirs, globals)
├── resources/
│   ├── fonts/                            ← fonts.json (PREMATION_FONTS_MANIFEST)
│   └── ffmpeg/                           ← GPL ffmpeg
└── .github/workflows/                    ← CI/CD (repo references)
```

---

## 14. Recommended Phase Plan

### Phase 1: Package & Config Identity (Low Risk)
- `package.json`, `electron-builder*.yml`, `index.html`, `vite.config.ts`
- Update repo URLs, appId, productName, copyright, artifact names
- **No code logic changes**

### Phase 2: Electron Main & Preload (Medium Risk)
- Deep link scheme: `premation://` → `morynex://` (registerProtocolClient, findDeepLink)
- Uninstall flag: `--premation-mcp-uninstall` → `--morynex-mcp-uninstall`
- Menu label: `Premation` → `Morynex`
- Updater message: `"Updated to Premation"` → `"Updated to Morynex"`
- Engine executable resolution: `premation-engine` → `morynex-engine` (with fallback)
- Environment variable prefixes: `PREMATION_` → `MORYNEX_` (with fallback reading both)

### Phase 3: C++ Engine Namespace (High Risk)
- Rename `namespace premation` → `namespace mory` across `native/engine/`, `native/protocol/`
- **Keep C ABI symbols** (`PremationPluginInfo`, `PremationPluginExpressions`) as aliases
- Update CMake targets: `premation_sdk` → `mory_sdk`, `PremationSdk` → `MorySdk`
- Update install paths: `premation_sdk` → `mory_sdk`
- Protocol codegen: update `packages/engine-api/codegen/` to emit `mory::api`
- **Test:** All native tests, plugin SDK samples, golden render tests

### Phase 4: SDK Headers & Plugin System (High Risk)
- Headers: `premation_sdk/pr_*.h` → `mory_sdk/pr_*.h` (install path)
- Keep `PremationPluginInfo` symbol export, add `MorynexPluginInfo` alias
- `premation_add_plugin()` → `mory_add_plugin()` (CMake function)
- CLI tool: `premation-plugins` → `morynex-plugins`
- Manifest filename: **KEEP `premation-plugin.json`** for engine compat
- Sample plugin IDs: **KEEP `com.premation.*`** for existing plugins

### Phase 5: Renderer/UI Strings (Medium Risk)
- i18n: Replace all `"Premation"` / `"Premation Cloud"` in `src/core/i18n/locales/*.json`
- Page titles: `PopoutRoute.tsx`, `index.html`
- Window globals: `__premationEngineTransport` → `__morynexEngineTransport` etc.
- Deep link handlers: `premation://` → `morynex://` in `AppRouter.tsx`
- Environment variable constants: `PREMATION_UI_PLATFORM` → `MORYNEX_UI_PLATFORM` (with fallback)

### Phase 6: Build Scripts & CI (Low Risk)
- `scripts/stageEngine.cjs`: copy `morynex-engine*`, `morynex-host-bridge.node`
- `scripts/buildMcp.cjs`: output `morynex-mcp.cjs`
- `scripts/pack-plugin.mjs`: SDK paths → `mory-sdk`
- `scripts/create-premation-plugin.mjs`: scaffold updates
- CI workflows: repo `isroil01/premation` → `rynex/morynex`
- Artifact names in release workflow

### Phase 7: Documentation (Low Risk)
- `docs/*.md`: Update architecture docs (keep historical context)
- `CLAUDE.md`, `AGENTS.md`: Update references
- **Do not rewrite history** — `docs/announcements/`, git history preserved

### Phase 8: Verification
- `npm run typecheck` — zero errors
- `npm test` — all ~440 suites pass
- `npm run lint` — within warning budget (129)
- `npm run native:test` — CTest all green
- `npm run render-tests` — golden parity
- `npm run bench:check` — benchmark ratchet
- Test local edition: `npm run electron:dev:local`
- Test plugin load: existing `com.premation.*` plugins still work

---

## 15. Open Questions

1. **Product name for "Premation Cloud"** — Keep "Morynex Cloud"? Or just "Morynex" (the app) with "Morynex Pro" for paid tier?

2. **Plugin store namespace** — New plugins use `com.mory.*` or `com.rynex.*`? Recommend `com.mory.*` (shorter, matches `mory` namespace).

3. **GitHub organization** — Target `rynex/morynex` confirmed? Need to create org/repo.

4. **Backward compatibility period** — How long to support `PREMATION_*` env vars and `premation://` deep links? Recommend 2 major versions.

5. **Windows uninstall flag** — `--premation-mcp-uninstall` used by NSIS installer. Must coordinate with installer script (`build/installer.nsh`).

6. **MCP server name** — `premation-mcp.cjs` → `morynex-mcp.cjs`. Claude Desktop config key changes.

---

## 16. Risk Register

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Breaking existing plugin ABI | Medium | Critical | Dual symbols, keep manifest filename |
| Deep link breakage (OAuth, connections) | High | High | Support both schemes during transition |
| CI/CD repo reference failures | Low | Medium | Update all workflows atomically |
| Golden render test drift | Low | Medium | Run `render-tests` after C++ namespace change |
| Electron-builder macOS notarization | Low | High | Update `mac.binaries` to new names, re-sign |
| Windows NSIS installer art | Low | Medium | Re-render `build/installer*.bmp` with new name |
| User data migration (localStorage keys) | Medium | Medium | `engineOwnedSession.tsx` recovery key rename |

---

**End of Audit** — Ready for Phase 1 implementation.