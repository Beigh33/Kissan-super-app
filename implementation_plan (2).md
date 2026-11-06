# Comprehensive PWA Manifest & Multi-Platform Installability Configuration

Configure a comprehensive, robust `manifest.json` along with iOS Safari meta configurations, PNG launcher shortcut icons, deep-linking URL handlers, and display mode settings to ensure full installability across Android (Chromium & TWA) and iOS devices.

## User Review & Critical Decisions

> [!IMPORTANT] Confirmed Preferences
> - **Orientation**: Configured to `any` (flexible auto-rotation between portrait and landscape for phone field diagnostics and tablet mandi dashboards).
> - **App Launcher Shortcuts**: Configured 2 home screen quick actions (AI Crop Doctor and Live Mandi Rates) pointing to PNG icon assets and deep-linked URL parameters (`/?tab=doctor` and `/?tab=bazaar`).
> - **Brand Colors**: Primary agricultural theme `#2E7D32` (Forest Green) with `#F4F8F4` splash background for contrast in outdoor daylight.

---

### 1. Overview & Core Concept

- **What It Delivers**:
  - A fully compliant Web App Manifest (`public/manifest.json`) fulfilling all W3C and Chromium PWA criteria (Lighthouse 100% PWA compliance score).
  - Multi-platform compatibility: Android Chrome/Edge (Add to Home screen & WebAPK), Android TWA (Bubblewrap), and iOS Safari (Add to Home Screen banner with status bar styling).
  - Deep-link support for app launcher shortcuts so tapping "AI Crop Doctor" directly opens the camera scanner tab, and "Mandi Rates" directly opens the APMC price comparison tab.
- **Target Audience**: Indian farmers, agronomists, and mandi traders who need one-tap home screen access from budget Android devices and iPhones with instant offline caching.

---

### 2. User Experience & Visual Design

- **Home Screen & Splash Presentation**:
  - **App Icon**: 192x192 and 512x512 high-resolution PNG icons with separate dedicated 512x512 maskable icon (`pwa-maskable-512x512.png`) respecting the 15% safe padding zone for Android adaptive squircles and circles.
  - **iOS Touch Icon**: 180x180 PNG (`apple-touch-icon.png`) for iOS Springboard with zero transparency clipping.
  - **Splash Screen**: `#2E7D32` brand green background with the Kisan icon smoothly transitioning into the app shell.
- **Orientation Behavior**:
  - Automatically adapts to portrait for one-handed handheld leaf scanning in fields and landscape for 5-day price trend charts and tractor comparison matrices.
- **Launcher Shortcuts Menu**:
  - Long-pressing the KisanApp icon on Android home screens reveals:
    1. **AI Crop Doctor** (`/?tab=doctor`) — Instant leaf photo capture.
    2. **Live Mandi Rates** (`/?tab=bazaar`) — Real-time APMC price check.

---

### 3. Key Technical Decisions & Trade-Offs

- **Decision 1: `orientation: "any"` vs `orientation: "portrait"`**
  - *Chosen Approach*: Set `"orientation": "any"` as selected by the user.
  - *Why*: Allows farmers to rotate their device when analyzing wide mandi comparison charts or machinery specs, while the responsive CSS ensures the mobile tab bar remains thumb-accessible.
- **Decision 2: PNG Icons for Shortcuts**
  - *Chosen Approach*: Point shortcut icons to `/pwa-192x192.png` instead of SVG.
  - *Why*: Android launcher shortcut rendering fails or defaults to generic gray icons when given SVGs; standard PNGs ensure crisp rendering across all Android versions.
- **Decision 3: Deep-Link Parameter Handling in `App.tsx`**
  - *Chosen Approach*: Read both `window.location.search` (`?tab=doctor`) and hash (`#doctor`) on app initialization and `popstate` events.
  - *Why*: Ensures the shortcut actually routes the user directly to the desired tab without flashing the home screen first.

---

### 4. Technical Architecture & Component Structure

```
┌─────────────────────────────────────────────────────────────┐
│                      Android / iOS OS                       │
│  - Launcher Icon (192px / 512px / 512px Maskable / 180px)   │
│  - App Shortcuts (Long-press: Crop Doctor & Mandi Bhav)     │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                    Web App Manifest                         │
│  - id: "/"                                                  │
│  - display: "standalone"                                    │
│  - display_override: ["window-controls-overlay", ...]       │
│  - orientation: "any"                                       │
│  - theme_color & background_color: "#2E7D32" / "#F4F8F4"    │
│  - shortcuts: [/?tab=doctor, /?tab=bazaar]                  │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                       index.html                            │
│  - <link rel="manifest" href="/manifest.json">              │
│  - <meta name="theme-color" content="#2E7D32">              │
│  - <meta name="apple-mobile-web-app-capable" content="yes"> │
│  - <link rel="apple-touch-icon" href="/apple-touch-icon..."> │
│  - Service Worker auto-registration (/sw.js)                │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                     React SPA (App.tsx)                     │
│  - Deep-link listener (?tab=doctor -> activeTab = 'doctor') │
│  - PWA Install Banner & Diagnostics Modal                   │
└─────────────────────────────────────────────────────────────┘
```

---

### 5. Implementation Steps

1. **Update `public/manifest.json`**:
   - Add `id: "/"`, `display_override: ["window-controls-overlay", "standalone", "minimal-ui"]`, `orientation: "any"`.
   - Update `shortcuts` array with proper `name`, `short_name`, `url: "/?tab=doctor"` and `url: "/?tab=bazaar"`, and PNG icon references.
   - Configure categories: `["business", "productivity", "utilities", "weather"]`.
   - Ensure `lang: "en-IN"` and `dir: "ltr"`.

2. **Enhance `index.html`**:
   - Add iOS Safari status bar style and touch icon metadata.
   - Ensure meta tags match manifest theme color and naming.

3. **Wire Deep-Linking in `src/App.tsx`**:
   - Add an effect reading URL search params (`?tab=doctor`, `?tab=bazaar`, `?tab=schemes`, `?tab=chaupal`) on mount and route directly to that screen.

4. **Verify & Test**:
   - Run `compile_applet` and `lint_applet` to ensure full build integrity.
   - Confirm manifest serves with `application/manifest+json` MIME type and all icon paths resolve cleanly.
