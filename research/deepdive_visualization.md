# Deep-Dive: ALPA Visualization Methods

**Date:** 2026-04-02
**Source:** Opus deep-dive agent
**Focus:** How Architecture Studio handles visualization in a text-based terminal

---

## Core Insight: Self-Contained HTML as the Universal Escape Hatch

One file, no build step, no server, works offline after first load. Every visual output is a single `.html` loaded in the default browser.

## Three Tiers of Visual Output

### Tier 1: Markdown (Structured Text)
Skills like `/environmental-analysis`, `/demographics-analysis`, `/occupancy-calculator` produce **markdown only**. Temperature data = table. Flood zones = text description. Wind = prose. **No charts, no maps, no spatial visualizations.** This is a deliberate scope boundary.

### Tier 2: Self-Contained HTML (Presentations)
`/slide-deck-generator` and `/color-palette-generator` produce single HTML files with embedded CSS/JS. **No external dependencies except system fonts.** 320 lines of CSS + JS navigation baked into SKILL.md template.

### Tier 3: CDN-Loaded Libraries (Interactive 3D)
`/zoning-envelope` loads Three.js v0.170.0 from `cdn.jsdelivr.net` via ES module import map. Most complex visual output — still a single HTML file.

## The 3D Zoning Envelope Viewer

**Technology:** Three.js via CDN import map. No local dependencies.

**Architecture:** SKILL.md specifies a geometry pipeline Claude implements from scratch each time: `signedArea()`, `insetPolygon()`, `triangulate()` (ear-clipping), `extrudePolygon()`. Self-correction: after computing inset, compares area and negates offset if result is larger (handles CW/CCW polygon windings).

**Data pipeline:** `/zoning-analysis-nyc` fetches lot polygon from MapPLUTO ArcGIS, converts WGS84→local feet, embeds JSON in report. `/zoning-envelope` reads that JSON and renders. Clean separation: analysis produces data, visualization consumes it.

**Design system:** Specific colors + opacities per element (ground 0.5, setback red 0.2, volumes blue 0.08-0.10, height cap amber 0.10-0.15). All materials: `transparent: true`, `depthWrite: false`, `side: DoubleSide` — ghosted architectural drawing aesthetic.

**Multi-scenario:** Toggle buttons show/hide Three.js Groups for different development options.

## The Slide Deck Generator

823-line SKILL.md containing **complete production CSS and JS** inline.

**22 slide types** organized by content pattern: Title, Heading+Body/List/Stats, Statement, Data Table, Bar Chart, Timeline, Two Column, Comparison, Image variants (full bleed, split 2/3/4/6), Callout.

**Design:** Left-aligned, massive whitespace (content ≤60% of slide). Monochrome + single accent (#E8B517). Helvetica Neue with `clamp()` for responsive sizing.

**Bar chart = pure CSS** — horizontal bars with `div.bar-fill` using inline width percentages. No charting library. **Only "data visualization" component in the system.**

## The Surprising Absence: No Analytical Data Visualization

**ALPA does NOT visualize analytical data.** Environmental analysis outputs temperature as a table. No charts, no maps, no wind roses, no solar path diagrams. The rule: "Use HTML only for interactive or visual outputs." Analytical skills are classified as text reports.

## The Terminal-to-Browser Bridge

Trivially simple: Claude writes HTML file with `Write` tool → runs `open <path>` (macOS) via Bash. No HTTP server, no WebSocket, no hot-reload. One-shot generation.

## Image Processing (Product Images)

Pipeline: `curl -L` (not WebFetch) → Python Pillow (LANCZOS, max 2000px, RGBA) → `rembg` with ONNX (u2net, ~170MB). Pure filesystem: `originals/` → `resized/` → `nobg/`.

---

## Adaptation for Infrared Environmental Visualization

### What ALPA's Pattern Solves

Self-contained HTML with CDN libraries works perfectly for:
- One-shot visualization generation
- No dependency installation required
- Works offline after first load
- Professional design via prescriptive SKILL.md templates

### What ALPA Does NOT Solve (Where Infrared Must Innovate)

| Gap | ALPA | Infrared Needs |
|-----|------|---------------|
| Geospatial maps | Zero map visualization | Mapbox GL JS / Deck.gl from CDN |
| Continuous fields | Only discrete geometry | Canvas raster rendering or WebGL shaders |
| Time-series animation | Static 3D viewer | Time slider controlling render loop |
| Color ramps | Manual hex values | Scientific color maps (viridis, RdYlBu) |
| Large datasets | Small JSON inline | Tile-based or chunked data loading |

### Proposed Visualization Skills

**1. Flood Inundation Map**
Self-contained HTML + Mapbox GL JS from CDN. GeoJSON building footprints with `fill-extrusion-height` for depth. Color ramp from SKILL.md design system. Info panel showing scenario parameters.

**2. UHI Heat Map**
Deck.gl HeatmapLayer or Mapbox GL JS `heatmap` layer. Satellite basemap + temperature overlay. Legend with temperature scale.

**3. Wind Comfort Diagram**
D3.js wind rose as SVG embedded in HTML. For spatial: Deck.gl ScreenGridLayer with velocity magnitudes. Compass + Beaufort scale in legend.

**4. Solar Exposure / Shadow Study**
Three.js (same CDN import map as ALPA). Building geometry from GeoJSON. Time slider updates DirectionalLight azimuth/altitude. Cumulative exposure as heat overlay on ground plane.

**5. 3D Building Shadow Study**
Extension of ALPA's zoning viewer. Same Three.js setup + date/time slider + shadow map enabled.

### The Critical Rule

> **The SKILL.md must be extremely prescriptive about the code Claude should produce.** Vague instructions ("make a map") produce inconsistent results. Specific instructions (exact color hex, exact CSS classes, exact JS function signatures with edge-case handling) produce reliable, professional output every time.
