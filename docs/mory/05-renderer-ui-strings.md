# Phase 5 — Renderer/UI Strings & Constants

**Status:** ⏳ Planned  
**Risk:** Medium  
**Scope:** All user-facing strings, i18n, window globals, deep link handlers, page titles

## Files to Modify

### i18n Locales (`src/core/i18n/locales/*.json`)
**Files:** `en.json`, `es.json`, `zh-CN.json`, `ja.json`, `ko.json`, `pt-BR.json`, `fr.json`, `de.json`, `ru.json`, `zh-TW.json`, `it.json`, `tr.json`, `pl.json`, `nl.json`, `vi.json`, `id.json`, `th.json`, `ar.json`, `hi.json`

**Patterns to Replace:**
| Current | Target |
|---------|--------|
| `"Premation"` | `"Morynex"` |
| `"Premation Cloud"` | `"Morynex Cloud"` |
| `"Premation Store"` | `"Morynex Store"` / `"Morynex Plugin Store"` |
| `"Premation account"` | `"Morynex account"` |
| `"Premation app"` | `"Morynex app"` |
| `"Premation 插件"` (zh-CN) | `"Morynex 插件"` |
| `"Premation 云"` (zh-CN) | `"Morynex 云"` |
| `"Premation プラグイン"` (ja) | `"Morynex プラグイン"` |
| `"Premation クラウド"` (ja) | `"Morynex クラウド"` |

**Approach:** Scripted replacement with manual review
```bash
# Example for en.json
sed -i 's/Premation Cloud/Morynex Cloud/g' src/core/i18n/locales/en.json
sed -i 's/Premation Store/Morynex Store/g' src/core/i18n/locales/en.json
sed -i 's/Premation account/Morynex account/g' src/core/i18n/locales/en.json
sed -i 's/Premation app/Morynex app/g' src/core/i18n/locales/en.json
sed -i 's/\bPremation\b/Morynex/g' src/core/i18n/locales/en.json
```

**Special cases needing manual review:**
- `"Premation contributors"` → `"Rynex contributors"` (copyright)
- `"Premation native plugin"` → `"Morynex native plugin"` (technical)
- `"Premation plugin package"` → `"Morynex plugin package"` (.pplugin)

### Page Titles (`src/pages/PopoutRoute.tsx`)
```typescript
// Lines 31-39
viewport: t('pages.popout.titleViewport', 'Viewport Preview — Premation')
→ viewport: t('pages.popout.titleViewport', 'Viewport Preview — Morynex')

timeline: t('pages.popout.titleTimeline', 'Timeline — Premation')
→ timeline: t('pages.popout.titleTimeline', 'Timeline — Morynex')

presentation: t('pages.popout.titlePresentation', 'Presentation Mode — Premation')
→ presentation: t('pages.popout.titlePresentation', 'Presentation Mode — Morynex')

panel: t('pages.popout.titlePanel', '{name} — Premation')
→ panel: t('pages.popout.titlePanel', '{name} — Morynex')

detached: t('pages.popout.titleDetached', 'Detached Window')  // No change
```

### Index.html
```html
<!-- Line 17 -->
<title>Premation</title>  →  <title>Morynex</title>
```

### Window Globals (`src/core/engine/engineOwnedSession.tsx`)
```typescript
// Line 49: Recovery key
const RECOVERY_KEY = 'premation.engineRecovery.v1';
→ const RECOVERY_KEY = 'morynex.engineRecovery.v1';

// Lines 210-211: Debug globals
(window as unknown as { __premationEngineTransport?: ... }).__premationEngineTransport
→ (window as unknown as { __morynexEngineTransport?: ... }).__morynexEngineTransport

// Also update references in realapp test scripts (Phase 6)
```

### Deep Link Handlers (`src/routes/AppRouter.tsx`)
```typescript
// Lines 70, 72, 75: premation:// scheme references
// premation://connections → morynex://connections
// premation://billing → morynex://billing
// premation://oauth → morynex://oauth

// Update routeDeepLink in electron/deepLink.ts (Phase 2)
// Update handler logic here to match new scheme
```

### Environment Variable Constants (`src/core/config/uiPlatform.ts`)
```typescript
// Line 12-13: Comment references PREMATION_UI_PLATFORM
// Line 73: /** `import.meta.env.PREMATION_UI_PLATFORM`. */

// Strategy: Support both
const platform = import.meta.env.MORYNEX_UI_PLATFORM ?? import.meta.env.PREMATION_UI_PLATFORM;

// Update Vite define in vite.config.ts (Phase 1)
```

### About Menu (`src/providers/Providers.tsx`)
```typescript
// Line 2644: label: 'About Premation'
label: 'About Morynex'
```

### Welcome Page (`src/pages/WelcomePage.tsx`)
```typescript
// Line 122: "How did you hear about Premation?"
// Uses i18n key auth.welcome.question — covered by i18n replacement
```

### Auth Page (`src/pages/AuthPage.tsx`)
```typescript
// Line 210: "If {slot} has a Premation account..."
// Uses i18n key auth.forgot.sentSubtitle — covered by i18n replacement
```

### Dashboard Pages (all use i18n keys — covered by i18n replacement)
- `DashboardPage.tsx`
- `DashboardTemplates.tsx`
- `DashboardSettings.tsx`
- `DashboardConnections.tsx`

### Plugin Detail Page (`src/layout/Plugins/PluginDetailPage.tsx`)
```typescript
// Line 7: Comment "Publisher ✓ · 12,400 installs · Premation Cloud"
// Line 322: i18n key plugins.page.notInStore — covered
```

### Registry Notice (`src/layout/Plugins/RegistryPluginsNotice.tsx`)
```typescript
// Line 4: "Premation 0.9 removed..." — historical, keep as-is or update to "Morynex (formerly Premation) 0.9"
```

## Verification
```bash
# Check no "Premation" strings remain in UI code (except historical comments)
grep -r "Premation" src/ --include="*.ts" --include="*.tsx" | grep -v ".test." | grep -v "historical" | grep -v "// "

# Run typecheck
npm run typecheck

# Quick visual check: npm run dev:local
```

## Notes
- i18n replacement is the bulk of this phase — script it
- Keep historical references in comments (e.g., "Premation 0.9 removed...")
- Deep link scheme change coordinated with Phase 2 (Electron main)
- Window globals change affects realapp test scripts (Phase 6)