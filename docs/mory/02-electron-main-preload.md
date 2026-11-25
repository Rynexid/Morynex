# Phase 2 — Electron Main & Preload

**Status:** ⏳ Planned  
**Risk:** Medium  
**Scope:** Deep link scheme, environment variables, process names, menu, updater

## Files to Modify

### `electron/main.ts`

#### Deep Link Scheme
```typescript
// Line 125
const DEEP_LINK_SCHEME = 'premation'  →  const DEEP_LINK_SCHEME = 'morynex'

// Lines 128-137: registerProtocolClient()
app.setAsDefaultProtocolClient(OAUTH_SCHEME, ...)
// Also update argv.find(a => a.startsWith(`${OAUTH_SCHEME}://`))

// Line 187: Uninstall flag
if (process.argv.includes('--premation-mcp-uninstall'))
→ if (process.argv.includes('--morynex-mcp-uninstall'))
```

#### Window Menu Label
```typescript
// nativeMenu.ts → label: 'Premation' (test expects 'Premation' at line 91)
label: 'Premation'  →  label: 'Morynex'
```

#### Updater Message
```typescript
// Line 71: "Updated to Premation X"
reason: `Updated to Premation ${version}`
→ reason: `Updated to Morynex ${version}`
```

#### Engine Resolution (delegated to engineSupervisor, but main calls it)
- No direct changes needed here — Phase 3 covers engine executable names

### `electron/preload.ts`

#### IPC Channels (no changes needed — channel names are internal)
- `engine:pixels`, `engine:pixelsRelease` — keep as-is
- `deepLink:connections`, `deepLink:billing` — keep as-is

#### Exposed API (`window.motionEditor`)
- No direct `premation` references in typed surface
- Deep link handlers use scheme from main

### `electron/deepLink.ts`
```typescript
// Route parsing for premation:// → morynex://
export const DEEP_LINK_SCHEME = 'premation'  →  'morynex'

// All route handlers update accordingly
```

### `electron/cliArgs.ts`
```typescript
// Line: --premation-mcp-uninstall
'--premation-mcp-uninstall'  →  '--morynex-mcp-uninstall'
```

### Environment Variables (Passed to Engine Process)
**Strategy:** Support BOTH old and new prefixes during transition

| Old Variable | New Variable | Location |
|--------------|--------------|----------|
| `PREMATION_DEV_URL` | `MORYNEX_DEV_URL` | `main.ts:93` |
| `PREMATION_UI_PLATFORM` | `MORYNEX_UI_PLATFORM` | `main.ts:864`, `uiPlatform.ts` |
| `PREMATION_PLUGIN_PATH` | `MORYNEX_PLUGIN_PATH` | `main.ts:1346` |
| `PREMATION_LIBRARY_DEV_DIR` | `MORYNEX_LIBRARY_DEV_DIR` | `main.ts:1544` |
| `PREMATION_ENGINE_PATH` | `MORYNEX_ENGINE_PATH` | `engineSupervisor.ts:587` |
| `PREMATION_VIEWPORT_ROUTE` | `MORYNEX_VIEWPORT_ROUTE` | `engineHost.ts:95` |
| `PREMATION_FONTS_MANIFEST` | `MORYNEX_FONTS_MANIFEST` | `engineHost.ts:111` |
| `PREMATION_ENGINE_ARGS` | `MORYNEX_ENGINE_ARGS` | `engineHost.ts:679` |
| `PREMATION_FFMPEG` | `MORYNEX_FFMPEG` | `engineHost.ts:866` |
| `PREMATION_SESSION_FOOTAGE` | `MORYNEX_SESSION_FOOTAGE` | `engineHost.ts:869` |
| `PREMATION_SAM_USER_DIR` | `MORYNEX_SAM_USER_DIR` | `engineHost.ts:871` |
| `PREMATION_SAM_GPU_DIR` | `MORYNEX_SAM_GPU_DIR` | `engineHost.ts:875` |
| `PREMATION_FACE_USER_DIR` | `MORYNEX_FACE_USER_DIR` | `engineHost.ts:876` |
| `PREMATION_DEPTH_USER_DIR` | `MORYNEX_DEPTH_USER_DIR` | `engineHost.ts:877` |
| `PREMATION_HAND_USER_DIR` | `MORYNEX_HAND_USER_DIR` | `engineHost.ts:877` |
| `PREMATION_SAM2_USER_DIR` | `MORYNEX_SAM2_USER_DIR` | `engineHost.ts:882` |
| `PREMATION_VITMATTE_USER_DIR` | `MORYNEX_VITMATTE_USER_DIR` | `engineHost.ts:883` |

**Implementation:** Read new first, fall back to old
```typescript
const value = process.env.MORYNEX_FOO ?? process.env.PREMATION_FOO
```

## Verification
```bash
npm run typecheck
npm run test -- electron/main.test.ts
npm run test -- electron/preload.test.ts
# Test deep link handling manually
```

## Notes
- Deep link scheme change affects OAuth flow — coordinate with backend (motion-back)
- Uninstall flag change requires NSIS installer script update (`build/installer.nsh`)
- Keep backward compat for env vars for 2 major versions