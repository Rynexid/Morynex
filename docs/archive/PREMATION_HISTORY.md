# Premation Project History

## Overview

This document consolidates the complete development history of the **Premation** project from its earliest available version through **Premation 1.2.2**, based on evidence from the repository (changelog, git tags, commits, release announcements, and source code).

**Original Project:** Premation
**Original Developer:** @isroil01 (isroiljonrustamov25@gmail.com)
**Original Repository:** https://github.com/isroil01/premation
**Upstream Baseline for Morynex:** Premation 1.2.2 (commit `6cf64bac610b237ad0429b41bb2eff1bb936049b`, tagged `v1.2.2`, dated 2026-11-13)

---

## Historical Narrative

### Foundations (Pre-1.0 / Versions 0.x)

Premation began as an **AI-native motion design and animation editor** targeting desktop platforms, with an architecture explicitly modeled after Adobe After Effects: a React/Electron UI frontend backed by an independent C++ engine process.

Key early architectural decisions (documented in `docs/NATIVE_CORE_PLAN.md`, decided 2026-09-22, finalized 2026-10-04):

- **Electron/React is the UI only.** The engine is a C++ process (`premation-engine`), supervised by Electron main.
- **GPU via Dawn** (Chromium's WebGPU implementation): D3D12 on Windows, Metal on macOS, Vulkan on Linux.
- **32-bit float, scene-linear working space, OCIO colour management** built into the render graph from the first pass.
- **Versioned command API** between UI and engine: commands, queries, events, frames.
- **C++20, Clang everywhere** (clang-cl on Windows), CMake presets + vcpkg manifest.
- **One engine:** The C++ engine is the only engine. No TypeScript fallbacks or second implementation.

The migration from a TypeScript engine to the C++ engine was executed in phases (A–G) behind flags, with the TypeScript engine fully removed by 2026-10-03 (documented in `docs/TS_ENGINE_REMOVAL.md`).

### Version 0.8.x — After Effects Import & Text (2026-09)

- **0.8.5 (2026-09-20):** After Effects project import (`.aep`/`.aepx`) — compositions, layers, parenting, transforms, keyframes, easing, masks, text, effects matched by name. Footage relinked by filename. Plugin publishing workspace redesign. Cloud dashboard readability fixes. Timeline bulk editing.
- **0.8.3 (2026-09-14):** Full right-to-left/bidirectional text (Arabic, Hebrew), vertical writing with tate-chū-yoko and kinsoku for CJK, true optical kerning. Source-text expressions. Time Stretch on any layer. Rounded rectangles preserved through path operators. Projected shadows fixed. Properties panel infinite render loop fixed.

### Version 0.9.0 — The Native Engine (2026-10-06)

**Major architectural milestone:** Everything drawn by a native C++ engine in its own process.

- **Premation Cloud launched:** Free plan (5 projects, 7 days history) + paid Premation Cloud ($9/mo or $90/yr, no project cap, 90 days history).
- **Account management in-app:** Change email, change password, device list with sign-out, account deletion.
- **New sign-in screens:** Unified frame for sign in, create account, confirmation code, password reset. Google social sign-in.
- **Performance:** Viewport renders all benchmark compositions faster; layer styles on GPU (Inner Shadow 1080p: ~1s → 3ms, Stroke: 0.8s → 1ms). All 393 effect benchmarks >24 fps.
- **Cached playback** holds full frame rate on heavy compositions.
- **Export runs in engine:** Render Queue, Export, CLI all use same engine. 16-bit ProRes 4444 with alpha.
- **Crash-safe design:** Engine restart replays work; repeated failure offers recovery copy.
- **After Effects Layer menu:** Create Shapes/Masks from Text, Convert to Editable Shapes/Text, Separate layers, Uncompose.
- **Pop-out viewports** render live from engine.
- **Removed:** JavaScript/WGSL plugin system. Projects with such plugins open with removal notice.
- **Native plugins** via C++ SDK (install via plugins folder).
- **HDR10/HLG exports** via engine (10-bit HEVC, BT.2020).

### Version 1.0.x — Plugin Platform & AI Assistants (2026-10-13)

- **1.0.0 (2026-10-13):**
  - **AI Assistants:** Claude, ChatGPT, Cursor, Antigravity connect via Dashboard ▸ Connections (`premation://connections`). Free on every plan; connection stays on user's computer.
  - **Plugin SDK 1.2:** Plugins add menu commands, panels (pop-out), importers, export formats, presets, on-canvas handles. Read other times, scene depth, comp camera.
  - **Premation's own plugins** (Element 3D-class 3D model renderer) included with Premation Cloud; third-party plugins free.
  - **Plugin safety:** Unavailable plugin passes through with notice, never breaks project.
  - **60 more After Effects effects** (215 → 275), plus ink, Roughen Edges, dither, bundled fonts with per-character OS fallback.
  - **Faster 3D/playback:** 3D surface chains cached, shadow maps/SSAO/reflections reused, Auto resolution no longer pumps. Radiance .hdr skies, engine-drawn ground grid.
  - **Reliability:** Large layers no longer blank frame, busy renderer not killed as hung, edits survive engine restart.
  - **ffmpeg ships in every installer**, pinned by hash, one-click repair.

- **1.0.1 (2026-10-13):** ffmpeg in every installer (reiterated). Plugin publisher listings: icon, screenshots, About, per-version notes. macOS engine builds again. Settings model rows keep readable info column.

- **1.0.2 (2026-10-19):**
  - **Linux support:** x64 AppImage alongside Windows installer and macOS DMG. Same engine, bundled ffmpeg/fonts, in-place updates. Needs FUSE 2 (`libfuse2`) and Vulkan-capable GPU.
  - **Panels:** Unified tab strip and field grammar, calmer timeline and tool row.
  - **Text:** Tracking in 1/1000 em, alignment anchor on first baseline, vertical columns anchored at top, real italics, synthesized small caps. Character panel scrubs Size/Leading/Tracking in AE units, Style menu, Anchor row. Assistants set justification, italic face, tracking, anchor.
  - **Camera:** Crane, dolly, orbit rigs, Follow Path, Fly to Selected Layers, shake, orient along path, depth compositing, linked flare, photo to 3D, camera import/export, expressions, stereo rig. New Camera dialog. One lens list in mm. Shot header. Simpler Depth of Field.
  - **Cache:** RAM tier + native disk cache (AE-style) with honest per-scope bars. 3D blink gone.
  - **Export:** Everything renders locally; window-only footage staged, not refused.
  - **Assistants:** AI reaches engine's 3D (primitives, PBR, glass, extrusion, typed lights, shadows, environment). Proposes three concepts per brief, remembers recent pieces. `premation://connections` opens assistant Connections.

### Version 1.1.x — Composition Settings, Languages, AE Import, SDK 1.4 (2026-11)

- **1.1.0 (2026-11-03):**
  - **Smoother playback:** Frames built on more than one core; heavy comps ~2× faster before cache.
  - **Scrubbing:** Drag playhead no longer waits for background render-ahead. Viewer shows "Rendering…" / "Dropped N".
  - **Blend Colors Using 1.0 Gamma:** Project Settings option changes layer mixing (AE behavior). New projects blend in gamma space; old projects keep linear look. Sharp-cornered shape rectangles fill corners fully.
  - **Checkout in browser:** Subscribe/manage payment in browser with saved cards/wallets. Premation stays open, brings itself forward when done, notifies when plan active.
  - **Report a Problem:** Screenshot + diagnostics (build, OS, GPU, engine status, last engine log lines, paths removed). Optional project details. Report number + email confirmation.
  - **Premation Cloud:** Free trial when on sale, price after trial at chosen billing period. Trial shows first payment date. Cancelling says nothing charged. Plans compare Library. Cancel dialog no longer mentions API keys.
  - **Icons:** New chrome icon set (1px strokes on 16px grid, square caps, drawn at exactly 16px). Tools, layer kinds, timeline switches, transport, align, panel chrome. Toolbar shows armed tool by blue glyph alone.
  - **Timeline:** Right-click keyframe opens menu first time. Click on multi-selected keyframe selects just that keyframe. Easy Ease after clicking one diamond no longer eases whole row.
  - **Effects:** Track matte source stays hidden when matted layer hidden. Turbulent Displace, Curl Noise, Wave Warp, Ripple, Scatter, Roughen Edges push pixels past layer box. Glow radius → 250px.
  - **Graph editor:** Drag keyframe/handle no longer blinks. Focused key, handles, numeric strip stay put. Curve follows keys. First move not lost. Hover chip not stale. Value snapping follows Snapping toggle. Handle cannot pass other handle. Sampler never folds curve back.
  - **Keyframes:** Auto Bezier smooth through key (tangent follows neighbours). Continuous Bezier starts from those tangents. Roving key sides linear. Drag keys past neighbours (reorder). Value-graph handles/speeds overshoot. Value graph draws every curve on shared axis, toolbar toggle for per-curve fitting. Easy Ease/Easy Ease In/Easy Ease Out apply to key's arrival/departure (fixed In/Out reversal). Keyframe Velocity leaves hold segment hold. Dragging keys in timeline no longer sends graph-selected keys to time 0.
  - **Cache:** Onion skin / viewer LUT toggle no longer empties cache bar.
  - **Export:** Time-remapped/speed-ramped footage and retimed precomps export with sound following picture (varispeed).
  - **Viewport:** Machines without WebGPU (Linux Intel HD, software GL) draw through Canvas 2D. When nothing can draw, viewport says so.
  - **Help:** Open Logs Folder, Copy Diagnostics Report for shipped builds (no devtools).
  - **Stability:** Burst of edits (assistant building scene) no longer kills engine as "hung". One failed frame no longer takes engine down.
  - **Cache:** Play that dropped frames caches range before next play. Cache fill stores every frame drawn (disk tier takes what RAM refuses). Fill waits on own viewport's frames. Integrated GPUs/small-memory laptops get fitting memory budgets.

- **1.1.1 (2026-11-05):**
  - **Composition Settings in picture:** Linear/radial backgrounds render in viewer and exports. Transparent composition shows checkerboard. Cancel discards Grid & Guides / Color tab changes.
  - **Pure white:** In gamma blending, white was one level darker. Now exact in both blending modes.
  - **No black boxes from nested comps:** Empty nested comp or first layer not started no longer paints black. Gradient Ramp and Fractal Noise only paint where nested comp has content.
  - **Enter confirms more dialogs:** Keyframe Velocity, Transition Settings, Assemble from Footage, Interpret Footage, Duck Under Voice, Remove Silence, New Camera/Light/Primitive.
  - **More languages:** Japanese, Korean, German, French. Instant language switch.
  - **After Effects import:** Shape layers, animated outlines, relinked footage, expressions. Imported project opens on main comp.
  - **Plugin SDK 1.4:** Plugin layer types, simulations, meshes/lights in 3D scene, ML models, OpenFX and CLAP hosting, audio effects, hot reload for plugin developers.
  - **Character rigging:** Part layers follow bones of character.
  - **Calmer editor:** Status messages in cleaner status bar. Subject panel regrouped with style gallery.
  - **Timeline:** One Scale row with Constrain Proportions. Cleaner graph editor.

### Version 1.2.x — Tags Exist, No Documented Changelog (2026-11)

Git tags exist for **v1.2.0**, **v1.2.1**, **v1.2.2** but:
- No changelog entries for these versions
- No release announcements in `docs/announcements/`
- `package.json` at `v1.2.2` commit still reports `1.1.1`
- Latest commit at `v1.2.2` (6cf64bac, 2026-11-13): "Remove the temporary Linux engine diagnostic" — removal of a diagnostic workflow for Linux engine C++ runtime linking

The `v1.2.2` tag appears to mark the latest codebase state on the `native-core` branch as of 2026-11-13, incorporating post-1.1.1 engine diagnostics and fixes, but without a formal version bump in package.json or user-facing changelog.

---

## Architecture Decision Record (from `docs/NATIVE_CORE_PLAN.md`)

**Decided 2026-09-22, finalized 2026-10-04:**

| Decision | Choice |
|----------|--------|
| UI | React 18 + TypeScript + Zustand + Radix + CSS modules in Electron |
| Engine | `premation-engine`, native C++20 process spawned/supervised by Electron main |
| GPU | Dawn (WebGPU) — D3D12 (Windows), Metal (macOS), Vulkan (Linux) |
| Colour | 32-bit float, scene-linear, OCIO |
| UI↔Engine API | Versioned command API: commands, queries, events, frames |
| Language | C++20, Clang everywhere (clang-cl on Windows) |
| Not changing | UI stack, plugin permission model, project file format, golden-frame test gate |
| Not doing | C++ UI (Qt, ImGui); web editor; collaboration; render farm; public CLI product |

**Superseded 2026-09-28 (`docs/TS_ENGINE_REMOVAL.md`):** One engine. Flags and fallbacks gone. C++ engine always owns document. TypeScript engine deleted 2026-10-03.

---

## Two Editions (from `src/core/config/edition.ts`)

| Edition | `VITE_EDITION=local` | `server` (default) |
|---------|---------------------|-------------------|
| Accounts | None — opens straight into editor | Required |
| Projects | On disk, `.motion` bundle | Cloud, autosave |
| AI Assistant | BYOK (OS keystore) | Via hosted gateway |

**Always use `:local` scripts** for development (`npm run electron:dev:local`, `npm run dev:local`, `npm run build:local`).

---

## Key Milestones Summary

| Date | Version | Milestone |
|------|---------|-----------|
| 2026-09-14 | 0.8.3 | RTL/bidirectional text, vertical writing, optical kerning |
| 2026-09-20 | 0.8.5 | AE import, plugin publishing workspace, timeline bulk edit |
| 2026-10-06 | 0.9.0 | **Native C++ engine process**, Premation Cloud, Linux AppImage |
| 2026-10-13 | 1.0.0 | **Plugin SDK 1.2**, AI Assistants (Claude, ChatGPT, Cursor), 60 more effects |
| 2026-10-13 | 1.0.1 | ffmpeg in installers, plugin publisher listings, macOS engine fix |
| 2026-10-19 | 1.0.2 | Linux AppImage, panels unification, text/camera/cache/export/assistants |
| 2026-11-03 | 1.1.0 | Multi-core playback, scrubbing, gamma blend, browser checkout, Report Problem, Cloud trials, icons, timeline/graph/keyframe UX, cache/export/viewport/help/stability |
| 2026-11-05 | 1.1.1 | Composition Settings rendering, pure white, nested comp fix, Enter confirms, languages, AE import, **SDK 1.4**, character rigging |
| 2026-11-13 | 1.2.2 | Linux engine diagnostic removed (tag only, no changelog) |

---

## Notes on Historical Completeness

This history is compiled from:
- `CHANGELOG.md` (covers up to 1.1.1)
- Git tags (v0.1.0 through v1.2.2)
- Git commit messages
- `docs/announcements/` (email-v1.1.0.md, discord-v1.1.0.md)
- `docs/NATIVE_CORE_PLAN.md` (architecture decisions)
- `docs/TS_ENGINE_REMOVAL.md` (one-engine decision)
- Source code inspection

**Gaps:**
- No changelog for v1.2.0, v1.2.1, v1.2.2
- No release announcements for 1.2.x
- `package.json` version not bumped to 1.2.x at tag
- Early versions (v0.1.0–v0.7.0) have tags but no documented changelog entries in current CHANGELOG.md

These gaps are noted in `UPSTREAM_VERSIONS.md` and `ARCHIVE_MANIFEST.md`.