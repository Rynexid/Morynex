# Morynex Environment Configuration

> Complete reference for environment variables, their purposes, migration strategy from legacy `PREMATION_*`/`MOTION_*` prefixes to `MORYNEX_*`, and setup for development, testing, native engine integration, and production builds.

---

## 1. Architecture Overview

### 1.1 Variable Sources and Precedence

Environment variables are consumed at **three distinct layers**, each with its own prefix scope and precedence rules:

| Layer | Prefix Scope | Config Source | Precedence (high → low) |
|-------|--------------|---------------|--------------------------|
| **Renderer (Vite)** | `VITE_*`, `PREMATION_UI_*` | `.env*`, `vite.config.ts` `envPrefix` | `.env.production` > `.env.local` > `.env` > shell |
| **Electron Main** | `PREMATION_*`, `MOTION_*`, `MORYNEX_*` | Shell env, `.env.production` (via main process) | Shell > `.env.production` > defaults |
| **Native Engine** | `PREMATION_*`, `MOTION_*` | Passed explicitly by Electron main at spawn | Electron main's resolved values |

> **Key principle**: The native engine receives **only** the variables Electron main explicitly passes via `engineEnv` in `engineHost.ts`. It has no direct access to shell or `.env` files.

### 1.2 Migration Prefix Strategy

| Legacy Prefix | New Prefix | Status | Notes |
|---------------|------------|--------|-------|
| `PREMATION_*` | `MORYNEX_*` | **Phased** | Core engine, plugin, model, FFT, viewport, font, plugin path, MCP |
| `MOTION_*` | `MORYNEX_*` | **Phased** | Backend, edition, export, updater, API, local backend |
| `PREMATION_UI_*` | `MORYNEX_UI_*` | **Phased** | Vite `envPrefix` — renderer only |
| `VITE_*` | `VITE_*` | **Unchanged** | Standard Vite prefix, compiled into renderer bundle |

**Migration Rules**:
1. **Dual-read period**: Code reads `MORYNEX_*` first, falls back to `PREMATION_*`/`MOTION_*` with deprecation warning (dev only)
2. **Documentation**: Every variable documents both legacy and new names
3. **CI/CD**: GitHub Actions variables updated to `MORYNEX_*`; secrets unchanged
4. **Cutover**: After 0.1.0 release, legacy reads removed in 0.2.0

---

## 2. Variable Reference

### 2.1 Core Application (Electron Main)

| Variable | Legacy | New | Required | Default | Description |
|----------|--------|-----|----------|---------|-------------|
| `NODE_ENV` | — | — | No | `development` | Standard Node env; `production` in packaged app |
| `MORYNEX_EDITION` | `MOTION_EDITION` | `MORYNEX_EDITION` | No | `standard` | Edition: `standard` or `local` (dev builds) |
| `MORYNEX_BACKEND_ORIGIN` | `MOTION_BACKEND_ORIGIN` | `MORYNEX_BACKEND_ORIGIN` | **Prod: Yes** | `http://localhost:4000` | Backend API origin (renderer uses `VITE_BACKEND_ORIGIN`) |
| `MORYNEX_BACKEND_PORT` | `MOTION_BACKEND_PORT` | `MORYNEX_BACKEND_PORT` | No | `4000` | Local backend port (dev) |
| `MORYNEX_BACKEND_ENTRY` | `MOTION_BACKEND_ENTRY` | `MORYNEX_BACKEND_ENTRY` | No | — | Explicit backend entry path (self-hosted) |
| `MORYNEX_LOCAL_BACKEND` | `MOTION_LOCAL_BACKEND` | `MORYNEX_LOCAL_BACKEND` | No | `0` | Set `1` to run bundled backend |
| `MORYNEX_DISABLE_UPDATES` | `MOTION_DISABLE_UPDATES` | `MORYNEX_DISABLE_UPDATES` | No | `0` | Set `1` to disable auto-updater (locked-down deployments) |
| `MORYNEX_EXPORT_MAX_CONCURRENT` | `MOTION_EXPORT_MAX_CONCURRENT` | `MORYNEX_EXPORT_MAX_CONCURRENT` | No | `1` | Max concurrent export jobs |

**Consumed in**: `electron/main.ts`, `electron/edition.ts`, `electron/apiBase.ts`, `electron/backend.ts`, `electron/updater.ts`, `electron/exportProcess.ts`

