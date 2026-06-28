---
title: "Widget theming"
description: "Theme argyros-swap with CSS variables — dark/light presets, custom accent, radius, fonts, and backgrounds."
llmDescription: "Theming guide for the Argyros argyros-swap Web Component. Built-in dark (default) and light themes; full CSS custom-property reference (--argyros-bg, --argyros-surface, --argyros-surface-hover, --argyros-border, --argyros-text, --argyros-text-secondary, --argyros-accent, --argyros-error, --argyros-radius). Custom branding examples (accent, rounded/sharp corners, custom background and font), Shadow DOM notes, and width control."
---

The widget uses CSS custom properties for all visual styling. Override them on the `<argyros-swap>` element to match your brand.

---

## Built-in Themes

### Dark (default)

```html
<argyros-swap theme="dark" api-key="..."></argyros-swap>
```

### Light

```html
<argyros-swap theme="light" api-key="..."></argyros-swap>
```

---

## CSS Variables

Override any variable on the host element:

```css
argyros-swap {
  --argyros-accent: #00ff88;
  --argyros-radius: 12px;
}
```

### Full Variable Reference

| Variable | Dark Default | Light Default | Description |
|----------|-------------|---------------|-------------|
| `--argyros-bg` | `#0c0c10` | `#ffffff` | Widget background |
| `--argyros-surface` | `#151519` | `#f5f5f7` | Card/panel background |
| `--argyros-surface-hover` | `#1e1e25` | `#ebebef` | Hover state background |
| `--argyros-border` | `#252530` | `#d4d4dc` | Border color |
| `--argyros-text` | `#e8e8ed` | `#1a1a2e` | Primary text |
| `--argyros-text-secondary` | `#7a7a8a` | `#6b6b7a` | Secondary text |
| `--argyros-text-dim` | `#555565` | `#9a9aaa` | Dimmed text (placeholders) |
| `--argyros-accent` | `#c8ff00` | `#1a1a2e` | Accent / CTA button color |
| `--argyros-accent-hover` | `#d4ff33` | `#2d2d45` | Accent hover state |
| `--argyros-error` | `#ff4d6a` | `#ff4d6a` | Error color |
| `--argyros-warning` | `#ffaa00` | `#ffaa00` | Warning color (price impact) |
| `--argyros-success` | `#00d68f` | `#00d68f` | Success color |
| `--argyros-radius` | `20px` | `20px` | Outer border radius |
| `--argyros-radius-sm` | `14px` | `14px` | Inner panel border radius |
| `--argyros-badge-bg` | `#0a0a0e` | `#e8e8ed` | Token badge background |
| `--argyros-font` | `'Inter', -apple-system, ...` | `'Inter', -apple-system, ...` | Font family |

---

## Custom Branding Examples

### Green accent

```css
argyros-swap {
  --argyros-accent: #00ff88;
  --argyros-accent-hover: #33ff9f;
}
```

### Rounded corners

```css
argyros-swap {
  --argyros-radius: 24px;
  --argyros-radius-sm: 18px;
}
```

### Sharp corners

```css
argyros-swap {
  --argyros-radius: 4px;
  --argyros-radius-sm: 2px;
}
```

### Custom background to match your app

```css
argyros-swap {
  --argyros-bg: #1a1b2e;
  --argyros-surface: #222340;
  --argyros-surface-hover: #2a2b50;
  --argyros-border: #3a3b55;
}
```

### Custom font

```css
argyros-swap {
  --argyros-font: "JetBrains Mono", monospace;
}
```

---

## Shadow DOM

The widget renders inside a shadow DOM, so your page styles don't leak in and widget styles don't leak out. CSS custom properties are the only way to customize the appearance from the outside. This is by design -- it ensures the widget looks correct regardless of your page's CSS.

---

## Width

The widget has `max-width: 460px` and `width: 100%`. To control its size, wrap it in a container:

```html
<div style="width: 400px; margin: 0 auto;">
  <argyros-swap api-key="..." theme="dark"></argyros-swap>
</div>
```
