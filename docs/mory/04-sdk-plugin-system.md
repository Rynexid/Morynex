# Phase 4 — SDK Headers & Plugin System

**Status:** ⏳ Planned  
**Risk:** HIGH  
**Scope:** SDK public headers, CMake install, CLI tool, plugin manifest compatibility

## Critical Constraint: Plugin ABI Compatibility
**DO NOT BREAK:**
- `PremationPluginInfo` / `PremationPluginExpressions` C symbols (exported by plugins)
- `premation-plugin.json` manifest filename (engine scans for this)
- `premation-plugin.sig` embedded signature format
- `PR_PLUGIN_INFO_SYMBOL` = `"PremationPluginInfo"` (host lookup string)

## Strategy
- Keep all ABI-compatible symbols/names
- Add new `mory_sdk` install paths alongside old
- New CMake function: `mory_add_plugin()` (alias `premation_add_plugin()` for compat)
- New CLI tool: `morynex-plugins` (keep `premation-plugins` as symlink/compat)
- New plugin IDs: `com.mory.*` or `com.rynex.*`

## Files to Modify

### SDK Headers Install Path
**Current:** `include/premation_sdk/pr_*.h`  
**New:** `include/mory_sdk/pr_*.h` (primary), keep `premation_sdk/` for compat

#### `native/sdk/CMakeLists.txt`
```cmake
# Line 26-31: Interface target rename
add_library(premation_sdk INTERFACE)  →  add_library(mory_sdk INTERFACE)
add_library(premation::sdk ALIAS premation_sdk)  →  add_library(mory::sdk ALIAS mory_sdk)
# KEEP old alias for compat
add_library(premation::sdk ALIAS mory_sdk)

# Line 27-29: Include directories
target_include_directories(mory_sdk INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include/mory_sdk>)  # Changed from include/premation_sdk

# Line 45-59: Install paths
install(DIRECTORY include/premation_sdk DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/mory_sdk COMPONENT sdk)
# KEEP old path for compat
install(DIRECTORY include/premation_sdk DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/premation_sdk COMPONENT sdk)

# Line 50: CMake config namespace
install(EXPORT MorySdkTargets NAMESPACE mory:: DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/MorySdk COMPONENT sdk)
# KEEP old config
install(FILES cmake/PremationSdkConfig.cmake DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/PremationSdk COMPONENT sdk)

# Line 60: Samples
install(DIRECTORY samples/ DESTINATION ${CMAKE_INSTALL_DATADIR}/mory-sdk/samples COMPONENT sdk)
install(DIRECTORY samples/ DESTINATION ${CMAKE_INSTALL_DATADIR}/premation-sdk/samples COMPONENT sdk)

# Line 62-67: fxkit
install(DIRECTORY fxkit/include/premation_fxkit DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/mory_fxkit COMPONENT sdk)
install(DIRECTORY fxkit/include/premation_fxkit DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/premation_fxkit COMPONENT sdk)
install(FILES fxkit/fxkit.mjs ... DESTINATION ${CMAKE_INSTALL_DATADIR}/mory-sdk/fxkit COMPONENT sdk)
install(FILES fxkit/fxkit.mjs ... DESTINATION ${CMAKE_INSTALL_DATADIR}/premation-sdk/fxkit COMPONENT sdk)

# Line 65-67: Scripts
install(FILES .../pack-plugin.mjs .../sign-plugin.mjs .../create-premation-plugin.mjs
  DESTINATION ${CMAKE_INSTALL_DATADIR}/mory-sdk COMPONENT sdk)
install(FILES ... DESTINATION ${CMAKE_INSTALL_DATADIR}/premation-sdk COMPONENT sdk)
```

### Header Files — Keep ABI Symbols
#### `native/sdk/include/premation_sdk/pr_effect.h`
```c
// KEEP EXACT — plugins export this symbol
#define PR_PLUGIN_INFO_SYMBOL "PremationPluginInfo"
PR_EXPORT const PrPluginInfo* PR_CALL PremationPluginInfo(void);

// ADD NEW — for new plugins
#define PR_PLUGIN_INFO_SYMBOL_MORY "MorynexPluginInfo"
PR_EXPORT const PrPluginInfo* PR_CALL MorynexPluginInfo(void);

// Same for expressions
#define PR_EXPR_TABLE_SYMBOL "PremationPluginExpressions"
PR_EXPORT const PrExprTable* PR_CALL PremationPluginExpressions(void);
PR_EXPORT const PrExprTable* PR_CALL MorynexPluginExpressions(void);
```

#### `native/sdk/include/premation_sdk/pr_types.h`
```c
// Line 30: Version comment mentions "Premation" — update to "Morynex"
// Line 39: PR_SDK_VERSION unchanged (ABI version)
```

#### `native/sdk/include/premation_sdk/pr_command.h`
```c
// Line 14: Comment "premation-engine" → "morynex-engine"
```

#### `native/sdk/include/premation_sdk/pr_io.h`
```c
// Line 14: Comment "premation-engine" → "morynex-engine"
```

#### `native/sdk/include/premation_sdk/pr_host.h`
```c
// Line 194: Comment "premation-plugins" → "morynex-plugins"
```