### 2.2 Development Server & Renderer (Vite)

| Variable | Legacy | New | Required | Default | Description |
|----------|--------|-----|----------|---------|-------------|
| `VITE_BACKEND_ORIGIN` | — | — | **Prod: Yes** | `http://localhost:4000` | Backend origin compiled into renderer bundle |
| `VITE_MEDIA_ORIGINS` | — | — | No | — | Comma-separated extra media origins (CSP) |
| `VITE_EDITION` | — | — | No | `standard` | Sets `MOTION_EDITION=local` in dev scripts |
| `VITE_API_TARGET` | — | — | No | `http://localhost:4000` | Dev server proxy target for `/api` |
| `MORYNEX_UI_PLATFORM` | `PREMATION_UI_PLATFORM` | `MORYNEX_UI_PLATFORM` | No | Auto | Force desktop chrome: `mac`, `windows`, `linux` |

**Consumed in**: `vite.config.ts` (`envPrefix: ['VITE_', 'PREMATION_UI_']`), `src/core/config/uiPlatform.ts`, `electron/preload.ts`

> **Note**: `PREMATION_UI_*` variables reach the renderer via Vite's `envPrefix`. They are **not** passed to the native engine.

### 2.3 Native Engine & GPU

| Variable | Legacy | New | Required | Default | Description |
|----------|--------|-----|----------|---------|-------------|
| `MORYNEX_ENGINE_PATH` | `PREMATION_ENGINE_PATH` | `MORYNEX_ENGINE_PATH` | No | Auto-resolved | Override engine binary path (dev/debug) |
| `MORYNEX_ENGINE_ARGS` | `PREMATION_ENGINE_ARGS` | `MORYNEX_ENGINE_ARGS` | No | — | Extra engine flags (e.g. `--no-gpu --log-level debug`) |
| `MORYNEX_VIEWPORT_ROUTE` | `PREMATION_VIEWPORT_ROUTE` | `MORYNEX_VIEWPORT_ROUTE` | No | `auto` | Viewport path: `auto`, `shared`, `copy` |
| `MORYNEX_FONTS_MANIFEST` | `PREMATION_FONTS_MANIFEST` | `MORYNEX_FONTS_MANIFEST` | No | Bundled `fonts/fonts.json` | Custom fonts manifest path |
| `MORYNEX_HOST_BRIDGE_PATH` | `PREMATION_HOST_BRIDGE_PATH` | `MORYNEX_HOST_BRIDGE_PATH` | No | Auto-resolved | Override `premation-host-bridge.node` path (macOS) |
| `MORYNEX_GPU_TESTS` | `PREMATION_GPU_TESTS` | `MORYNEX_GPU_TESTS` | No | `0` | Set `1` to enable GPU-requiring tests |
| `MORYNEX_DUMP_DIR` | `PREMATION_DUMP_DIR` | `MORYNEX_DUMP_DIR` | No | — | Debug frame dump directory |
| `MORYNEX_SANITIZE` | `MOTION_SANITIZE` | `MORYNEX_SANITIZE` | No | `none` | Native sanitizers: `none`, `asan-ubsan`, `tsan` |

**Consumed in**: `electron/engineHost.ts`, `electron/engineSupervisor.ts`, `electron/sharedTextureHandles.ts`, `native/cmake/sanitizers.cmake`, test files

### 2.4 FFmpeg & Media

| Variable | Legacy | New | Required | Default | Description |
|----------|--------|-----|----------|---------|-------------|
| `MORYNEX_FFMPEG_PATH` | `FFMPEG_PATH` | `MORYNEX_FFMPEG_PATH` | No | Bundled ffmpeg | Override ffmpeg binary path |
| `MORYNEX_PACKAGE_WITHOUT_FFMPEG` | `PREMATION_PACKAGE_WITHOUT_FFMPEG` | `MORYNEX_PACKAGE_WITHOUT_FFMPEG` | No | `0` | Set `1` to allow packaging without ffmpeg (rehearsal) |
| `FFPROBE_PATH` | `FFPROBE_PATH` | `MORYNEX_FFPROBE_PATH` | No | System `ffprobe` | Override ffprobe path |

**Consumed in**: `electron/ffmpegService.ts`, `electron/main.ts`, `scripts/fetchFfmpeg.cjs`, `packages/render-worker/electron/main.cjs`

