# Deep-Dive: Adaptation Blueprint — ALPA → Infrared Climate Agents

**Date:** 2026-04-02
**Source:** Opus deep-dive agent
**Focus:** How to adapt ALPA's patterns for environmental/urban climate simulation

---

## Proposed Plugin Structure (7 plugins, 21-22 skills)

| # | Plugin | Skills | Focus |
|---|--------|--------|-------|
| 00 | Site Context | 4 | `/site-buildings`, `/site-weather`, `/site-terrain`, `/site-context` (composite) |
| 01 | Wind Analysis | 3 | `/wind-speed`, `/wind-comfort`, `/wind-area` |
| 02 | Solar Analysis | 4 | `/daylight`, `/direct-sun`, `/shadow-study`, `/solar-radiation` |
| 03 | Thermal Comfort | 3 | `/thermal-comfort`, `/heat-stress`, `/cold-stress` |
| 04 | Flood Risk | 2-3 | `/flood-screening`, `/flood-simulation` (future), `/flood-report` |
| 05 | Results & Reporting | 3 | `/results-visualize`, `/results-interpret`, `/climate-report` |
| 06 | Dispatcher | 2 | `/climate-studio`, `/climate-skills` |

**~60% of ALPA's count.** Deliberate restraint — earn complexity, don't architect it.

---

## Key Adaptations from ALPA's Architecture

### What to Copy Directly

1. **SKILL.md format** — YAML frontmatter (name, description, allowed-tools) + workflow + output template + guidelines. Adopt wholesale.
2. **Agent-as-orchestrator** — Markdown files specifying which skills to call, in what order, what judgment to apply. No code, no framework.
3. **Rules system** — Always-on conventions: units (metric), data citation (DWD/UBA/BfN sources), professional disclaimer, transparency (show every input to every calculation).
4. **Hooks for output validation** — Post-write disclaimer check, metadata stamp, pre-commit data-provenance check.
5. **Dispatcher discipline** — "You do not contain orchestration logic. The agent files do."
6. **Output as file** — Write results to `~/Documents/infrared/` with `{analysis-type}-{location-slug}-{date}.{ext}` convention.

### What to Change

| ALPA Pattern | Infrared Adaptation |
|---|---|
| Synchronous API queries | Async job lifecycle: submit → poll/wait → download |
| Text input/output | Geometric input validation (dotbim, GeoJSON, coordinate systems) |
| Self-evident results (violation lists) | Numerical interpretation layer (Lawson criteria, UTCI thresholds) |
| Independent skill data fetching | Shared input data pipeline (`/site-context` fetched once, read by all) |
| Free unlimited APIs (Socrata) | Cost/resource awareness (tile count estimation, credit warnings) |
| Deterministic parameters (address → results) | Parameter guidance ("for worst-case summer wind in Central Europe, use...") |
| One-shot analysis | Design iteration support (modify geometry → re-run → compare before/after) |
| Single resolution (one tax lot) | Multi-resolution (single tile, polygon, city-scale) |

---

## SDK Integration: CLI-via-Bash

**CLI preferred over MCP server.** The `infrared-cli` already handles auth, serialization, async job lifecycle, and structured output. SKILL.md specifies exact command templates:

```bash
# Submit and wait for wind-speed analysis
infrared --json run wind-speed \
  --geometries ${BUILDINGS_FILE} \
  --wind-speed ${WIND_SPEED} \
  --wind-direction ${WIND_DIR}

infrared --json jobs wait ${JOB_ID} --timeout 300
infrared --json jobs results ${JOB_ID} -o ${OUTPUT_FILE}
```

This mirrors ALPA's approach of embedding exact API URLs in SKILL.md.

---

## Router Design (`/climate-studio`)

| If request involves... | Route to |
|---|---|
| Wind speed, wind comfort, Lawson criteria | Wind Analyst agent |
| Sun hours, daylight, shadow, solar radiation | Solar Analyst agent |
| Thermal comfort, heat stress, UTCI | Thermal Analyst agent |
| Flood risk, flood zone, pluvial | Flood Risk agent |
| Full site assessment, "analyze this site" | Site Assessment agent |
| Building data, fetch buildings | `/site-buildings` directly |
| Weather data, weather station | `/site-weather` directly |
| Visualize results, render | `/results-visualize` directly |

**Site Assessment agent** is the most important — handles multi-analysis requests:
1. Fetch site context once (shared across analyses)
2. Run requested analyses in parallel
3. Generate per-analysis visualizations
4. Synthesize combined report with cross-analysis insights

---

## Data Sources

| Domain | Primary Source | Fallback | API Key? |
|---|---|---|---|
| Buildings | Infrared API (`infrared buildings`) | Overture Maps (S3), OSM Overpass | No |
| Weather | Infrared API (`infrared weather`) | DWD Open Data, ERA5, NOAA | ERA5 needs CDS key |
| Terrain | Copernicus DEM (OpenTopography) | SRTM (USGS) | No |
| Flood zones | FEMA NFHL (US), Länder Hochwasserkarten (DE) | JRC European Flood Awareness | No |
| Land use | OSM Overpass, Overture Maps | CORINE Land Cover | No |

**Key difference from ALPA:** Cache aggressively. Building geometry and terrain don't change between analysis runs. `/site-context` writes to local files, subsequent skills read from those files.

---

## Visualization Strategy

| Analysis | Library | Approach |
|---|---|---|
| Wind/solar/thermal heatmaps | Deck.gl + MapLibre (CDN) | Self-contained HTML, grid overlay on basemap |
| Wind roses | D3.js (CDN) | SVG embedded in HTML |
| Shadow studies | Three.js (CDN) + time slider | Extension of ALPA's zoning viewer |
| Static report images | `infrared image generate` | PNG embedded in markdown |

**Dual output:** Interactive HTML for exploration + static PNG for reports. Same self-contained HTML pattern as ALPA.

---

## What ALPA Lacks That Infrared Needs

1. **Async job management** — timeout handling, progress reporting, failure recovery, parallel job collection
2. **Numerical result interpretation** — domain-specific thresholds (Lawson, UTCI, m/s → qualitative)
3. **Input data pipeline** — shared data preparation stage, dependency graph between skills
4. **Visualization framework** — reusable geographic heatmap renderer, not one-off Three.js
5. **Cost/resource awareness** — tile count estimation, credit warnings before large analyses
6. **Parameter guidance** — recommended values by climate zone, analysis type, use case
7. **Design iteration** — before/after comparison, result diffing, change tracking
8. **Coordinate system management** — dotbim local → WGS84 → projected coordinate handling
9. **Multi-resolution support** — single tile vs polygon vs city-scale

---

## Bottom Line

> ALPA provides a proven skill framework that Infrared should adopt for organizational patterns (SKILL.md format, agent orchestration, rules, hooks, dispatcher). But the core challenge — async simulation jobs, numerical interpretation, shared input pipelines, geographic visualization, and iterative design support — is work ALPA's architecture was never designed to handle.
>
> **Infrared's contribution to the Claude Code skill ecosystem will be demonstrating that the ALPA pattern scales from regulatory text retrieval to scientific simulation orchestration.**
