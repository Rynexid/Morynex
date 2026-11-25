# Phase 1 — Package & Config Identity

**Status:** ⏳ Planned  
**Risk:** Low  
**Scope:** Metadata, URLs, artifact names — **no code logic changes**

## Files to Modify

### `package.json`
| Field | Current | Target |
|-------|---------|--------|
| `name` | `motion-editor` | `@mory/morynex` |
| `repository.url` | `git+https://github.com/isroil01/motion-editor.git` | `git+https://github.com/rynex/morynex.git` |
| `homepage` | `https://github.com/isroil01/motion-editor#readme` | `https://github.com/rynex/morynex#readme` |
| `bugs.url` | `https://github.com/isroil01/motion-editor/issues` | `https://github.com/rynex/morynex/issues` |
| `author` | `Premation contributors` | `Rynex contributors` |
| `description` | "AI-native motion design... Premation" | "AI-native motion design... Morynex" |

### `electron-builder.yml`
| Field | Current | Target |
|-------|---------|--------|
| `appId` | `com.premation.app` | `com.morynex.app` |
| `productName` | `Premation` | `Morynex` |
| `copyright` | `© Premation contributors — GNU AGPL v3.0` | `© Rynex contributors — GNU AGPL v3.0` |
| `protocols.schemes` | `premation` | `morynex` |
| `fileAssociations.name` | `Premation Plugin` | `Morynex Plugin` |
| `fileAssociations.mimeType` | `application/x-premation-plugin` | `application/x-morynex-plugin` |
| `win.artifactName` | `Premation-Setup-Windows.${ext}` | `Morynex-Setup-Windows.${ext}` |
| `mac.artifactName` | `Premation-macOS-${arch}.${ext}` | `Morynex-macOS-${arch}.${ext}` |
| `linux.artifactName` | `Premation-Linux-${arch}.${ext}` | `Morynex-Linux-${arch}.${ext}` |
| `mac.binaries` | `premation-engine`, `premation-host-bridge.node` | `morynex-engine`, `morynex-host-bridge.node` |
| `publish.owner/repo` | `isroil01/premation` | `rynex/morynex` |

### `electron-builder.selfhosted.yml`
- Same `extraResources` filter updates for new executable names

### `index.html`
```html
<title>Premation</title>  →  <title>Morynex</title>
```

### `vite.config.ts`
- Update any `define` constants referencing `PREMATION_` → `MORYNEX_`

### `tsconfig.json`
- Verify path aliases still work (should be unaffected)

## Verification
```bash
npm run typecheck   # Zero errors
npm run lint        # Within warning budget
cat package.json | grep -E 'name|repository|author'
```

## Notes
- No code changes — pure metadata
- Safe to commit independently
- Electron-builder will pick up new names on next `pack:local`