### 2.5 Plugin System

| Variable | Legacy | New | Required | Default | Description |
|----------|--------|-----|----------|---------|-------------|
| `MORYNEX_PLUGIN_PATH` | `PREMATION_PLUGIN_PATH` | `MORYNEX_PLUGIN_PATH` | No | — | Colon/semicolon-separated extra plugin folders |
| `MORYNEX_LIBRARY_DEV_DIR` | `PREMATION_LIBRARY_DEV_DIR` | `MORYNEX_LIBRARY_DEV_DIR` | No | `../premation-library` | Dev plugin library directory |

**Consumed in**: `electron/main.ts`, `electron/engineHost.ts`, `electron/nativePluginStore.ts`

### 2.6 ML Models (ONNX)

Each model has a pair: `_USER_DIR` (user install) and `_DIR` (bundled). Electron main resolves and passes to engine.

| Model | User Dir Variable | Bundled Dir Variable | Legacy Prefix | New Prefix |
|-------|-------------------|---------------------|---------------|------------|
| Object Matte (SAM) | `MORYNEX_SAM_USER_DIR` | `MORYNEX_SAM_DIR` | `PREMATION_SAM_*` | `MORYNEX_SAM_*` |
| Object Matte GPU | `MORYNEX_SAM_GPU_USER_DIR` | `MORYNEX_SAM_GPU_DIR` | `PREMATION_SAM_GPU_*` | `MORYNEX_SAM_GPU_*` |
| Face Landmarks | `MORYNEX_FACE_USER_DIR` | `MORYNEX_FACE_DIR` | `PREMATION_FACE_*` | `MORYNEX_FACE_*` |
| Depth Anything | `MORYNEX_DEPTH_USER_DIR` | `MORYNEX_DEPTH_DIR` | `PREMATION_DEPTH_*` | `MORYNEX_DEPTH_*` |
| Hand Landmarks | `MORYNEX_HAND_USER_DIR` | `MORYNEX_HAND_DIR` | `PREMATION_HAND_*` | `MORYNEX_HAND_*` |
| SAM 2 Video | `MORYNEX_SAM2_USER_DIR` | `MORYNEX_SAM2_DIR` | `PREMATION_SAM2_*` | `MORYNEX_SAM2_*` |
| ViTMatte | `MORYNEX_VITMATTE_USER_DIR` | `MORYNEX_VITMATTE_DIR` | `PREMATION_VITMATTE_*` | `MORYNEX_VITMATTE_*` |

**Consumed in**: `electron/modelManifest.ts`, `electron/engineHost.ts`, `electron/objectMatteModel.ts`, `electron/faceModel.ts`, `electron/handModel.ts`, `electron/depthModel.ts`

### 2.7 MCP (Model Context Protocol)

| Variable | Legacy | New | Required | Default | Description |
|----------|--------|-----|----------|---------|-------------|
| `MORYNEX_MCP_DIR` | `PREMATION_MCP_DIR` | `MORYNEX_MCP_DIR` | No | `<userData>/mcp` | MCP server config directory |
| `MORYNEX_MCP_NO_MAIN` | `PREMATION_MCP_NO_MAIN` | `MORYNEX_MCP_NO_MAIN` | No | `0` | Set `1` to import MCP tools without starting server |

**Consumed in**: `electron/mcp/ipc.ts`, `electron/mcp/extension.ts`, `packages/mcp-server/src/server.ts`, `scripts/buildMcp.cjs`

### 2.8 Packaging & Release Rehearsal

| Variable | Legacy | New | Required | Default | Description |
|----------|--------|-----|----------|---------|-------------|
| `MORYNEX_PACKAGE_WITHOUT_ENGINE` | `PREMATION_PACKAGE_WITHOUT_ENGINE` | `MORYNEX_PACKAGE_WITHOUT_ENGINE` | No | `0` | Set `1` to package without engine (local rehearsal) |
| `MORYNEX_PACKAGE_WITHOUT_FFMPEG` | `PREMATION_PACKAGE_WITHOUT_FFMPEG` | `MORYNEX_PACKAGE_WITHOUT_FFMPEG` | No | `0` | Set `1` to package without ffmpeg (local rehearsal) |
| `MORYNEX_APP_EXE` | `PREMATION_APP_EXE` | `MORYNEX_APP_EXE` | No | — | Drive packaged build for realapp scripts |

