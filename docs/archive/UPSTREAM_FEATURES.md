# Upstream Features — Premation Capability Inventory

## Overview

Complete inventory of Premation capabilities at the upstream baseline (Premation 1.2.2, commit `6cf64bac`). Features are organized by subsystem and marked with verification status.

**Verification Levels:**
- ✅ **Verified** — Documented in changelog, has tests, visible in source
- 🔶 **Partially Implemented** — Code exists but incomplete, behind flag, or limited
- 🧪 **Experimental** — In development, not production-ready
- ❓ **Undocumented** — Code references exist but no changelog/docs
- ❌ **Removed** — Explicitly removed in a version

---

## 1. Editor UI (Electron/React)

### 1.1 Timeline
| Feature | Status | Since | Notes |
|---------|--------|-------|-------|
| Multi-track timeline with layers | ✅ | 0.8.x | |
| Keyframe editing (add/move/delete/ease) | ✅ | 0.8.x | |
| Graph editor (value/speed curves) | ✅ | 0.8.x | 1.1.0: drag no longer blinks, shared axis, per-curve fitting |
| Auto Bezier / Continuous Bezier / Roving keys | ✅ | 1.1.0 | Smooth through key, linear roving sides |
| Hold keyframes | ✅ | 1.1.0 | Keyframe Velocity leaves hold segment hold |
| Drag keys past neighbours (reorder) | ✅ | 1.1.0 | |
| Value-graph handles/speeds overshoot | ✅ | 1.1.0 | |
| Easy Ease / In / Out on arrival/departure | ✅ | 1.1.0 | Fixed In/Out reversal |
| Scale row with Constrain Proportions | ✅ | 1.1.1 | Single Scale row |
| Timeline bulk editing (range select, drag group, stagger, zigzag) | ✅ | 0.8.5 | |
| Zoom anchored to pointer | ✅ | 0.8.5 | |
| Nudge by sub-frame | ✅ | 0.8.5 | |
| Right-click keyframe → menu first time | ✅ | 1.1.0 | |
| Click multi-selected keyframe → selects one | ✅ | 1.1.0 | |
| Onion skin toggle | ✅ | 1.1.0 | No longer empties cache bar |

### 1.2 Composition & Layers
| Feature | Status | Since | Notes |
|---------|--------|-------|-------|
| Composition Settings (background, grid, guides) | ✅ | 1.1.1 | Renders in viewer/exports; transparent = checkerboard |
| Nested compositions | ✅ | 0.9.0 | Empty/first-layer-not-started no longer paints black |
| Layer parenting | ✅ | 0.9.0 | |
| Layer styles (Inner Shadow, Stroke, etc.) | ✅ | 0.9.0 | Moved to GPU in 0.9.0 (Inner Shadow 1080p: ~1s → 3ms) |
| Track mattes (source stays hidden when matted layer hidden) | ✅ | 1.1.0 | |
| Layer menu: Create Shapes/Masks from Text | ✅ | 0.9.0 | Font outlines |
| Layer menu: Convert to Editable Shapes/Text | ✅ | 0.9.0 | For SVG |
| Layer menu: Separate Layers | ✅ | 0.9.0 | |
| Layer menu: Uncompose (un-precomp) | ✅ | 0.9.0 | |
| 3D layers (camera, lights, depth) | ✅ | 1.0.0 | AI reaches engine's 3D |
| Character rigging (part layers follow bones) | ✅ | 1.1.1 | |
| Shape layers (rect, ellipse, path, trim, repeater, zigzag) | ✅ | 0.8.3 | Rounded corners preserved through all operators |
| Merge Paths booleans | ✅ | 0.8.3 | |
| Path cloner | ✅ | 0.8.3 | |
| Knife tool | ✅ | 0.8.3 | |

