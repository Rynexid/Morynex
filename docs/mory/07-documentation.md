# Phase 7 — Documentation Updates

**Status:** ⏳ Planned  
**Risk:** Low  
**Scope:** Architecture docs, agent rules, user-facing docs — **preserve historical context**

## Files to Modify

### Architecture Docs (`docs/`)

#### `docs/NATIVE_CORE_PLAN.md`
- **Do NOT rewrite history** — this documents the Premation engine plan
- Update **current/future references** only:
  - `premation-engine` → `morynex-engine` (in current-state descriptions)
  - `premation-plugin` → `morynex-plugin` (in current-state descriptions)
  - Keep historical section headers and past-tense descriptions
- Add note at top: "This document describes the Premation engine plan (now Morynex). Historical references to 'Premation' refer to the project's former name."

#### `docs/PLUGIN_SDK.md`
- Update SDK name references: `premation_sdk` → `mory_sdk`
- `PremationSdk` CMake config → `MorySdk`
- `premation-plugin.json` → **KEEP** (manifest filename, ABI)
- `premation-plugins` CLI → `morynex-plugins`
- `premation_add_plugin()` → `mory_add_plugin()` (with compat alias)
- Install paths: `premation_sdk/` → `mory_sdk/`
- Plugin IDs: new plugins use `com.mory.*` or `com.rynex.*`

#### `docs/PLUGIN_STORE.md`
- Store name: "Premation Plugin Store" → "Morynex Plugin Store"
- Token env: `PREMATION_TOKEN` → `MORYNEX_TOKEN`
- SDK version env: `PREMATION_SDK_VERSION` → `MORYNEX_SDK_VERSION`
- Release download: `isroil01/premation` → `rynex/morynex`
- Artifact names: `premation-sdk-<platform>.zip` → `mory-sdk-<platform>.zip`

#### `docs/ENGINE_API.md`
- Namespace: `premation::api` → `mory::api`
- Protocol types: `premation/protocol/` → `mory/protocol/`
- Codegen: generates `mory::api` types

#### `docs/TS_ENGINE_REMOVAL.md`
- Historical document — keep as-is
- Add note: "Premation engine → Morynex engine"

#### `docs/B3_PATTERNS.md`
- Engine command patterns — update `premation-engine` → `morynex-engine`
- `EngineClient` references unchanged (TS side)

#### `docs/VIEWPORT_ROUTE.md`
- Route documentation — update `premation-engine` → `morynex-engine`
- `PREMATION_VIEWPORT_ROUTE` → `MORYNEX_VIEWPORT_ROUTE` (with compat)

#### `docs/MCP_PLAN.md`
- MCP server: `premation-mcp.cjs` → `morynex-mcp.cjs`
- Deep link scheme: `premation://` → `morynex://`
- OAuth flow references

#### `docs/SCRIPTING.md`
- **Preserve as historical** — documents the scripting API
- Update current references: `premation` API namespace → `mory` (if changed)
- File paths: `%APPDATA%\Premation\Scripts` → `%APPDATA%\Morynex\Scripts`
- `~/Library/Application Support/Premation/Scripts` → `~/Library/Application Support/Morynex/Scripts`
- Console output: `premation.log` → `morynex.log` (or keep for compat)

#### `docs/AE_PARITY_PLAN.md`
- `premation-engine` → `morynex-engine`
- `premation-plugin` → `morynex-plugin`
- `premation-engine-headless` → `morynex-engine-headless`
- Repository references: `isroil01/premation` → `rynex/morynex`

#### `docs/announcements/*.md`
- **DO NOT MODIFY** — historical marketing emails
- Keep as record of project history

### Agent Instructions

#### `CLAUDE.md`
- Architecture section: `premation-engine` → `morynex-engine`
- Layering diagram: `premation-engine` → `morynex-engine`
- Native code section: `premation-engine` → `morynex-engine`
- Repo traps: keep as-is (technical)
- Update any command references

#### `AGENTS.md` (already created with Morynex context)
- Verify all references updated
- Native engine entry: `premation-engine` → `morynex-engine`
- Engine supervisor: `EngineSupervisor` (unchanged)
- Commands: `npm run native:*` (unchanged)

### New Documentation

#### `docs/mory/` (this directory)
- Phase 0-8 planning docs (this set)

#### `docs/MIGRATION_GUIDE.md` (new)
```markdown
# Migration Guide: Premation → Morynex

For plugin authors, script writers, and integrators.

## Plugin Authors
- Existing plugins (com.premation.*) continue to work unchanged
- New plugins: use `com.mory.*` namespace
- SDK: `find_package(MorySdk)` + `mory_add_plugin()`
- CLI tool: `morynex-plugins` (replaces `premation-plugins`)

## Script Authors
- `premation` API namespace → `mory` (in engine commands)
- Deep link scheme: `premation://` → `morynex://`
- Config paths: `Premation` → `Morynex` (AppData, Application Support)

## Users
- App name: Premation → Morynex
- Executable: Premation.exe → Morynex.exe (Windows)
- App bundle: Premation.app → Morynex.app (macOS)
- AppImage: Premation-Linux-* → Morynex-Linux-*
- Config directory: ~/.config/Premation → ~/.config/Morynex
- Data directory: ~/Premation → ~/Morynex (or platform equivalent)
```

## Verification
```bash
# Check for remaining "Premation" references in docs (excluding historical)
grep -r "Premation" docs/ --include="*.md" | grep -v "announcements" | grep -v "historical" | grep -v "formerly Premation"

# Verify links work
# Manual review of key docs
```

## Notes
- **Preserve git history** — don't rewrite commit messages
- **Preserve historical docs** — announcements, old plans
- Add "formerly Premation" notes where helpful for continuity
- Update user-facing paths (config directories) in documentation