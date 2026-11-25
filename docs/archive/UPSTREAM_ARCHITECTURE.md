# Upstream Architecture — Premation Technical Architecture

## Overview

This document captures the complete technical architecture of the Premation project as it exists at the upstream baseline (Premation 1.2.2, commit `6cf64bac`). It is preserved for the Morynex project to maintain full understanding of the inherited codebase.

**Source:** `docs/NATIVE_CORE_PLAN.md`, `docs/TS_ENGINE_REMOVAL.md`, `docs/ENGINE_API.md`, `docs/B3_PATTERNS.md`, `docs/VIEWPORT_ROUTE.md`, `docs/PLUGIN_SDK.md`, `CLAUDE.md`, `AGENTS.md`, and source code inspection.

---

## High-Level Architecture

```
┌──────────────────────── Electron UI Process (React) ────────────────────────┐
│  Timeline · Graph Editor · Inspector · Layers · Effects · Dialogs · Menus    │
│  Read-only mirror of the document, fed by engine change events               │
│  Draws gizmos and handles immediately; never renders the composition         │
└───────────────────────┬──────────────────────────────────────────────▲───────┘
                        │ commands, queries (typed preload API)         │ events, frames
┌───────────────────────▼────────────── Electron Main ─────────────────┴───────┐
│  Window + OS services · File dialogs · Updates · IPC relay                   │
│  EngineSupervisor: spawns, watches, restarts premation-engine                │
└───────────────────────┬──────────────────────────────────────────────▲───────┘
                        │ versioned binary protocol over a pipe         │ frames: route
                        │                                               │ chosen in C1
┌───────────────────────▼──────────── premation-engine (C++) ──────────┴───────┐
│  Document + command log + undo                                               │
│  Evaluation: keyframes, interpolation, expressions, parenting, time remap   │
│  Render graph ── Dawn ── D3D12 / Metal / Vulkan                              │
│  Colour: float linear + OCIO    Cache: RAM + disk frames                     │
│  Media: ffmpeg + NVDEC / D3D11VA / VideoToolbox → GPU textures               │
│  Text + vector: Skia (Graphite on Dawn) + HarfBuzz                           │
│  Effects: WGSL on GPU, SIMD kernels on all cores                             │
|  Audio: decode, mix, playback; owns the clock                                |
|  Export: frames → ffmpeg, multi-frame across threads                         |
|  Plugin host: native SDK with GPU texture handoff                            |
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## Layering (Lint-Enforced)

```
Electron (electron/)  →  Editor (src/layout, src/components, src/stores, src/hooks)
                       →  Engine API (packages/engine-api, src/engine-client,
                          the document mirror)  →  premation-engine (native/)
