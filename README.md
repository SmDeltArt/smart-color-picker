# SmΔrt Color Picker

A self-contained, framework-free color picker widget — solid color, gradient editor, alpha, presets, palette save/load. Single HTML file, zero dependencies.

- **Version:** 1.1.0
- **Size:** ~1 file (HTML + inlined CSS/JS)
- **License:** see repository

## Quick start

### 1. As a standalone page

Just open `smart-color-picker.html` in any browser.

### 2. Embed in your site (iframe)

```html
<iframe
  src="https://cdn.jsdelivr.net/gh/<user>/<repo>@latest/release/smart-color-picker/smart-color-picker.html"
  style="border:0;width:420px;height:560px;background:transparent"
  allowtransparency="true"
></iframe>
```

Replace `<user>/<repo>` with your GitHub path. Pin a version with `@v1.1.0` for production.

### 3. CDN options

| CDN | URL pattern |
|---|---|
| jsDelivr | `https://cdn.jsdelivr.net/gh/<user>/<repo>@<tag>/release/smart-color-picker/smart-color-picker.html` |
| Statically | `https://cdn.statically.io/gh/<user>/<repo>/<tag>/release/smart-color-picker/smart-color-picker.html` |
| GitHub Pages | `https://<user>.github.io/<repo>/release/smart-color-picker/smart-color-picker.html` |
| Vercel | configure `release/smart-color-picker/` as a route |

## Public API

When loaded directly (not in an iframe) the widget exposes:

```js
window.SmartColorPicker.open({ theme: "light" });
window.SmartColorPicker.set("#ff8800");
const color = window.SmartColorPicker.get();
window.SmartColorPicker.setTheme("dark");
window.SmartColorPicker.onChange(payload => console.log(payload));
window.SmartColorPicker.close();
```

### Pre-load configuration

Set before the script runs:

```html
<script>
  window.SmartColorPickerConfig = {
    theme: "dark",      // "dark" | "light"
    autoOpen: true,
    openOptions: { color: "#6366f1" }
  };
</script>
```

### postMessage bridge (for iframe hosts)

```js
const frame = document.getElementById("colorPickerFrame");

// Open with theme
frame.contentWindow.postMessage(
  { type: "scp:open", theme: "light", color: "#ff0000" },
  "*"
);

// Listen for color changes
window.addEventListener("message", e => {
  if (e.data?.type === "scp:color") console.log(e.data);
});
```

Supported inbound message types: `scp:open`, `scp:close`, `scp:set`, `scp:setTheme`.
Outbound: `scp:color` (continuous), `scp:commit` (on release), `scp:closed`.

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

## Pinning versions

For production embeds, **always pin a tag**:

```
https://cdn.jsdelivr.net/gh/<user>/<repo>@v1.1.0/release/smart-color-picker/smart-color-picker.html
```

Using `@latest` or no tag will pull the most recent commit and may break.