**Consumed in**: `scripts/stageEngine.cjs`, `scripts/fetchFfmpeg.cjs`, `scripts/realapp/*.cjs`

### 2.9 Native Build (CMake/vcpkg)

| Variable | Legacy | New | Required | Default | Description |
|----------|--------|-----|----------|---------|-------------|
| `MORYNEX_SANITIZE` | `MOTION_SANITIZE` | `MORYNEX_SANITIZE` | No | `none` | Sanitizer mode: `none`, `asan-ubsan`, `tsan` |
| `MORYNEX_ASAN_RUNTIME_DIR` | `MOTION_ASAN_RUNTIME_DIR` | `MORYNEX_ASAN_RUNTIME_DIR` | No | — | ASan runtime DLL directory (Windows) |

**Consumed in**: `native/cmake/sanitizers.cmake`, `native/vcpkg-overlays/*/portfile.cmake`, `native/engine/shaders/embed_wgsl.cmake`, `native/engine/catalog/embed_catalog.cmake`

### 2.10 Test & Benchmark

| Variable | Legacy | New | Required | Default | Description |
|----------|--------|-----|----------|---------|-------------|
| `BENCH_FULL` | — | — | No | `0` | Set `1` for full benchmark suite |
| `CI` | — | — | Auto | Auto | Set by GitHub Actions; affects test retries/reporters |
| `UPDATE_I18N_SOURCE` | — | — | No | `0` | Set `1` to update i18n source catalog |
| `GEN_NATIVE_CJKWRAP` | — | — | No | `0` | Set `1` to generate native CJK wrap fixtures |
| `VF_REFERENCE_DIR` | — | — | No | — | Variable font reference directory |
| `BIDI_UCD_DIR` | — | — | No | — | BIDI Unicode Character Database directory |
| `OTF_FIXTURE` | — | — | No | — | OpenType font fixture path |
| `JSMATH_SAMPLES` | — | — | No | `120` | JS math test samples per function |
| `NUMCONV_SAMPLES` | — | — | No | `220` | Number conversion test samples |

---

## 3. Migration Implementation

### 3.1 Dual-Read Helper Pattern

```typescript
// electron/env.ts (new utility)
export function getEnv(env: NodeJS.ProcessEnv, morynexKey: string, legacyKeys: string[]): string | undefined {
  // 1. New prefix first
  if (env[morynexKey]) return env[morynexKey];
  // 2. Legacy fallbacks (with dev warning)
  for (const k of legacyKeys) {
    if (env[k]) {
      if (!env.MORYNEX_SILENCE_LEGACY_WARN && process.env.NODE_ENV !== 'production') {
        console.warn(`[env] ${k} is deprecated; use ${morynexKey}`);
      }
      return env[k];
    }
  }
  return undefined;
}

// Usage
const enginePath = getEnv(process.env, 'MORYNEX_ENGINE_PATH', ['PREMATION_ENGINE_PATH']);
const backendOrigin = getEnv(process.env, 'MORYNEX_BACKEND_ORIGIN', ['MOTION_BACKEND_ORIGIN']);
const viewportRoute = getEnv(process.env, 'MORYNEX_VIEWPORT_ROUTE', ['PREMATION_VIEWPORT_ROUTE']);
```

### 3.2 Files to Update (Priority Order)

