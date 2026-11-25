# Upstream Attribution — Premation Credits

## Original Project

**Project Name:** Premation
**Original Developer:** @isroil01 (isroiljonrustamov25@gmail.com)
**Original Repository:** https://github.com/isroil01/premation
**Upstream Baseline for Morynex:** Premation 1.2.2 (commit `6cf64bac610b237ad0429b41bb2eff1bb936049b`, tagged `v1.2.2`, dated 2026-11-13)

---

## Copyright & License

**License:** GNU Affero General Public License v3.0 only (AGPL-3.0-only)

**Copyright Holders:**
- Premation contributors (2026)
- Original author: @isroil01

**License Text:** The complete AGPL-3.0-only license text is preserved in `UPSTREAM_LICENSE.md` and in the repository's `LICENSE` file (if present) and `package.json` `"license": "AGPL-3.0-only"`.

---

## Attribution Requirements (Per AGPL-3.0-only)

As a derivative work based on Premation, **Morynex** must:

1. **Preserve all copyright notices** from the original work
2. **Preserve the license notice** stating the work is licensed under AGPL-3.0-only
3. **Preserve the "preferred form for making modifications"** (source code) availability
4. **Offer source code** to anyone who receives the work (including network users per AGPL)
5. **Not add restrictions** beyond those in AGPL-3.0-only
6. **State changes** made to the original work (this archive documents the rebrand)

---

## Original Contributors

Based on git history and `package.json` `"author": "Premation contributors"`:

### Primary Author
- **@isroil01** (isroiljonrustamov25@gmail.com) — Original creator, architect, and maintainer of Premation

### Co-Authors (from commit history)
- **Claude Fable 5.2** (noreply@anthropic.com) — AI-assisted development contributions

### Premation Contributors (collective)
All contributors to the Premation project whose work is embedded in the codebase at the 1.2.2 baseline.

---

## Third-Party Components (Bundled / Linked)

The Premation codebase incorporates or links to the following major third-party components. Their licenses are preserved and must be respected:

### Runtime Dependencies (Electron App)
| Component | License | Purpose |
|-----------|---------|---------|
| Electron 44.4.5 | MIT / Chromium BSD | Application shell |
| React 18.3.1 | MIT | UI framework |
| Zustand 4.5.5 | MIT | State management |
| Radix UI | MIT | Accessible components |
| TypeScript 5.6.2 | Apache-2.0 | Language |
| Vite 5.4.8 | MIT | Build tool |
| Jest 29.6.1 | MIT | Testing |
| ESLint 9.39.5 | MIT | Linting |

### Native Engine (C++)
| Component | License | Purpose |
|-----------|---------|---------|
| Dawn (Chromium WebGPU) | BSD-3-Clause | GPU abstraction (D3D12/Metal/Vulkan) |
| Skia (Graphite) | BSD-3-Clause | 2D graphics / text shaping |
| HarfBuzz | MIT | Text shaping |
| ffmpeg | GPL-3.0 (GPL build) | Media decode/encode (libx264, libx265, libvpx-vp9, libopus) |
| zstd | BSD-3-Clause | Disk cache compression |
| vcpkg packages | Various (mostly BSD/MIT) | C++ dependencies |
| Catch2 | BSL-1.0 | C++ testing |
| Google Benchmark | Apache-2.0 | C++ benchmarking |

### Fonts (Bundled)
| Font Family | License | Source |
|-------------|---------|--------|
| Inter | OFL-1.1 | Google Fonts |
| Roboto | Apache-2.0 | Google Fonts |
| Outfit | OFL-1.1 | Google Fonts |
| Playfair Display | OFL-1.1 | Google Fonts |
| Fira Code | OFL-1.1 | Mozilla |
| Montserrat | OFL-1.1 | Google Fonts |
| Lora | OFL-1.1 | Google Fonts |
| Merriweather | OFL-1.1 | Google Fonts |
| PT Sans | OFL-1.1 | ParaType |
| Open Sans | Apache-2.0 | Google Fonts |

Font licenses preserved in `resources/fonts/*-OFL.txt`.

---

## Morynex Attribution Statement

**Morynex** is a derivative work based on **Premation 1.2.2**, created by **Rynex**.

```
Morynex 0.1.0
Copyright (C) 2026 Rynex contributors
Based on Premation 1.2.2
Copyright (C) 2026 Premation contributors (original author: @isroil01)

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU Affero General Public License as published by
the Free Software Foundation, version 3 of the License only.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU Affero General Public License for more details.

You should have received a copy of the GNU Affero General Public License
along with this program. If not, see <https://www.gnu.org/licenses/>.

Original Premation repository: https://github.com/isroil01/premation
Morynex repository: https://github.com/rynex/morynex
```

---

## Files Requiring Attribution Preservation

The following files in the codebase contain copyright/license headers that must be preserved during the rebrand:

- All source files in `native/` (C++ files with `// Premation` headers)
- All source files in `electron/` (TypeScript files)
- All source files in `src/` (TypeScript/React files)
- `package.json` (license field, author field)
- `electron-builder.yml` (copyright field)
- `LICENSE` file (if present)
- `docs/announcements/*` (historical, preserve as-is)
- `CHANGELOG.md` (historical, preserve as-is)

---

## Historical Records (Preserved As-Is)

The following document the original project's history and must not be altered:

- `CHANGELOG.md` — Premation release notes (up to 1.1.1)
- `docs/announcements/email-v1.1.0.md` — Premation 1.1.0 release email
- `docs/announcements/discord-v1.1.0.md` — Premation 1.1.0 Discord announcement
- `docs/NATIVE_CORE_PLAN.md` — Architecture decisions (refer to "Premation")
- `docs/TS_ENGINE_REMOVAL.md` — One-engine decision (refer to "Premation")
- `docs/PLUGIN_SDK.md` — Plugin SDK documentation (refer to "Premation")
- `docs/ENGINE_API.md` — Engine API documentation (refer to "Premation")
- `docs/B3_PATTERNS.md` — Engine command patterns (refer to "Premation")
- `docs/VIEWPORT_ROUTE.md` — Viewport route documentation (refer to "Premation")
- `CLAUDE.md` — Agent rules (refer to "Premation")
- Git commit history (preserved in full)

---

## Morynex-Specific Attribution

New files created for Morynex (rebrand, archive, new features) carry:

```
Copyright (C) 2026 Rynex contributors
Licensed under AGPL-3.0-only
Based on Premation by @isroil01
```

---

## Compliance Checklist for Morynex Releases

- [ ] AGPL-3.0-only license text included in distribution
- [ ] Copyright notices preserved in all source files
- [ ] `package.json` license field = `"AGPL-3.0-only"`
- [ ] `electron-builder.yml` copyright field references both Premation and Rynex
- [ ] Source code offered to all recipients (including network users)
- [ ] Modifications from Premation documented (this archive)
- [ ] Third-party licenses (ffmpeg GPL, Dawn BSD, etc.) respected and included
- [ ] Font licenses (OFL/Apache-2.0) included in `resources/fonts/`
- [ ] Original Premation repository URL referenced in documentation