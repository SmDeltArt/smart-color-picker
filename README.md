# SmΔrt Color Picker

A self-contained, framework-free color picker widget — solid color, gradient editor, alpha, presets, palette save/load. Single HTML file, zero dependencies.

- **Version:** 1.2.0
- **Size:** ~1 file (HTML + inlined CSS/JS)
- **License:** see repository

## Quick start

### 1. As a standalone page

Just open `smart-color-picker.html` in any browser.

### 2. Embed in your site (iframe)

```html
<iframe
  src="https://smdeltart.github.io/smart-color-picker/smart-color-picker.html"
  style="border:0;width:420px;height:560px;background:transparent"
  allowtransparency="true"
></iframe>
```

GitHub Pages serves the file with `Content-Type: text/html`, so it renders
and iframes correctly. Pin a tag in production by using a tagged Statically
URL (see below) instead of Pages, which always tracks `main`.

### 3. Hosting / CDN options

| Source                | URL pattern                                                                               | Renders HTML? |
| --------------------- | ----------------------------------------------------------------------------------------- | ------------- |
| **GitHub Pages**      | `https://smdeltart.github.io/smart-color-picker/smart-color-picker.html`                  | ✅ yes (recommended) |
| **Statically** (pinned) | `https://cdn.statically.io/gh/SmDeltArt/smart-color-picker/<tag>/smart-color-picker.html` | ✅ yes |
| jsDelivr (asset only) | `https://cdn.jsdelivr.net/gh/SmDeltArt/smart-color-picker@<tag>/smart-color-picker.html`  | ❌ served as `text/plain` — fine for `fetch()`, **not** for direct viewing or iframes |
| Vercel                | deploy this repo and route `/smart-color-picker.html`                                     | ✅ yes |

> **Why not jsDelivr for iframes?** jsDelivr deliberately serves HTML files
> from `/gh/` paths as `text/plain` to discourage abuse. Use it for
> `fetch()`-style asset loading; use **GitHub Pages** or **Statically** for
> direct page / iframe URLs.

## Public API

When loaded directly (not in an iframe) the widget exposes:

```js
window.SmartColorPicker.open({ theme: "light" });
window.SmartColorPicker.set("#ff8800");
const color = window.SmartColorPicker.get();
window.SmartColorPicker.setTheme("dark");
window.SmartColorPicker.onChange((payload) => console.log(payload));
window.SmartColorPicker.close();
```

### Pre-load configuration

Set before the script runs:

```html
<script>
  window.SmartColorPickerConfig = {
    theme: "dark", // "dark" | "light"
    autoOpen: true,
    openOptions: { color: "#6366f1" },
  };
</script>
```

### postMessage bridge (for iframe hosts)

All messages use a `{ type: "smart-widget", action, data }` envelope. The widget only accepts messages from same-origin, `localhost`, `127.0.0.1`, `https://smdeltart.com`, or `https://smdeltart-*.vercel.app`.

```js
const frame = document.getElementById("colorPickerFrame");

// Open with theme + initial color
frame.contentWindow.postMessage(
  {
    type: "smart-widget",
    action: "scp:open",
    data: { hex: "#ff0000", theme: "light" },
  },
  "*",
);

// Listen for color changes
window.addEventListener("message", (e) => {
  const msg = e.data;
  if (!msg || msg.type !== "smart-widget") return;
  if (msg.action === "scp:color") console.log(msg.data);
});
```

**Inbound actions** (host → widget):

- `scp:open` · data: `{ hex?, alpha?, theme? }`
- `scp:close`
- `scp:set` · data: `{ hex?, alpha? }`
- `scp:setTheme` · data: `{ theme: "dark" | "light", palette?: PaletteOverrides }`
- `scp:get` (triggers a `scp:color` emit)
- `scp:selectStop` · data: `{ index?: number }` or `{ pos?: number }` (0–100, picks nearest stop) — only meaningful when the gradient editor is open
- `scp:setGradient` · data: `{ open?: boolean, type?: "linear" | "radial", angle?: 0..360 }` — toggle the gradient editor section, change geometry from the host UI

`PaletteOverrides` is an optional object that lets the host re-skin the picker to match its own surfaces. All keys are optional; unknown keys are ignored. Any CSS color value works (`#hex`, `rgb()`, `hsl()`, etc.).

```js
{
  (bg,
    surface,
    panel,
    input, // backgrounds (most → least prominent)
    text,
    muted,
    faint, // text tiers
    border, // dividers
    accent,
    primary,
    accent2); // brand / focus / hover colors
}
```

You can also pass `palette` inside `scp:open` data to apply at open time. Reset by sending `scp:setTheme` with an empty `palette: {}` (clears overrides; built-in dark/light tokens take over).

**Outbound actions** (widget → host):

- `scp:color` · live updates while picking — data: `{ hex, rgba, hsla, gradient, gradientActive }`
- `scp:validate` · color confirmed (swatch click, add to palette)
- `scp:close` · panel dismissed
- `scp:size` · panel bbox `{ width, height }` so host can shrink iframe to fit

## Features

- HSV color wheel + value/alpha track
- Linear & radial gradients with stop editor
- 8-snap angle dial (cardinals + diagonals)
- Harmony presets (complement, triad, tetrad, analogous)
- Save/load palettes (localStorage + JSON file)
- Eyedropper (where supported by browser)
- Drag-anywhere panel with hard screen bounds
- Light/dark theme
- Touch + mouse + keyboard
- **Mobile-first**: long-press a swatch to remove (mobile equivalent of right-click)
- **Tutorial tips**: hover-to-explain mode, toggled from the help popover
- **Default color set**: ships with `smart-colorset.json` auto-loaded on first run
- **i18n-ready**: shared CSV (`smart-color-picker.i18n.csv`) — register a translator via `SmartColorPicker.setI18n(fn)`

## Changelog

### v1.2.0

- ✨ Tutorial tips overlay (toggle from help popover) with `data-tut` /
  `data-tut-key` on every key control
- ✨ Default color set: bundled `smart-colorset.json` auto-loads when
  localStorage is empty
- ✨ i18n: companion `smart-color-picker.i18n.csv` (EN + FR seeded,
  9 more locales available); `SmartColorPicker.setI18n(fn)` to wire it
- 📱 Mobile: long-press to remove a saved swatch (550 ms hold, suppresses
  follow-up click, optional haptic)
- 🎨 Theme-aware help popover (Δ + accents follow detected palette)
- 🐛 Demo: rgba object → string conversion on solid fallback

### v1.1.0

- Initial public release: standalone SPA, gradient editor, palette
  save/load, theme palette overrides

## Pinning versions

For production embeds, **always pin a tag**:

```
https://cdn.statically.io/gh/SmDeltArt/smart-color-picker/v1.2.0/smart-color-picker.html
```

(Statically returns `text/html` and supports tag pinning — preferred over
jsDelivr for direct page / iframe usage.)

Using `@latest` or no tag will pull the most recent commit and may break.
