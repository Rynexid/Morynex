# Phase 6 — Build Scripts & CI/CD

**Status:** ⏳ Planned  
**Risk:** Low-Medium  
**Scope:** Build scripts, executable names, CI workflows, artifact naming

## Files to Modify

### `scripts/stageEngine.cjs`
```javascript
// Line 2: Comment "Stage premation-engine" → "Stage morynex-engine"
// Line 16: "win32    premation-engine.exe" → "win32    morynex-engine.exe"
// Line 20: "darwin   premation-engine" → "darwin   morynex-engine"
// Line 24: "linux    premation-engine" → "linux    morynex-engine"
// Line 60: "premation-plugin-host" → "morynex-plugin-host"
// Line 63: return [...'premation-engine.exe', 'dxcompiler.dll', 'dxil.dll', 'DirectML.dll', 'pdfium.dll'...]
// Line 66: return [...'premation-engine', 'premation-host-bridge.node', 'libpdfium.dylib'...]
// Line 70: return [{ f: 'premation-engine', required: true }...]
// Line 119: chmodSync(path.join(OUT, 'premation-engine')) → chmodSync(path.join(OUT, 'morynex-engine'))
```

### `scripts/buildMcp.cjs`
```javascript
// Line 4: "build/mcp/premation-mcp.cjs" → "build/mcp/morynex-mcp.cjs"
// Line 20: outfile = path.join(root, 'build', 'mcp', 'premation-mcp.cjs')
// → outfile = path.join(root, 'build', 'mcp', 'morynex-mcp.cjs')
```

### `scripts/pack-plugin.mjs`
```javascript
// Line 11-12: Bundle structure comments — update "premation-engine" → "morynex-engine"
// Line 38: MANIFEST = 'premation-plugin.json' — KEEP (ABI)
// Line 40: EMBEDDED_SIGNATURE = 'premation-plugin.sig' — KEEP (ABI)
// Line 99: SDK path reference "share/premation-sdk/" → "share/mory-sdk/"
```

### `scripts/sign-plugin.mjs`
- No changes needed (uses manifest/sig constants from pack-plugin)

### `scripts/create-premation-plugin.mjs`
```javascript
// RENAME FILE: create-premation-plugin.mjs → create-morynex-plugin.mjs (or create-mory-plugin.mjs)

// Line 2: Comment "create-premation-plugin — start a Premation native plugin project"
// → "create-morynex-plugin — start a Morynex native plugin project"

// Line 5: Usage example
// node create-premation-plugin.mjs <dir> --id com.you.glow --name "Glow"
// → node create-morynex-plugin.mjs <dir> --id com.mory.glow --name "Glow"

// Line 13: "Every project gets: CMakeLists.txt (find_package(PremationSdk))"
// → "find_package(MorySdk)"

// Line 72: "node sdk/share/premation-sdk/fxkit/fxkit.mjs ."
// → "node sdk/share/mory-sdk/fxkit/fxkit.mjs ."

// Line 87-88: premation-plugins CLI
// ./sdk/bin/premation-plugins --plugins build/plugins/${o.slug} test...
// → ./sdk/bin/morynex-plugins --plugins build/plugins/${o.slug} test...

// Line 95: "A Premation native plugin..." → "A Morynex native plugin..."

// Line 99: "Unzip the SDK from a Premation release (premation-sdk-<platform>.zip)"
// → "Unzip the SDK from a Morynex release (mory-sdk-<platform>.zip)"

// Line 104: "./sdk/bin/premation-plugins --plugins build/plugins/${o.slug} list"
// → "./sdk/bin/morynex-plugins --plugins build/plugins/${o.slug} list"

// Line 113-114: sign/pack plugin paths
// node sdk/share/premation-sdk/sign-plugin.mjs keygen
// node sdk/share/premation-sdk/pack-plugin.mjs...
// → node sdk/share/mory-sdk/sign-plugin.mjs keygen
// → node sdk/share/mory-sdk/pack-plugin.mjs...

// Line 135: "# CI for ${o.name} (created by create-premation-plugin)."
// → "# CI for ${o.name} (created by create-morynex-plugin)."

// Line 137: "# Builds the plugin on Windows, macOS (universal) and Linux against the SDK from a Premation"
// → "# Builds the plugin on Windows, macOS (universal) and Linux against the SDK from a Morynex"

// Line 139: "# (docs/PLUGIN_STORE.md §2) and, on a tag, publishes it to the Premation plugin store."
// → "# (docs/PLUGIN_STORE.md §2) and, on a tag, publishes it to the Morynex plugin store."

// Line 143: "#   PREMATION_TOKEN         an access token from your signed-in Premation account"
// → "#   MORYNEX_TOKEN         an access token from your signed-in Morynex account"

// Line 145: "#   PREMATION_SDK_VERSION   the Premation release whose SDK to build against"
// → "#   MORYNEX_SDK_VERSION   the Morynex release whose SDK to build against"

// Line 164-166: Matrix SDK references
// sdk: premation-sdk-windows-x64 → sdk: mory-sdk-windows-x64
// sdk: premation-sdk-macos-arm64 → sdk: mory-sdk-macos-arm64
// sdk: premation-sdk-linux-x64 → sdk: mory-sdk-linux-x64

// Line 174: gh release download from isroil01/premation → rynex/morynex

// Line 209-210: Linux SDK download from isroil01/premation → rynex/morynex

// Line 217: node sdk/share/premation-sdk/pack-plugin.mjs → node sdk/share/mory-sdk/pack-plugin.mjs

// Line 227: node sdk/share/premation-sdk/sign-plugin.mjs publish → node sdk/share/mory-sdk/sign-plugin.mjs publish

// Line 240: "# ${o.name} — a Premation native plugin (effect template)."
// → "# ${o.name} — a Morynex native plugin (effect template)."

// Line 242: "#   cmake -B build -DCMAKE_PREFIX_PATH=/path/to/premation-sdk"
// → "#   cmake -B build -DCMAKE_PREFIX_PATH=/path/to/mory-sdk"

// Line 249: find_package(PremationSdk 1.2 REQUIRED)
// → find_package(MorySdk 1.2 REQUIRED)

// Line 250: premation_add_plugin(${o.slug} SOURCES src/${o.slug}.cpp MANIFEST premation-plugin.json)
// → mory_add_plugin(${o.slug} SOURCES src/${o.slug}.cpp MANIFEST premation-plugin.json)

// Line 254: #include <premation_sdk/premation_sdk.h>
// → #include <mory_sdk/premation_sdk.h>

// Line 259: "// ${o.name} — a Premation effect..."
// → "// ${o.name} — a Morynex effect..."

// Line 314: PR_PLUGIN_INFO_SYMBOL "PremationPluginInfo" — KEEP in generated code (ABI)
// Line 318: 'premation-plugin.json': json(manifest) — KEEP

// Line 337: "// ${o.name} — a Premation effect written in WGSL with fxkit..."
// → "// ${o.name} — a Morynex effect written in WGSL with fxkit..."

// Line 339: "#   cmake -B build -DCMAKE_PREFIX_PATH=/path/to/premation-sdk"
// → "#   cmake -B build -DCMAKE_PREFIX_PATH=/path/to/mory-sdk"
```