### 1.3 Text
| Feature | Status | Since | Notes |
|---------|--------|-------|-------|
| Real multilingual (RTL/bidirectional: Arabic, Hebrew) | ✅ | 0.8.3 | Mixed with Latin in one line |
| Vertical writing (CJK: tate-chū-yoko, kinsoku) | ✅ | 0.8.3 | |
| True optical kerning | ✅ | 0.8.3 | |
| Source-text expressions | ✅ | 0.8.3 | Drive what text layer says |
| Tracking in 1/1000 em per range | ✅ | 1.0.2 | |
| Alignment anchor on first baseline | ✅ | 1.0.2 | |
| Vertical columns anchored at top | ✅ | 1.0.2 | |
| Real italics of system families | ✅ | 1.0.2 | |
| Synthesized small caps | ✅ | 1.0.2 | |
| Character panel: Size/Leading/Tracking in AE units | ✅ | 1.0.2 | Style menu, Anchor row |
| Assistants can set justification, italic, tracking, anchor | ✅ | 1.0.2 | |
| Bundled fonts with per-character OS fallback | ✅ | 1.0.0 | |

### 1.4 Camera & 3D
| Feature | Status | Since | Notes |
|---------|--------|-------|-------|
| Crane, dolly, orbit rigs | ✅ | 1.0.2 | |
| Follow Path | ✅ | 1.0.2 | |
| Fly to Selected Layers | ✅ | 1.0.2 | |
| Shake, orient along path | ✅ | 1.0.2 | |
| Depth compositing | ✅ | 1.0.2 | |
| Linked flare | ✅ | 1.0.2 | |
| Photo to 3D | ✅ | 1.0.2 | |
| Camera import/export | ✅ | 1.0.2 | |
| Camera expressions | ✅ | 1.0.2 | |
| Stereo rig | ✅ | 1.0.2 | |
| New Camera dialog (asks what you're doing) | ✅ | 1.0.2 | One lens list in mm, Shot header, simpler DoF |
| Radiance .hdr skies | ✅ | 1.0.0 | |
| Engine-drawn ground grid | ✅ | 1.0.0 | |
| 3D surface chains cached & sized to layer | ✅ | 1.0.0 | |
| Shadow maps, SSAO, reflections reused between frames | ✅ | 1.0.0 | |
| Layer that only moves reuses effect chain | ✅ | 1.0.0 | |
| Auto resolution no longer pumps | ✅ | 1.0.0 | |
| Element 3D-class plugin (glTF model, comp camera/lights, motion blur, occlusion, overlay, material panel) | ✅ | 1.0.0 | Included with Premation Cloud |

### 1.5 Viewport & Rendering
| Feature | Status | Since | Notes |
|---------|--------|-------|-------|
| Route C: Shared GPU texture (Dawn/NT handles/IOSurface/dmabuf) | ✅ | 1.0.x | Chosen in C1; frames never on command pipe |
| Route A: Pixel copy fallback | ✅ | 1.0.x | Linux w/o dmabuf, macOS w/o host bridge |
| Canvas 2D fallback (no WebGPU) | ✅ | 1.1.0 | Linux Intel HD, software GL |
| Viewport says "Rendering…" / "Dropped N" | ✅ | 1.1.0 | |
| Cached playback holds full frame rate | ✅ | 0.9.0 | Once frames cached |
| Pop-out viewports (each renders live from engine) | ✅ | 0.9.0 | |
| Blend Colors Using 1.0 Gamma (Project Settings) | ✅ | 1.1.0 | New projects = gamma space; old = linear |
| Sharp-cornered shape rectangles fill corners | ✅ | 1.1.0 | |
| Pure white/black exact in both blending modes | ✅ | 1.1.1 | Fixed gamma blending white level |
| Linear/radial backgrounds render in viewer/exports | ✅ | 1.1.1 | Composition Settings |
| Transparent composition = checkerboard | ✅ | 1.1.1 | |

### 1.6 Effects (278 at baseline)
| Feature | Status | Since | Notes |
|---------|--------|-------|-------|
| GPU effects (WGSL on Dawn) | ✅ | 0.9.0 | |
| CPU effects (SIMD, 8/16/32 bpc, threaded rows) | ✅ | 0.9.0 | |
| Smart render (CPU + GPU paths) | ✅ | 0.9.0 | |
| Faded/mask-scoped effects stay on GPU | ✅ | 1.1.0 | No CPU fallback |
| Effect chain cached & sized to layer | ✅ | 1.0.0 | |
| 60+ After Effects effects added in 1.0.0 | ✅ | 1.0.0 | 215 → 275 |
| Ink, Roughen Edges, Dither | ✅ | 1.0.0 | |
| Turbulent Displace, Curl Noise, Wave Warp, Ripple, Scatter, Roughen Edges push pixels past layer box | ✅ | 1.1.0 | |
| Glow radius → 250px | ✅ | 1.1.0 | |
| Gradient Ramp, Fractal Noise only paint where nested comp has content | ✅ | 1.1.1 | |
| Track matte source hidden when matted layer hidden | ✅ | 1.1.0 | |
| Projected shadows no longer paint over casting layer | ✅ | 0.8.3 | |
| Lamp wash survives alongside shadow | ✅ | 0.8.3 | |

### 1.7 Panels & UI Chrome
| Feature | Status | Since | Notes |
|---------|--------|-------|-------|
| Unified tab strip & field grammar | ✅ | 1.0.2 | |
| Calmer timeline & tool row | ✅ | 1.0.2 | |
| Status messages in cleaner status bar | ✅ | 1.1.1 | |
| Subject panel regrouped with style gallery | ✅ | 1.1.1 | |
| New icon set (1px strokes, 16px grid, square caps, drawn at 16px) | ✅ | 1.1.0 | Tools, layer kinds, timeline switches, transport, align, panel chrome |
| Toolbar shows armed tool by blue glyph alone | ✅ | 1.1.0 | No grey well, no hover fill |
| Right-click keyframe opens menu first time | ✅ | 1.1.0 | |
| Properties panel no longer infinite re-render | ✅ | 0.8.3 | Fixed in 0.8.3 |
| Inspector ⋯ menu: keyframe lanes, Open Effect Controls | ✅ | 0.8.3 | Stay put |
| Color panel | ✅ | 1.1.1 | Cancel discards changes |

---

## 2. Engine (C++ premation-engine)

### 2.1 Evaluation
| Feature | Status | Notes |
|---------|--------|-------|
| Keyframe interpolation (linear, bezier, hold, roving) | ✅ | |
| Expressions (JavaScript-like, engine-side) | ✅ | |
| Parenting (transform hierarchy) | ✅ | |
| Time remapping | ✅ | |
| Motion blur (shutter + motion blur in PrInData) | ✅ | SDK 1.2 |

### 2.2 Render Graph
| Feature | Status | Notes |
|---------|--------|-------|
| Graph ordering + resource pools | ✅ | engine_rg_core |
| FX bounds (effect chain grow bounds) | ✅ | engine_rg_core |
| Dawn device management | ✅ | D3D12 / Metal / Vulkan |
| Compositor | ✅ | |
| Render thread + frame-slot ring | ✅ | |
| Shared textures (Route C) | ✅ | Windows NT handles, macOS IOSurface, Linux dmabuf |
| VRAM tracking / memory budgets | ✅ | Per-scope bars |

### 2.3 Colour
| Feature | Status | Notes |
|---------|--------|-------|
| 32-bit float scene-linear | ✅ | |
| OCIO (OpenColorIO) | ✅ | Built into render graph from first pass |
| Working space / display transform | ✅ | |

### 2.4 Cache
| Feature | Status | Notes |
|---------|--------|-------|
| RAM tier (preview cache) | ✅ | |
| Disk tier (AE-style native disk cache) | ✅ | zstd compression (frame_codec_zstd_ffi.cpp) |
| Raw frame codec fallback | ✅ | frame_codec_raw.cpp when zstd not found |
| Honest per-scope bars | ✅ | 1.0.2 |
| 3D blink gone | ✅ | 1.0.2 |
| Cache fill waits on own viewport's frames | ✅ | 1.1.0 |
| Integrated GPUs / small-memory laptops get fitting budgets | ✅ | 1.1.0 |

### 2.5 Media
| Feature | Status | Notes |
|---------|--------|-------|
| Video decode → GPU textures | ✅ | ffmpeg + NVDEC / D3D11VA / VideoToolbox |
| Audio decode / mix / playback | ✅ | Engine owns clock |
| Varispeed export (time-remapped audio follows picture) | ✅ | 1.1.1 |
| Time Stretch on any layer | ✅ | 0.8.3 |

### 2.6 Text & Vector (Engine-Side)
| Feature | Status | Notes |
|---------|--------|-------|
| Skia (Graphite on Dawn) + HarfBuzz | ✅ | |
| RTL/bidirectional, vertical CJK, optical kerning | ✅ | Matches UI |
| Source-text expressions | ✅ | |

### 2.7 Export
| Feature | Status | Notes |
|---------|--------|-------|
| Render Queue / Export / CLI → same engine | ✅ | |
| 16-bit ProRes 4444 with alpha | ✅ | |
| HDR10 (10-bit HEVC, HDR10 metadata, BT.2020) | ✅ | 0.9.0 |
| HLG export | ✅ | 0.9.0 |
| Multi-frame across threads | ✅ | |
| ffmpeg GPL build (libx264, libx265, libvpx-vp9, libopus) | ✅ | Pinned by SHA-256 |
| ffmpeg ships in installer, one-click repair | ✅ | 1.0.1 |

---

## 3. Plugin System (Native SDK)

### 3.1 SDK Versions & Capabilities
| SDK | Version | Capabilities |
|-----|---------|--------------|
| Base | 1.0 | Effects, generators |
| 1.1 | 1.1 | Composition camera/lights/layer transform, viewer overlays, panels, STRING/CURVE/GRADIENT/FILE params, presets |
| 1.2 | 1.2 | Commands, importers, output modules, checkout_time/checkout_param, shutter/motion blur, layer time, result_rect, PR_OUT_FLAG_COMP_SPACE, get_asset_image/sibling_bytes |
| 1.3 | 1.3 | PR_PARAM_CUSTOM_UI boxes in Effect Controls (DRAW_PARAM_UI/PARAM_UI_DRAG, ui_fill/ui_text) |
| 1.4 | 1.4 | **Manifest `layerTypes`** (layer drawn by generator), ML suite (ONNX), OpenFX hosting, CLAP hosting, plugin hot reload |

### 3.2 Plugin Types Supported
| Type | Status | Example |
|------|--------|---------|
| Effect (CPU) | ✅ | ripple, grade (CPU fallback) |
| Effect (GPU) | ✅ | grade (SMART_RENDER_GPU), fxkit (WGSL) |
| Generator | ✅ | rings (sequence data, button), model3d (Element 3D) |
| Command | ✅ | distribute, statspanel |
| Importer | ✅ | ppmio, ppmseq |
| Output Module | ✅ | ppmseq |
| Panel | ✅ | rings (overlay), statspanel, model3d (material) |
| Layer Type (SDK 1.4) | ✅ | model3d |
| OpenFX Host | ✅ | ofxinvert (out-of-process) |
| CLAP Host | ✅ | clapgain (out-of-process) |
| ML Models (ONNX) | ✅ | SDK 1.4 ML suite |

### 3.3 Plugin Safety
| Feature | Status | Notes |
|---------|--------|-------|
| Unavailable plugin passes through with notice | ✅ | Never breaks project |
| Crash isolation (per layer) | ✅ | Engine process failing = restart, not project loss |
| Out-of-process hosting (premation-plugin-host) | ✅ | OpenFX, CLAP, third-party |
| GPU texture handoff | ✅ | Plugin receives Dawn texture |
| Plugin signing / verification | ✅ | premation-plugin.sig, pack-plugin.mjs, sign-plugin.mjs |

### 3.4 Sample Plugins (Built-in)
| Plugin | Type | SDK | Notes |
|--------|------|-----|-------|
| ripple | Effect (CPU) | 1.0 | Smart render, threaded rows |
| rings | Generator + Panel | 1.1 | Sequence data, button, overlay |
| grademap | Effect | 1.1 | STRING/CURVE/GRADIENT/FILE, .cube LUT |
| checkout | Effect | 1.2 | Layer checkouts (displace, echo) |
| distribute | Command | 1.2 | |
| ppmio | Importer | 1.2 | |
| ppmseq | Output Module | 1.2 | |
| statspanel | Panel | 1.2 | |
| model3d | Element 3D | 1.2 | glTF, camera/lights, motion blur, occlusion |
| grade | Effect (GPU+CPU) | 1.2 | SMART_RENDER_GPU + fallback |
| fxkit | Effect (WGSL) | 1.2 | GPU=WGSL, CPU=generated C++ twin |
| clapgain | CLAP Host | 1.4 | Out-of-process |
| ofxinvert | OFX Host | 1.4 | Out-of-process |
| silkwaves / bounce / particles / scatter / mlstyle | Various | 1.x | More samples |

---

## 4. Project & File Management

### 4.1 Project Format
| Feature | Status | Notes |
|---------|--------|-------|
| `.motion` directory bundle (chunked) | ✅ | Local edition source of truth |
| Single-file + cloud autosave | ✅ | Server edition |
| Temp-file + rename (crash-safe) | ✅ | Never overwrites user file |
| Version history (cloud: 90 days / local: unlimited) | ✅ | |

### 4.2 Undo/History
| Feature | Status | Notes |
|---------|--------|-------|
| Single unified history (scene + timeline) | ✅ | 1.0.x |
| Stable clip IDs | ✅ | |
| beginGesture/endGesture (drag = one undo entry) | ✅ | |

### 4.3 After Effects Import
| Feature | Status | Since | Notes |
|---------|--------|-------|-------|
| Open `.aep` / `.aepx` directly | ✅ | 0.8.5 | |
| Compositions, layers, parenting | ✅ | 0.8.5 | |
| Transforms with keyframes & easing | ✅ | 0.8.5 | |
| Masks | ✅ | 0.8.5 | |
| Text with font & tracking | ✅ | 0.8.5 | |
| Effects matched by name | ✅ | 0.8.5 | |
| Footage relinked by filename | ✅ | 0.8.5 | Missing media clearly listed |
| Import report (what came across / what didn't) | ✅ | 0.8.5 | Nothing silently dropped |

---

## 5. AI Assistants & Scripting

### 5.1 AI Assistants
| Feature | Status | Notes |
|---------|--------|-------|
| Connect via `premation://connections` deep link | ✅ | Dashboard ▸ Connections |
| Supported: Claude, ChatGPT, Cursor, Antigravity | ✅ | |
| Free on every plan | ✅ | Connection stays on user's computer |
| MCP server: `premation-mcp.cjs` | ✅ | Bundled, launched by Claude Desktop |
| Local edition: BYOK (OS keystore) | ✅ | Via `electron/aiProxy.ts` |
| Server edition: Hosted gateway | ✅ | Via `motion-back` |
| AI reaches engine 3D (primitives, PBR, glass, extrusion, lights, shadows, env) | ✅ | 1.0.2 |
| Proposes 3 concepts per brief, remembers recent | ✅ | 1.0.2 |

### 5.2 Scripting (AE-Style)
| Feature | Status | Notes |
|---------|--------|-------|
| Plain JS files (`.jsx` / `.js`) | ✅ | |
| Sandboxed Worker | ✅ | |
| `app` API (AE-compatible) | ✅ | |
| `premation` API (engine direct) | ✅ | |
| Console: `$.writeln`, `alert`, `premation.log` | ✅ | |
| Permission dialog before run | ✅ | Names script + permissions |
| Reaches project only via `app` / `premation` | ✅ | Can never undo |
| Scripts directory (Win: `%APPDATA%\Premation\Scripts`, macOS: `~/Library/Application Support/Premation/Scripts`, Linux: `~/.config/Premation/Scripts`) | ✅ | |

---

## 6. Platform & Distribution

### 6.1 Supported Platforms
| Platform | Status | Binary | Notes |
|----------|--------|--------|-------|
| Windows x64 | ✅ | NSIS installer (`.exe`), portable | AppImage not for Windows |
| macOS arm64 + x64 (universal) | ✅ | DMG | Hardened runtime, notarization |
| Linux x64 | ✅ | AppImage | FUSE 2 (`libfuse2`), Vulkan GPU driver |

### 6.2 Auto-Update
| Feature | Status | Notes |
|---------|--------|-------|
| electron-updater from GitHub Releases | ✅ | `latest.yml` / `latest-mac.yml` / `latest-linux.yml` |
| macOS: Squirrel.Mac requires signed updates | ✅ | Gatekeeper blocks unsigned |
| Windows: SmartScreen warns unsigned | ✅ | Still installs via "More info" |
| Linux: AppImage updated in place | ✅ | |

### 6.3 Code Signing
| Platform | Cert | Notes |
|----------|------|-------|
| macOS | Developer ID Application (.p12) | CSC_LINK / CSC_KEY_PASSWORD |
| Windows | Code-signing cert | CSC_LINK / CSC_KEY_PASSWORD |
| Linux | None (AppImage unsigned) | No code-signing gate |

---

## 7. Two Editions

| Feature | Local (`VITE_EDITION=local`) | Server (default) |
|---------|------------------------------|------------------|
| Accounts | None — opens to editor | Required |
| Projects | Disk `.motion` bundle | Cloud, autosave |
| AI Assistant | BYOK (OS keystore) | Hosted gateway |
| Billing | None | Premation Cloud |
| Sync | None | Encrypted vault |
| Dashboard | None | Cloud dashboard |
| Library | Local only | Cloud + Pro items |
| Plugins | All free (native) | Premation Cloud plugins included |

**Dev scripts:** Always use `:local` (`npm run electron:dev:local`, `npm run dev:local`, `npm run build:local`).

---

## 8. Developer Tooling

| Tool | Status | Notes |
|------|--------|-------|
| TypeScript typecheck (`npm run typecheck`) | ✅ | `tsc --noEmit` (root + electron) |
| Jest tests (`npm test`) | ✅ | ~440 suites, <1 min |
| ESLint (`npm run lint`) | ✅ | max-warnings 129 (ratchet) |
| Native build (`npm run native:configure/build/test`) | ✅ | CMake presets, Ninja |
| Sanitizers (ASan/UBSan/TSan) | ✅ | Blocking in CI |
| clang-tidy | ✅ | Blocking in CI |
| Golden render tests (`npm run render-tests`) | ✅ | Parity gate |
| Benchmarks (`npm run bench` / `bench:check`) | ✅ | Ratchet on `bench/baseline.json` |
| Engine API codegen (`npm run engine-api:gen/check`) | ✅ | Schema → TS + C++ |
| Worktrees (`npm run worktree`) | ✅ | Parallel sessions |

---

## 9. Known Limitations / Gaps (at 1.2.2 Baseline)

| Area | Limitation |
|------|------------|
| Collaboration | Not implemented (not prevented) |
| Render farm | Not implemented |
| Public CLI product | Not implemented |
| Web editor | Not implemented |
| C++ UI (Qt/ImGui) | Not implemented |
| Plugin registry (JS/WGSL) | Removed in 0.9.0; native only |
| Cloud plugins in Local edition | Not available (no backend) |
| Linux: Wayland support | Not explicitly documented |
| ARM64 Windows | Not built |
| Linux ARM64 | Not built |

---

## Verification Summary

| Subsystem | Verified Features | Partially Implemented | Experimental | Undocumented |
|-----------|-------------------|----------------------|--------------|--------------|
| Editor UI | 45+ | 0 | 0 | 0 |
| Engine | 25+ | 0 | 0 | 0 |
| Plugin System | 20+ | 0 | 0 | 0 |
| Project/Files | 8 | 0 | 0 | 0 |
| AI/Scripting | 10 | 0 | 0 | 0 |
| Platform/Dist | 10 | 0 | 0 | 0 |
| Editions | 2 | 0 | 0 | 0 |
| Tooling | 10 | 0 | 0 | 0 |

**Total documented capabilities: ~130+**

All features marked ✅ have changelog entries, source code evidence, or both. No feature is claimed as fully functional without source-level evidence.