| File | Variables to Migrate | Notes |
|------|---------------------|-------|
| `electron/main.ts` | `PREMATION_DEV_URL`, `PREMATION_PLUGIN_PATH`, `PREMATION_LIBRARY_DEV_DIR`, `FFPROBE_PATH` | Entry point; high impact |
| `electron/engineHost.ts` | All `PREMATION_*` engine env vars (15+) | Core engine spawn config |
| `electron/engineSupervisor.ts` | `PREMATION_ENGINE_PATH` | Engine resolution |
| `electron/apiBase.ts` | `MOTION_BACKEND_ORIGIN`, `MOTION_BACKEND_PORT` | Backend connection |
| `electron/backend.ts` | `MOTION_BACKEND_*`, `MOTION_LOCAL_BACKEND` | Local backend |
| `electron/updater.ts` | `MOTION_DISABLE_UPDATES` | Auto-updater |
| `electron/exportProcess.ts` | `MOTION_EXPORT_MAX_CONCURRENT` | Export |
| `electron/edition.ts` | `MOTION_EDITION` | Edition |
| `electron/modelManifest.ts` | All `PREMATION_*_DIR/USER_DIR` | Model paths |
| `electron/ffmpegService.ts` | `FFMPEG_PATH` | FFmpeg |
| `electron/mcp/*` | `PREMATION_MCP_*` | MCP |
| `electron/nativePluginStore.ts` | `MOTION_REVOCATION_KEY` (comment only) | Revocation key |
| `vite.config.ts` | `envPrefix: ['VITE_', 'PREMATION_UI_']` → add `MORYNEX_UI_` | Renderer prefix |
| `scripts/stageEngine.cjs` | `PREMATION_PACKAGE_WITHOUT_ENGINE` | Packaging |
| `scripts/fetchFfmpeg.cjs` | `PREMATION_PACKAGE_WITHOUT_FFMPEG` | Packaging |
| `scripts/buildMcp.cjs` | `PREMATION_MCP_NO_MAIN` | MCP build |
| `scripts/realapp/*.cjs` | `PREMATION_*`, `MOTION_*` | Realapp scripts |
| `packages/render-worker/electron/main.cjs` | `PREMATION_ENGINE_PATH`, `FFMPEG_PATH` | Render worker |
| `jest.config.cjs` | `PREMATION_ENGINE_PATH` | Test config |
| `native/cmake/sanitizers.cmake` | `MOTION_SANITIZE`, `MOTION_ASAN_RUNTIME_DIR` | Native build |

### 3.3 Vite envPrefix Update

```typescript
// vite.config.ts
envPrefix: ['VITE_', 'PREMATION_UI_', 'MORYNEX_UI_'],
```

---

## 4. Environment Setup by Context

### 4.1 Local Development

```bash
# .env.local (gitignored)
VITE_BACKEND_ORIGIN=http://localhost:4000
VITE_EDITION=local
MORYNEX_EDITION=local
MORYNEX_BACKEND_ORIGIN=http://localhost:4000
MORYNEX_BACKEND_PORT=4000
MORYNEX_LOCAL_BACKEND=1
# Optional: force engine from local build
MORYNEX_ENGINE_PATH=/path/to/native/build/linux-clang/engine/premation-engine
# Optional: extra plugin dev folder
MORYNEX_PLUGIN_PATH=/path/to/my-plugins
# Optional: debug engine args
MORYNEX_ENGINE_ARGS="--log-level debug --no-gpu"
```

**Start commands**:
```bash
npm run electron:dev          # Standard edition
npm run electron:dev:local    # Local edition (sets VITE_EDITION=local, MOTION_EDITION=local)
```

### 4.2 Local Production Build (Release Rehearsal)

```bash
# .env.production (gitignored, copy from .env.production.example)
VITE_BACKEND_ORIGIN=https://api.morynex.app
VITE_MEDIA_ORIGINS=https://media.morynex.app,https://cdn.morynex.app

# Shell env for main process
export MORYNEX_BACKEND_ORIGIN=https://api.morynex.app
export MORYNEX_DISABLE_UPDATES=0
export MORYNEX_PACKAGE_WITHOUT_ENGINE=0
export MORYNEX_PACKAGE_WITHOUT_FFMPEG=0

npm run dist    # Full signed build (requires signing secrets)
npm run pack    # Unpacked, fast, unsigned
```

### 4.3 CI / GitHub Actions

**Repository Variables** (Settings → Variables):
```
MORYNEX_BACKEND_ORIGIN = https://api.morynex.app
MORYNEX_DISABLE_UPDATES = 0
VITE_BACKEND_ORIGIN = https://api.morynex.app
VITE_MEDIA_ORIGINS = https://media.morynex.app
```

**Repository Secrets** (Settings → Secrets):
```
CSC_LINK                  # macOS/Windows code signing cert (base64 .p12)
CSC_KEY_PASSWORD          # Cert password
APPLE_ID                  # Apple ID for notarization
APPLE_TEAM_ID             # Apple Team ID
APPLE_APP_SPECIFIC_PASSWORD # App-specific password
WIN_CSC_LINK              # Windows cert (separate if different)
```

