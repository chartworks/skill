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

3. **Render** — write the chart to disk:

   ```bash
   npx chartworks render --spec spec.json --out chart.png
   ```

`spec.json` describes one chart or a composition of blocks. The shape comes from step 2 — don't guess.

For SVG output add `--format svg`. For all options: `npx chartworks --help`.
