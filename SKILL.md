---
name: chartworks
description: Use when the user asks for a chart, graph, or visualization. Renders charts (bar, line, pie, mekko, callouts, tables, compositions) as PNG or SVG via the Chartworks CLI.
---

# Using Chartworks

Chartworks renders charts from a JSON spec. The agent flow has three stages — fetch the manual once, fetch a guide for each chart type you need, then render.

1. **Manual** — orientation, once per session:

   ```bash
   npx chartworks manual
   ```

2. **Guide** — per-chart schema, examples, and gotchas. Pass the chart types you plan to render:

   ```bash
   npx chartworks guide bar line
   ```

3. **Render** — write the chart to disk. Pass the spec via file or stdin:

   ```bash
   npx chartworks render --spec spec.json --out chart.png

   # or pipe inline without touching the filesystem:
   echo '{"type":"bar", ...}' | npx chartworks render --out chart.png
   ```

The spec describes one chart or a composition of blocks. The shape comes from step 2 — don't guess.

For SVG output add `--format svg`. For all options: `npx chartworks --help`.