**Workflow env** (`.github/workflows/*.yml`):
```yaml
env:
  MORYNEX_BACKEND_ORIGIN: ${{ vars.MORYNEX_BACKEND_ORIGIN }}
  VITE_BACKEND_ORIGIN: ${{ vars.VITE_BACKEND_ORIGIN }}
  VITE_MEDIA_ORIGINS: ${{ vars.VITE_MEDIA_ORIGINS }}
  MORYNEX_DISABLE_UPDATES: ${{ vars.MORYNEX_DISABLE_UPDATES }}
```

### 4.4 Native Engine Development

```bash
# Configure
npm run native:configure    # Runs scripts/native.mjs configure

# Build
npm run native:build        # cmake --build --preset linux-clang

# Test
npm run native:test         # ctest --preset linux-clang

# Benchmark
npm run native:bench        # ./build/linux-clang/bench/motion_bench

# Sanitizers (Linux only)
MORYNEX_SANITIZE=asan-ubsan npm run native:configure
MORYNEX_SANITIZE=asan-ubsan npm run native:build
MORYNEX_SANITIZE=asan-ubsan npm run native:test
```

**CMake Presets** (`native/CMakePresets.json`):
- `linux-clang` — Ubuntu, Clang, Ninja
- `macos-clang` — macOS, Apple Clang, Ninja
- `windows-clang-cl` — Windows, LLVM clang-cl, Ninja
- `linux-clang-asan-ubsan` — ASan+UBSan
- `linux-clang-tsan` — ThreadSanitizer
- `*-engine` — With Dawn, Skia, ffmpeg, OCIO (heavy)

### 4.5 Plugin SDK Development

```bash
# Install SDK from local build
cmake --install build/linux-clang --component sdk --prefix /path/to/morynex-sdk

# In plugin project
find_package(MorynexSdk REQUIRED)
morynex_add_plugin(my_plugin SOURCES plugin.cpp MANIFEST morynex-plugin.json)

# Test against local engine
export MORYNEX_ENGINE_PATH=/path/to/native/build/linux-clang/engine/premation-engine
export MORYNEX_PLUGIN_PATH=/path/to/my-plugin/build/plugins
```

### 4.6 MCP Server Development

```bash
# Build MCP bundle
npm run mcp:build    # scripts/buildMcp.cjs --check

# Run standalone (for testing)
export MORYNEX_MCP_DIR=/tmp/test-mcp
export PREMATION_MCP_NO_MAIN=1  # Legacy, will migrate
node build/mcp/premation-mcp.cjs
```

---

## 5. Production Build Checklist

### 5.1 Required Variables (Release Workflow Fails Without)

| Variable | Source | Validation |
|----------|--------|------------|
| `VITE_BACKEND_ORIGIN` | GitHub Variables | Release workflow `verify` job checks |
| `MORYNEX_BACKEND_ORIGIN` | GitHub Variables | Electron main reads at runtime |

### 5.2 Signing (Conditional)

| Platform | Secrets Required | Behavior if Missing |
|----------|------------------|---------------------|
| macOS | `CSC_LINK`, `CSC_KEY_PASSWORD`, `APPLE_ID`, `APPLE_TEAM_ID`, `APPLE_APP_SPECIFIC_PASSWORD` | **Unsigned** — Gatekeeper blocks on Sequoia+; auto-update disabled |
| Windows | `CSC_LINK`, `CSC_KEY_PASSWORD` | **Unsigned** — SmartScreen warning; auto-update works |
| Linux | None | **Unsigned** — AppImage works; no code-signing gate |

> The release workflow **fails** if signing is attempted and fails. It does **not** fall back to unsigned. Set `CSC_IDENTITY_AUTO_DISCOVERY=false` to explicitly skip signing.

### 5.3 Artifact Naming

| Platform | Artifact Pattern | Update Manifest |
|----------|------------------|-----------------|
| Windows | `Morynex-Setup-Windows.exe` | `latest.yml` |
| macOS (x64) | `Morynex-macOS-x64.dmg` | `latest-mac.yml` |
| macOS (arm64) | `Morynex-macOS-arm64.dmg` | `latest-mac.yml` (shared) |
| Linux | `Morynex-Linux-x86_64.AppImage` | `latest-linux.yml` |

---

## 6. Troubleshooting

### 6.1 Engine Won't Start

