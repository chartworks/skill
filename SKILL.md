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

For exact output dimensions (report pipelines, fixed slots), set `dimensions: { "width": 1600, "height": 900 }` in the spec — authoritative for SVG and the PNG logical canvas, tables included. `aspect` plus one of `width`/`height` derives the other; never send all three. PNG pixels = canvas × `--scale` (default 2, range 1-4): 1600x900 at `--scale 1` is exactly 1600x900, at `--scale 2` exactly 3200x1800. Scale changes resolution only, never layout; SVG ignores it. Oversized requests fail with the failing limit named (canvas ≤ 4096px/side; PNG output ≤ 8192px/side and ≤ 8,000,000px² area).

To browse themes: `npx chartworks themes`.

To list workspace assets (logos and images you've uploaded): `npx chartworks assets list`. Reference one in a spec as `asset:<name>` — the name must match exactly, so list first rather than guessing.

For SVG output add `--format svg`. For all options: `npx chartworks --help`.