#### `native/sdk/include/premation_sdk/pr_scene.h`
```c
// Line 106: WGSL function `premation_material` — KEEP (shader ABI)
// This is a WGSL function name used in shaders, not C ABI
```

### CMake Helper Functions
#### `native/sdk/CMakeLists.txt` — `premation_sample_plugin()` → `mory_sample_plugin()`
```cmake
# Line 71: Function rename
function(premation_sample_plugin name)  →  function(mory_sample_plugin name)
# KEEP old function as alias
function(premation_sample_plugin name)
  mory_sample_plugin(${name})
endfunction()

# Line 93: Manifest copy — KEEP filename `premation-plugin.json`
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/samples/${name}/premation-plugin.json ...)
```

#### `native/engine/CMakeLists.txt` — `premation_embed_wgsl()` → `mory_embed_wgsl()`
```cmake
# Line 86: Function rename
premation_embed_wgsl(${ENGINE_WGSL_GENERATED})  →  mory_embed_wgsl(${ENGINE_WGSL_GENERATED})
# KEEP old as alias
function(premation_embed_wgsl OUT_DIR)
  mory_embed_wgsl(${OUT_DIR})
endfunction()

# Line 97-100: Windows defines
target_compile_definitions(engine_gpu PUBLIC PREMATION_SHARED_TEXTURE=1)
# ADD new define
target_compile_definitions(mory_gpu PUBLIC MORYNEX_SHARED_TEXTURE=1)
```

### CLI Tool
**Current:** `bin/premation-plugins`  
**New:** `bin/morynex-plugins` (primary), keep `premation-plugins` as wrapper

#### `native/sdk/CMakeLists.txt` — Install renamed
```cmake
# The tool is built in engine/CMakeLists.txt, installed here
# Rename executable target in engine/CMakeLists.txt
add_executable(morynex-plugins ...)  # instead of premation-plugins
```

### Sample Plugin Manifests (KEEP for compat)
All `native/sdk/samples/*/premation-plugin.json` — **DO NOT RENAME**
- Plugin IDs: `com.premation.samples.*` — **KEEP** (existing plugins)
- New samples: use `com.mory.samples.*`

### Build Scripts
#### `scripts/pack-plugin.mjs`
```javascript
// Line 38: Manifest filename — KEEP
export const MANIFEST = 'premation-plugin.json';

// Line 11-12: Bundle structure — KEEP
// Line 40: Embedded sig — KEEP
export const EMBEDDED_SIGNATURE = 'premation-plugin.sig';

// SDK paths update
// Line 99: share/premation-sdk/ → share/mory-sdk/
```

#### `scripts/sign-plugin.mjs`
- Same manifest/sig handling — no changes needed

#### `scripts/create-premation-plugin.mjs`
```javascript
// Line 2: Rename to create-morynex-plugin.mjs (or create-mory-plugin.mjs)
// Line 72: SDK path reference
// Line 99: SDK unzip path
// Line 113-114: sign/pack plugin paths
// Line 137-139: CI references to Premation SDK
// Line 174: gh release download from isroil01/premation → rynex/morynex
// Line 209-210: Linux SDK download
// Line 217: pack-plugin.mjs path
// Line 227: sign-plugin.mjs publish
// Line 249: find_package(PremationSdk) → find_package(MorySdk)
// Line 250: premation_add_plugin() → mory_add_plugin()
// Line 259: #include <premation_sdk/premation_sdk.h> → #include <mory_sdk/premation_sdk.h>
```

### SDK Release Artifacts
| Current | New |
|---------|-----|
| `premation-sdk-<platform>.zip` | `mory-sdk-<platform>.zip` |
| Contains `include/premation_sdk/` | Contains `include/mory_sdk/` (+ compat `premation_sdk/`) |
| `lib/cmake/PremationSdk/` | `lib/cmake/MorySdk/` (+ compat) |
| `bin/premation-plugins` | `bin/morynex-plugins` (+ compat symlink) |
| `share/premation-sdk/` | `share/mory-sdk/` (+ compat) |

## Verification
```bash
# Build SDK
cmake --preset linux-clang -DMOTION_BUILD_ENGINE=ON
cmake --build --preset linux-clang --target mory_sdk
cmake --install --preset linux-clang --component sdk --prefix /tmp/mory-sdk

# Verify install structure
ls /tmp/mory-sdk/include/mory_sdk/
ls /tmp/mory-sdk/lib/cmake/MorySdk/
ls /tmp/mory-sdk/bin/

# Test sample plugin build against new SDK
cd /tmp/mory-sdk/share/mory-sdk/samples/ripple
cmake -B build -DCMAKE_PREFIX_PATH=/tmp/mory-sdk
cmake --build build
# Verify plugin loads in engine

# Test existing plugin (com.premation.samples.ripple) still works
```

## Notes
- This phase coordinates tightly with Phase 3 (C++ namespace)
- Dual install paths add ~2x install size — acceptable for SDK
- Backward compat maintained for 2+ major versions
- New plugin authors use `find_package(MorySdk)` + `mory_add_plugin()`