| Symptom | Likely Cause | Fix |
|---------|--------------|-----|
| `cannotStart` exit code (2) | No GPU / Vulkan / Metal / D3D12 | Check `MORYNEX_ENGINE_ARGS=--no-gpu`; verify drivers |
| `deviceLost` exit code (3) | GPU reset / driver crash | Update drivers; try `--no-gpu` |
| `framing` exit code (4) | Protocol mismatch | Engine/UI version mismatch; rebuild both |
| `PREMATION_ENGINE_PATH not found` | Engine not staged | Run `npm run native:build` then `npm run electron:build` |

### 6.2 Backend Connection Issues

| Symptom | Likely Cause | Fix |
|---------|--------------|-----|
| App loads `http://localhost:4000` in production | `VITE_BACKEND_ORIGIN` not set | Set in GitHub Variables; rebuild |
| Renderer shows CSP errors | `VITE_MEDIA_ORIGINS` missing | Add media CDN origins |
| Main process can't reach backend | `MORYNEX_BACKEND_ORIGIN` not set | Set in shell env / GitHub Variables |

### 6.3 Model Loading Failures

| Symptom | Likely Cause | Fix |
|---------|--------------|-----|
| "Model not found" for bundled model | `fetchModels.cjs` not run | Run `npm run fetch:models` before `electron:build` |
| User model ignored | Wrong `_USER_DIR` path | Check `electron/modelManifest.ts` resolution order |
| Checksum mismatch | Model file corrupted | Delete `<userData>/models/<id>/` and re-download |

### 6.4 Packaging Failures

| Error | Fix |
|-------|-----|
| `PREMATION_PACKAGE_WITHOUT_ENGINE ignored in CI` | Remove the env var; CI must always package engine |
| `ffmpeg not found` | Run `npm run fetch:models` (includes ffmpeg) or check `scripts/fetchFfmpeg.cjs` |
| `icon.png missing` | Ensure `build/icon.png` exists (tracked in git) |

---

## 7. Security Considerations

### 7.1 What Goes in VITE_* (Renderer Bundle)

- **PUBLIC by construction**: Every `VITE_*` value is compiled into `dist/` and ships in the installer
- **Safe**: Backend origin, media origins, edition, API target
- **Never put here**: API keys, secrets, database URLs, signing material, JWT secrets

### 7.2 What Goes in Electron Main Env

- **Private to main process**: Not compiled into renderer
- **Safe for**: Backend origin (duplicate), local backend config, update flags, engine paths
- **Never put here**: Secrets that shouldn't be in repo variables

### 7.3 What Goes in GitHub Actions Secrets

- **Code signing certs** (`CSC_LINK`, `CSC_KEY_PASSWORD`)
- **Apple notarization** (`APPLE_ID`, `APPLE_TEAM_ID`, `APPLE_APP_SPECIFIC_PASSWORD`)
- **Backend secrets** (API keys, DB URLs, AI keys) — in backend repo, not here

### 7.4 Variable Validation

```typescript
// electron/env.ts - add validation at startup
export function validateRequiredEnv(): void {
  const required = ['MORYNEX_BACKEND_ORIGIN'];
  const missing = required.filter(k => !process.env[k] && !process.env[legacyMap[k]]);
  if (missing.length > 0 && app.isPackaged) {
    throw new Error(`Missing required env: ${missing.join(', ')}`);
  }
}
```

---

## 8. Legacy Compatibility Matrix

