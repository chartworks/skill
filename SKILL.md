---
name: chartworks
description: Use when the user asks for a chart, graph, or visualization. Renders Chartworks charts, tables, callouts, and compositions as PNG or SVG via the Chartworks CLI.
---

# Using Chartworks

Chartworks renders charts from a JSON spec. The agent flow has three stages — fetch the manual once, fetch a guide for each chart type you need, then render.

1. **Manual** — orientation, once per session:

   ```bash
   npx chartworks manual
   ```

2. **Guide** — per-chart schema, examples, and gotchas. Pass the chart or block types you plan to render:

   ```bash
   npx chartworks guide column combo
   ```

3. **Render** — write the chart to disk. Pass the spec via file or stdin:

   ```bash
   npx chartworks render --spec spec.json --out chart.png

   # or pipe inline without touching the filesystem:
   echo '{"type":"bar", ...}' | npx chartworks render --out chart.png
   ```

   Optional — validate a spec first (no render, no quota) to catch shape errors before rendering:

   ```bash
   npx chartworks validate --spec spec.json
   ```

The spec describes one chart or a composition of blocks. The shape comes from step 2 — don't guess. For small multiples, fetch the relevant chart guide and use that chart's `facet` channel when the guide/schema supports it. Usually omit `facet.columns`; Chartworks picks an aspect-aware grid by default.

For exact output dimensions (fixed slots), set `dimensions: { "width": 960, "height": 540 }` in the spec — authoritative for SVG and the PNG logical canvas, tables included. `aspect` plus one of `width`/`height` derives the other; never send all three. PNG pixels = canvas × `--scale` (default 2, range 1-4): 960x540 at `--scale 1` is exactly 960x540, at `--scale 2` exactly 1920x1080. Scale changes resolution only, never layout; SVG ignores it. Oversized requests fail with the failing limit named (canvas ≤ 4096px/side; PNG output ≤ 8192px/side and ≤ 8,000,000px² area).

**Sizing a chart that gets placed in a document or slide.** The render command prints one JSON line with the output `path` and a `render` object containing the logical `canvas`, outer `inset`, and resolved `labelPx`. Text inside the chart is sized from the canvas. Compute its placed size rather than guessing:

```
displayed label size (pt) = (label_px ÷ canvas_width_px) × 72 × placed_width_inches
```

If the result does not match the host text, set `dimensions.typeScale` from 0.6 through 2 and render again. The multiplier applies after the canvas-derived size clamp. It scales chart text and the outer inset; canvas size, output pixels, and placement stay unchanged. Titles, subtitles, block titles, footnotes, and attribution stop scaling above 1.25; chart content continues to 2. Read the new `labelPx` and `inset` from the result. For PNG pixel cropping, multiply the logical inset by `--scale`.

Axis labels are about 0.025 × canvas width at 400x225, falling to about 0.014 at 960x540 and above — the base type size clamps, so a bigger canvas makes type _smaller_ relative to the figure. Two anchors:

- **Report figure, ~6in text column, 11pt body text** — use `{ "width": 400, "height": 225 }` with `--scale 4`. Labels land near 10.8pt, output is 1600x900px at ~267 DPI. The same figure from a 1600x900 canvas gives roughly 6pt labels and 4pt footnotes, which is unreadable next to body text.
- **Slide, 10-13in content box** — use `{ "width": 600, "height": 400 }` or larger. Labels land 12-16pt against typical 18-24pt slide text.

Raise `--scale` for print density; it never changes layout or type size. Use one canvas size across every figure in a set so type stays consistent between them.

To browse themes: `npx chartworks themes`.

To list workspace assets (logos and images you've uploaded): `npx chartworks assets list`. Reference one in a spec as `asset:<name>` — the name must match exactly, so list first rather than guessing.

To upload one: `npx chartworks assets put ./acme-logo.png` (PNG, JPEG, or WebP; the name defaults to the filename). Uploading over an existing name needs `--force`. This needs a key with `asset:write`, which `npx chartworks auth login` requests by default. Delete with `npx chartworks assets rm <name>`.

For SVG output add `--format svg`. For all options: `npx chartworks --help`.