```

**Hard constraints (enforced by ESLint):**
- `src/core/**` and `packages/**` never import `react`, `react-dom`, `zustand`, `@/layout/*`, `@/components/*`
- `packages/**` never import from `src/**`
- Hooks → `src/hooks`, stores → `src/stores`, components → `src/components` or owning layout
- Editor state (selection, zoom, panel layout, scroll) never enters the project document
- UI changes to the document go through the engine API (commands with inverses, queries, change events)

---

## Two Editions

| | **Local** (`VITE_EDITION=local`) | **Server** (default) |
|---|---|---|
| Accounts | None — opens straight into editor | Required |
| Projects | On disk, `.motion` bundle | Cloud, autosave |
| AI Assistant | BYOK (OS keystore) | Via hosted gateway |

**Always use `:local` scripts** (`npm run electron:dev:local`, `npm run dev:local`, `npm run build:local`). The `server` edition expects a backend (`motion-back`) not in this repo.

---

## Engine API (The Seam)

**Defined in:** `packages/engine-api/codegen/` (schema → TypeScript + C++ types)

### Commands (change document, undoable, have inverses)
```
createLayer · deleteLayer · reorderLayers · setParent · precompose
setProperty(layer, path, value) · setKeyframe · deleteKeyframe · setEasing
addEffect · removeEffect · setEffectParam · addMask · setMaskPath
setClip(trim/move/split) · setComposition · importAsset · relinkAsset
beginGesture(label) … endGesture()   ← a drag becomes ONE undo entry
undo · redo
```

### Transport (owned by engine)
`play`, `pause`, `seek(time)`, `setLoop`, `setPreviewQuality` — UI never calls per-frame render.

### Queries (read without changing)
Layer tree, property values at time, keyframes in range, waveform peaks, font list, asset metadata.

### Events (tell UI what changed, batched, with revision number)
`layerAdded`, `propertyChanged`, `keyframesChanged`, `clipChanged`, `historyChanged`, `renderStats`, `engineError`.

### Frames
Delivered to viewport via route chosen in Phase C1 (see Viewport Route below).

**Rules:** API versioned from day one; schema generates both TS and C++ types; every command loggable for replay; AI tools, scripts, plugins use same API.

---

## Viewport Route (Frame Delivery)

**Three routes prototyped (C1), Route C chosen:**

| Route | Mechanism | Status |
|-------|-----------|--------|
| A | Pixel copy (fd 5) — engine reads back each frame, writes pixels | Fallback |
| B | Shared memory buffer | Not used |
| **C** | **Shared GPU texture (Dawn/NT handles/IOSurface/dmabuf)** | **Chosen** |

**Route C (Shared Texture):**
- Engine renders to Dawn texture
- Exports as OS handle: Windows NT handle, macOS IOSurface, Linux dmabuf
- Main process imports via `sharedTexture.importSharedTexture`
- Sends to renderer via `sharedTexture.sendSharedTexture` → `VideoFrame`
- Page draws with `VideoFrame`; releases slot via `allReferencesReleased`
- **Frames never travel over command pipe**

**Route A (Copy) fallback:** Where shared textures don't work (Linux without dmabuf, macOS without host bridge). Engine offers `frames.copy`, main reads pixels on fd 5, pushes via `engine:pixels` IPC, page wraps in `VideoFrame`.

---

## Native Engine (C++20)

### Toolchain
- **Clang everywhere** (clang-cl on Windows)
- **CMake presets** + vcpkg manifest (committed baseline)
- **Ninja** generator
- **WASM** (Emscripten) glue builds as test target

### Safety Gates (Blocking in CI)
- ASan + UBSan run whole suite
- TSan runs everything threaded
- clang-tidy (cppcoreguidelines, bugprone, performance, modernize) — warnings as errors
- `-Wall -Wextra -Wpedantic -Werror -Wshadow -Wconversion`
- clang-format (after first formatting pass)

### Code Standards
- No raw `new`/`delete` — RAII everywhere
- `unique_ptr` by default; `shared_ptr` needs written justification
- Plain structs, `std::span`/`string_view` at boundaries
- No exceptions across protocol or C ABI
- FFI to ffmpeg/Skia/OS only in `*_ffi.cpp` files

### Determinism
- No wall-clock reads in rendering
- No unseeded randomness
- No fast-math (`-ffp-contract=off`)
- Same document + frame + settings ⇒ same pixels on every platform
- Golden render tests (`packages/render-tests`) are the parity gate

### Isolation
- Engine code runs ONLY in `premation-engine` process
- Plugins, decoders, effects fail per layer without taking frame down
- Engine process failing costs a restart, never the user's project
- No engine code in UI process (not in renderer, not as N-API addon)

### Engine Internal Libraries
```
motion_eval       →  Evaluation (keyframes, interpolation, expressions, parenting, time remap)
engine_protocol   →  Generated engine-API codec + hand-written runtime (wire, framing, frame channel)
engine_core       →  Document, history, evaluation, protocol session, transport clock
engine_rg_core    →  Render graph ordering + resource pools (GPU-free half)
engine_disk_cache →  Preview cache disk tier (zstd via frame_codec_zstd_ffi.cpp)
engine_gpu        →  Dawn device, compositor, render thread, frame-slot ring, shared textures
premation-host-bridge (macOS) → N-API module for IOSurface lookup (Route C)
premation-engine  →  Process: pipes, reader/writer threads, core loop
```

### Engine Process Lifecycle
1. Electron main spawns `premation-engine` via `EngineSupervisor` (`electron/engineSupervisor.ts`)
2. Engine starts, advertises capabilities (including `frames.sharedTexture` if Route C available)
3. Renderer page installs `sharedTexture` receiver
4. Engine pushes frames via frame channel (fd 3) → main imports shared texture → sends to page
5. Page draws, calls `release()`, main returns slot to engine
6. EngineSupervisor watches for crashes/hangs, restarts (max crash loop → offers recovery save)
7. On Chromium GPU process loss, EngineSupervisor restarts engine (must follow adapter)

---

## Plugin System (Native SDK)

### SDK Versioning
- **PR_SDK_VERSION_MAJOR** (1) — bumped on breaking change; host loads only plugins built against its major
- **PR_SDK_VERSION_MINOR** (4 at baseline) — bumped when something ADDED (new selectors, param types, host callbacks, trailing struct fields)
- Every struct starts with `struct_size`; fields past reported size don't exist

### Plugin Types (SDK 1.4)
| Type | Description |
|------|-------------|
| Effect | Pixel processing (CPU: smart render, 8/16/32 bpc, threaded rows; GPU: SMART_RENDER_GPU on Dawn device) |
| Generator | Creates layer content (sequence data, USER_CHANGED_PARAM) |
| Command | Non-effect plugins (SDK 1.2) |
| Importer | File import (SDK 1.2) |
| Output Module | Export format (SDK 1.2) |
| Panel | UI panel (SDK 1.1 overlay + panel) |
| Element 3D | glTF model through comp camera/lights, motion blur, scene-depth occlusion, overlay handle, material panel |

### ABI Stability
- C ABI (C99 headers, usable from C and C++)
- Plain structs, integers, doubles, pointers host owns
- No C++ exceptions, no STL types, no allocator mismatch
- Memory plugin must hand back is a host HANDLE (`pr_host.h`)
- Exported symbols: `PremationPluginInfo`, `PremationPluginExpressions` (weak aliases added for Morynex compat)

### Plugin Manifest
`premation-plugin.json` — engine scans for this filename (must be preserved for compat)

### Plugin Hosting
- In-process (engine loads plugin DLL/.so/.dylib directly)
- Out-of-process: `premation-plugin-host` (SDK 1.4, for OpenFX/CLAP/third-party)
- GPU texture handoff: plugin receives Dawn texture, renders into it

### Sample Plugins (in `native/sdk/samples/`)
- `ripple` — CPU effect, smart render
- `rings` — Generator with sequence data, button (USER_CHANGED_PARAM), SDK 1.1 overlay + panel
- `grademap` — STRING/CURVE/GRADIENT/FILE params, 1D .cube LUT via get_asset_bytes
- `checkout` — Layer checkouts (Layer Displace on GPU, Time Echo)
- `distribute` / `ppmio` / `ppmseq` / `statspanel` — SDK 1.2: command, importer, output module, panel
- `model3d` — Element 3D class: glTF model, comp camera/lights, motion blur, occlusion, overlay, material panel
- `grade` — GPU effect (SMART_RENDER_GPU) + CPU fallback
- `fxkit` — Two effects written once in WGSL; GPU path = WGSL, CPU path = generated C++ twin
- `clapgain` / `ofxinvert` — OpenFX and CLAP hosting (out-of-process)

### SDK Release Artifact
`cmake --install <build> --component sdk --prefix premation-sdk` lays out:
```
include/premation_sdk/*.h              → headers
lib/cmake/PremationSdk/                → find_package(PremationSdk) → premation::sdk + premation_add_plugin()
bin/premation-plugins                  → list/render/crash-check a bundle
share/premation-sdk/{pack,sign}-plugin.mjs  → package + sign for store
share/premation-sdk/create-premation-plugin.mjs → start plugin project
share/premation-sdk/fxkit/             → WGSL→C++ generator + runtime
share/premation-sdk/samples/           → sample plugins + common/sample_util.hpp
```

---

## Document Format

### `.motion` Bundle
- Directory bundle (chunked) — source of truth in Local edition
- Single-file + cloud autosave in Server edition
- **Never write over user's file** — temp-file + rename
- Safe saves: every persisted file written temp-then-rename; crash can't corrupt project

### Undo History
- Single unified history (scene + timeline coherent)
- Stable clip IDs
- `beginGesture`/`endGesture` — a drag becomes ONE undo entry

---

## Media Pipeline

| Component | Technology |
|-----------|------------|
| Video decode | ffmpeg + NVDEC (Windows) / D3D11VA (Windows) / VideoToolbox (macOS) → GPU textures |
| Audio decode | ffmpeg → engine audio mix/playback |
| Export encode | ffmpeg (libx264, libx265, libvpx-vp9, libopus) — GPL build required |
| Export | Engine renders frames → ffmpeg, multi-frame across threads |
| ffmpeg | Pinned by SHA-256 in `electron/ffmpegManifest.json`; staged per platform by `scripts/fetchFfmpeg.cjs`; ships in installer; app repairs if missing |

---

## Colour Management

- 32-bit float, scene-linear working space
- OCIO (OpenColorIO) built into render graph from first pass
- Not retrofitted — core to render graph design

---

## Text & Vector

- **Skia** (Graphite on Dawn) + **HarfBuzz**
- Real multilingual text: RTL/bidirectional (Arabic, Hebrew), vertical writing (CJK tate-chū-yoko, kinsoku), true optical kerning
- Source-text expressions drive text layer content
- Bundled fonts (Noto families, etc.) registered by engine first

---

## Effects

- **WGSL on GPU** (Dawn), **SIMD kernels on all CPU cores**
- Smart render: CPU and GPU paths, 8/16/32 bpc, threaded rows
- Effect chain cached and sized to layer
- Faded or mask-scoped effects stay on GPU instead of falling back to CPU
- 278 effects (EffectType) at baseline (215 → 275 in 1.0.0, plus SDK additions)

---

## Audio

- Engine owns the clock (transport)
- Decode, mix, playback in engine
- Varispeed export: time-remapped/speed-ramped footage exports with sound following picture

---

## Export

- Render Queue, Export, CLI all render through same engine
- 16-bit ProRes 4444 with alpha
- HDR10/HLG: 10-bit HEVC with HDR10 metadata, BT.2020
- Command line: `--captions`, `--data`, `--commands` run in engine

---

## AI Assistants (Claude, ChatGPT, Cursor, Antigravity)

- Connect via Dashboard ▸ Connections (`premation://connections` deep link)
- Free on every plan; connection stays on user's computer
- MCP server: `premation-mcp.cjs` (bundled, launched by Claude Desktop)
- Local edition: BYOK through OS keystore + `electron/aiProxy.ts`
- Server edition: via hosted gateway (`motion-back`)

---

## Scripting (After Effects-Style)

- Plain JavaScript files (`.jsx` or `.js`)
- Run in sandboxed Worker
- Access via `app` (AE-compatible) and `premation` (engine API direct)
- Console shows `$.writeln`, `alert`, `premation.log`
- Before run: dialog names script and permissions
- Reaches project only through `app` and `premation`; can never undo
- Scripts directory:
  - Windows: `%APPDATA%\Premation\Scripts`
  - macOS: `~/Library/Application Support/Premation/Scripts`
  - Linux: `~/.config/Premation/Scripts`

---

## Performance Discipline

- No React render per played frame (playback/scrubbing via store subscriptions and refs)
- Every performance change quotes before/after from `npm run bench` or viewport HUD
- `npm run bench:check` must stay green
- No per-frame allocations in hot paths without comment

---

## Reliability Discipline

- Single bad layer/effect/plugin/asset/decode never blanks frame or crashes editor
- Project saves: temp-file + rename (never overwrite user's file)
- Rendering deterministic: no wall-clock, no unseeded RNG, no DOM layout inputs

---

## CI Gates (`.github/workflows/`)

| Workflow | Purpose |
|----------|---------|
| `ci.yml` | `typecheck` → `test` → `lint` (every push/PR to native-core/dev/main) |
| `native.yml` | Native engine build/test/sanitizers/WASM/protocol (on native/** changes) |
| `release.yml` | Enforces tags only from `native-core`, version match, signed artifacts |
| `render-tests.yml` | Golden image comparison (parity gate) |

---

## Key Paths Reference

| Purpose | Path |
|---------|------|
| Engine API schema + codegen | `packages/engine-api/codegen/` |
| Document mirror (UI reads) | `src/stores/documentMirror.ts` |
| Edition/feature flags | `src/core/config/edition.ts` |
| Engine client (TS) | `src/engine-client/` |
| Engine supervisor (Electron main) | `electron/engineSupervisor.ts` |
| Native engine entry | `native/engine/src/main.cpp` |
| Render test harness | `packages/render-tests/` |
| Lint ratchet baselines | `src/__tests__/engineWriteRatchet.json`, `bench/baseline.json` |
| Native CMake workspace | `native/CMakeLists.txt` |
| SDK headers | `native/sdk/include/premation_sdk/` |
| Plugin samples | `native/sdk/samples/` |

---

## Architecture Decision Records (from `docs/NATIVE_CORE_PLAN.md`)

| Decision | Date | Status |
|----------|------|--------|
| Electron/React = UI only; C++ engine process | 2026-09-22 | Final |
| Dawn (WebGPU) for GPU | 2026-09-22 | Final |
| Float colour + OCIO from first pass | 2026-09-22 | Final |
| Versioned command API (commands/queries/events/frames) | 2026-09-22 | Final |
| One engine (C++ only, TS engine deleted) | 2026-09-28 | Final |
| Viewport Route C (shared GPU texture) | 2026-10-04 | Final |
| TypeScript engine fully removed | 2026-10-03 | Done |

---

## Preserved for Morynex

All of the above architecture is inherited by Morynex 0.1.0. The rebrand (Premation → Morynex, premation → mory namespaces) must preserve:

- Engine API contracts (wire format, command semantics)
- C++ ABI (plugin symbols, SDK headers)
- `.motion` project format
- Cross-platform support (Win/macOS/Linux)
- Edition behavior (local/server)
- Golden render test parity
- AGPL-3.0-only license and upstream attribution