### `scripts/beforePack.cjs`
```javascript
// Line 6: Comment references "scripts/stageEngine.cjs" — no change needed
// Calls stageEngine.cjs which is updated above
```

### `scripts/native.mjs`
```javascript
// Line 6: Comment "(--engine: premation-engine + Dawn...)" → "(--engine: morynex-engine + Dawn..."
// Line 117: "premation-engine missed its heartbeat" → "morynex-engine missed its heartbeat"
// Any other references to premation-engine executable name
```

### Real-app Test Scripts (`scripts/realapp/*.cjs`)
```javascript
// All files: temp dir prefixes
// premation-default-* → morynex-default-*
// premation-transport-* → morynex-transport-*
// premation-rq-* → morynex-rq-*
// premation-d5-* → morynex-d5-*

// Window globals references:
// __premationEngineTransport → __morynexEngineTransport
// __premationEngineSurface → __morynexEngineSurface
// __premationProcessEngineState → __morynexProcessEngineState
// __premationViewportHud → __morynexViewportHud
// __premationAutomation → __morynexAutomation

// ENGINE path references:
// Line 24 (transportPreset.cjs): path.join(REPO, 'native', 'build', 'windows-clang-cl-engine', 'engine', 'premation-engine.exe')
// → path.join(REPO, 'native', 'build', 'windows-clang-cl-engine', 'engine', 'morynex-engine.exe')

// Line 19 (f1RenderQueue.cjs): same
// Line 19 (d5Viewport.cjs): same
```

### CI/CD Workflows (`.github/workflows/`)

#### `.github/workflows/ci.yml`
```yaml
# Repository references
repository: isroil01/premation  →  rynex/morynex
# Any job names, artifact names referencing "premation"
```

#### `.github/workflows/native.yml`
```yaml
# Repository references
repository: isroil01/premation  →  rynex/morynex
# Artifact names: premation-engine → morynex-engine
# SDK artifact: premation-sdk-<platform>.zip → mory-sdk-<platform>.zip
```

#### `.github/workflows/release.yml`
```yaml
# Repository references
repository: isroil01/premation  →  rynex/morynex
# Release asset names: Premation-Setup-*, Premation-macOS-*, Premation-Linux-*
# → Morynex-Setup-*, Morynex-macOS-*, Morynex-Linux-*
# Engine binary: premation-engine → morynex-engine
# mac.binaries: premation-engine, premation-host-bridge.node
# → morynex-engine, morynex-host-bridge.node
```

#### `.github/workflows/render-tests.yml`
```yaml
# Repository references
repository: isroil01/premation  →  rynex/morynex
```

### Vite Config (`vite.config.ts`)
```typescript
// Check for any define constants
define: {
  'import.meta.env.PREMATION_UI_PLATFORM': ...  →  add MORYNEX_UI_PLATFORM
  // Add both for transition period
}
```

## Verification
```bash
# Test build scripts
node scripts/stageEngine.cjs
node scripts/buildMcp.cjs
node scripts/pack-plugin.mjs --help
node scripts/create-morynex-plugin.mjs --help

# Test CI locally (act or push to test branch)
npm run typecheck
npm run lint

# Verify executable names in build output
ls build/engine/
```

## Notes
- Rename `create-premation-plugin.mjs` → `create-morynex-plugin.mjs` (or `create-mory-plugin.mjs`)
- Update `package.json` scripts if they reference the old name
- Real-app test scripts are dev-only — lower priority but needed for E2E tests
- CI workflows must be updated atomically with repo move