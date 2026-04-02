# Deep-Dive: ALPA Plugin Architecture

**Date:** 2026-04-02
**Source:** Opus deep-dive agent
**Focus:** Marketplace, plugin.json, directory organization, distribution, composability

---

## Key Finding: The Entire System Is Markdown + Shell Scripts

No TypeScript, no Python, no build step. 186 files, mostly markdown, with 3 shell scripts for hooks. The entire system runs on structured prose Claude reads at invocation time.

## Directory Structure

```
skills-for-architects/
  .claude-plugin/marketplace.json    ← root marketplace manifest
  agents/                            ← 7 agent orchestration files (.md)
  hooks/                             ← 3 bash scripts + settings-snippet.json
  plugins/                           ← 9 plugin directories (00 through 08)
  rules/                             ← 7 always-on convention files (.md)
```

## Marketplace Manifest

```json
{
  "name": "skills-for-architects",
  "owner": { "name": "ALPA", "email": "hello@alpa.llc" },
  "metadata": { "description": "...", "version": "1.0.0" },
  "plugins": [
    { "name": "00-due-diligence", "source": "./plugins/00-due-diligence", "description": "..." }
  ]
}
```

Key observations:
- **No dependency graph** — no `depends_on`, no version constraints between plugins
- **Numeric prefixes encode lifecycle order** (00=due-diligence → 08=dispatcher)
- **Flat list** — no category taxonomy at marketplace level
- **Per-plugin installation** — cherry-pick which phases you need

## Plugin Manifest (plugin.json)

```json
{
  "name": "02-zoning-analysis",
  "version": "1.1.0",
  "description": "...",
  "author": { "name": "ALPA", "url": "https://alpa.llc" },
  "keywords": ["zoning", "envelope", "NYC", "PLUTO"],
  "homepage": "https://alpa.llc/skills",
  "license": "MIT"
}
```

**Notably absent:** No `skills` array. No `agents`/`rules` references. No `hooks` config. No `dependencies`. Skill discovery is purely structural — `plugins/{name}/skills/{skill-name}/SKILL.md`.

## Four-Layer Architecture

1. **Rules** (always loaded, passive) — 7 markdown files governing all output
2. **Skills** (invoked explicitly, single-purpose) — 36 skills across 9 plugins
3. **Agents** (invoked by router, multi-skill orchestrators) — 7 markdown files
4. **Dispatcher** (entry point, routing) — `/studio` classifies and dispatches

Flow: `/studio` → agent → skills (parallel or sequential) → output (governed by rules), with hooks on lifecycle events.

## Composability Model

- **Agents compose skills** (nyc-zoning-expert orchestrates 9 skills across 3 plugins)
- **Skills compose through data artifacts** (zoning-analysis produces JSON → zoning-envelope consumes it)
- **Agents hand off to agents** (directed graph, not rigid pipeline)
- **No cross-plugin imports** — skills are pure prose, nothing to import

## Bundled Domain Data

Critical pattern: domain knowledge shipped as structured files alongside skills:
- `zoning-rules/` — 10 markdown files encoding NYC Zoning Resolution (~1,540 lines)
- `data/occupancy-load-factors.json` — IBC Table 1004.5
- `data/archetypes.json` — workplace benchmarks
- `normativa/` — Maldonado zoning ordinances + `tone-zones.json`

Skills load files **conditionally** based on input classification.

## What to Keep for Infrared

1. Four-layer separation (rules/skills/agents/dispatcher)
2. Skills as structured prose with embedded domain knowledge
3. Data artifact contracts between skills (JSON schema bridges)
4. `allowed-tools` as permission boundaries
5. Hooks for quality gates

## What to Change for Infrared

1. **Add dependency declarations** — climate simulation has tighter data dependencies
2. **Add versioned data packages** — climate data is time-series, versioned, large
3. **Add parameterized skill invocation** — typed params (`--scenario RCP8.5 --resolution 100m`)
4. **Add intermediate result caching** — 10-min land-use classification shouldn't re-run
5. **Add validation hooks between pipeline stages** — CRS compatibility, temporal bounds
6. **Replace single-user file I/O with structured state** — project-scoped directory + metadata