| Legacy Variable | New Variable | Read Support Until | Remove In |
|-----------------|--------------|-------------------|-----------|
| `PREMATION_ENGINE_PATH` | `MORYNEX_ENGINE_PATH` | 0.1.x | 0.2.0 |
| `PREMATION_ENGINE_ARGS` | `MORYNEX_ENGINE_ARGS` | 0.1.x | 0.2.0 |
| `PREMATION_VIEWPORT_ROUTE` | `MORYNEX_VIEWPORT_ROUTE` | 0.1.x | 0.2.0 |
| `PREMATION_FONTS_MANIFEST` | `MORYNEX_FONTS_MANIFEST` | 0.1.x | 0.2.0 |
| `PREMATION_HOST_BRIDGE_PATH` | `MORYNEX_HOST_BRIDGE_PATH` | 0.1.x | 0.2.0 |
| `PREMATION_PLUGIN_PATH` | `MORYNEX_PLUGIN_PATH` | 0.1.x | 0.2.0 |
| `PREMATION_LIBRARY_DEV_DIR` | `MORYNEX_LIBRARY_DEV_DIR` | 0.1.x | 0.2.0 |
| `PREMATION_PACKAGE_WITHOUT_ENGINE` | `MORYNEX_PACKAGE_WITHOUT_ENGINE` | 0.1.x | 0.2.0 |
| `PREMATION_PACKAGE_WITHOUT_FFMPEG` | `MORYNEX_PACKAGE_WITHOUT_FFMPEG` | 0.1.x | 0.2.0 |
| `PREMATION_MCP_DIR` | `MORYNEX_MCP_DIR` | 0.1.x | 0.2.0 |
| `PREMATION_MCP_NO_MAIN` | `MORYNEX_MCP_NO_MAIN` | 0.1.x | 0.2.0 |
| `PREMATION_*_DIR` / `*_USER_DIR` | `MORYNEX_*_DIR` / `*_USER_DIR` | 0.1.x | 0.2.0 |
| `PREMATION_GPU_TESTS` | `MORYNEX_GPU_TESTS` | 0.1.x | 0.2.0 |
| `PREMATION_DUMP_DIR` | `MORYNEX_DUMP_DIR` | 0.1.x | 0.2.0 |
| `PREMATION_UI_PLATFORM` | `MORYNEX_UI_PLATFORM` | 0.1.x | 0.2.0 |
| `MOTION_EDITION` | `MORYNEX_EDITION` | 0.1.x | 0.2.0 |
| `MOTION_BACKEND_ORIGIN` | `MORYNEX_BACKEND_ORIGIN` | 0.1.x | 0.2.0 |
| `MOTION_BACKEND_PORT` | `MORYNEX_BACKEND_PORT` | 0.1.x | 0.2.0 |
| `MOTION_BACKEND_ENTRY` | `MORYNEX_BACKEND_ENTRY` | 0.1.x | 0.2.0 |
| `MOTION_LOCAL_BACKEND` | `MORYNEX_LOCAL_BACKEND` | 0.1.x | 0.2.0 |
| `MOTION_DISABLE_UPDATES` | `MORYNEX_DISABLE_UPDATES` | 0.1.x | 0.2.0 |
| `MOTION_EXPORT_MAX_CONCURRENT` | `MORYNEX_EXPORT_MAX_CONCURRENT` | 0.1.x | 0.2.0 |
| `MOTION_SANITIZE` | `MORYNEX_SANITIZE` | 0.1.x | 0.2.0 |
| `MOTION_ASAN_RUNTIME_DIR` | `MORYNEX_ASAN_RUNTIME_DIR` | 0.1.x | 0.2.0 |
| `FFMPEG_PATH` | `MORYNEX_FFMPEG_PATH` | 0.1.x | 0.2.0 |
| `FFPROBE_PATH` | `MORYNEX_FFPROBE_PATH` | 0.1.x | 0.2.0 |

---

## 9. Quick Reference: npm Scripts and Their Env

| Script | Key Env Variables Set | Description |
|--------|----------------------|-------------|
| `dev` | — | Vite dev server only |
| `dev:local` | `VITE_EDITION=local`, `MOTION_EDITION=local` | Local edition dev |
| `electron:dev` | — | Full Electron + Vite dev |
| `electron:dev:local` | `VITE_EDITION=local`, `MOTION_EDITION=local` | Local edition Electron dev |
| `electron:build` | — | Production build (requires `.env.production`) |
| `electron:build:local` | `VITE_EDITION=local`, `MOTION_EDITION=local` | Local edition production build |
| `pack` | — | Unpacked app (fast, unsigned) |
| `dist` | — | Signed installers (requires secrets) |
| `native:configure` | — | CMake configure |
| `native:build` | — | CMake build |
| `native:test` | `PREMATION_GPU_TESTS=1` (optional) | CTest |
| `fetch:models` | — | Downloads bundled ONNX models + ffmpeg |
| `mcp:build` | `PREMATION_MCP_NO_MAIN=1` (internal) | Builds MCP bundle |

---

## 10. Changelog

| Version | Date | Changes |
|---------|------|---------|
| 0.1.0 | 2026-11-26 | Initial comprehensive environment audit; dual-read migration strategy; `MORYNEX_*` prefix introduced; all 50+ variables documented |

---

*This document is part of the Morynex rebrand (v0.1.0). See `docs/mory/README.md` for the full planning index.*