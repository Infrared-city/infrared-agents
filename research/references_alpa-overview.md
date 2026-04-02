# ALPA (alpa.llc) — Architecture Studio Overview

**Date:** 2026-04-02
**Source:** Sonnet research agent
**Status:** Pending deep-dive analysis

---

## Company

- **Entity:** Alpaca Design Lab LLC, dba ALPA
- **Location:** New York, NY + Uruguay (Manantiales)
- **Founder:** Federico Negro
- **Contact:** hi@alpacalabs.co
- **GitHub:** [AlpacaLabsLLC/skills-for-architects](https://github.com/AlpacaLabsLLC/skills-for-architects) (MIT, 72 stars)
- **Pricing:** Free, open-source. No subscription.

## Product: Architecture Studio

Open-source Claude Code plugin system: **36 skills, 9 plugins, 7 agents** covering the full architectural project lifecycle.

### Five-Layer Architecture

```
Architecture Studio
├── /studio              — Router: natural language entry point
├── agents/              — 7 orchestrators with domain judgment
├── plugins/             — 9 installable bundles (project lifecycle order)
│   └── skills/          — 36 single-purpose SKILL.md workers
├── rules/               — 7 always-on output conventions
└── hooks/               — 3 event-driven automations
```

---

## All 9 Plugins

| # | Plugin | Skills | Focus |
|---|--------|--------|-------|
| 0 | Due Diligence | 7 | NYC Open Data (landmarks, permits, violations, ownership, variances) |
| 1 | Site Planning | 4 | Climate, transit, demographics, neighborhood history from address |
| 2 | Zoning Analysis | 3 | Buildable envelope + 3D visualization (NYC + Uruguay) |
| 3 | Programming | 2 | IBC occupancy loads, workplace space programs |
| 4 | Specifications | 1 | CSI MasterFormat spec generation |
| 5 | Sustainability | 4 | EPD parsing, research, comparison, spec integration |
| 6 | Product & Materials | 11 | FF&E research, extraction, tagging, image processing, SIF export |
| 7 | Presentations | 2 | HTML slide decks (22 slide types), color palettes |
| 8 | Dispatcher | 2 | Smart router (`/studio`) + skills menu |

---

## All 36 Skills

### Due Diligence (NYC)
- `/nyc-landmarks` — LPC landmark and historic district check
- `/nyc-dob-permits` — DOB permit and filing history
- `/nyc-dob-violations` — DOB and ECB violations
- `/nyc-acris` — ACRIS property transactions (deeds, mortgages, liens)
- `/nyc-hpd` — HPD violations, complaints, registration (residential)
- `/nyc-bsa` — BSA variances and special permits
- `/nyc-property-report` — Combined due diligence (all 6 above)

### Site Planning
- `/environmental-analysis` — Climate, precipitation, wind, sun, flood zones, seismic, soil
- `/mobility-analysis` — Transit and mobility from address
- `/demographics-analysis` — Demographics and market analysis
- `/history` — Neighborhood context and history

### Zoning Analysis
- `/zoning-analysis-nyc` — NYC buildable envelope (FAR, height, setbacks from PLUTO)
- `/zoning-analysis-uruguay` — Uruguay buildable envelope (FOS, FOT, height from TONE)
- `/zoning-envelope` — Interactive 3D zoning envelope viewer

### Programming
- `/workplace-programmer` — Space programs from headcount + work style
- `/occupancy-calculator` — IBC egress and plumbing fixture counts

### Specifications
- `/spec-writer` — CSI outline specs from materials list

### Sustainability
- `/epd-parser` — Extract impact data from EPD PDFs
- `/epd-research` — Search EC3, UL, Environdec registries
- `/epd-compare` — Side-by-side comparison + LEED MRc2 check
- `/epd-to-spec` — CSI sections with EPD requirements and GWP thresholds

### Product & Materials Research
- `/product-research` — Brief-based FF&E research
- `/product-spec-bulk-fetch` — Extract specs from product URLs at scale
- `/product-spec-bulk-cleanup` — Normalize messy FF&E schedules
- `/product-spec-pdf-parser` — Extract specs from product PDFs
- `/product-image-processor` — Download, resize, background-remove product images
- `/ffe-schedule` — Format specification schedules
- `/product-enrich` — Auto-tag products (categories, colors, materials, style)
- `/product-match` — Find similar products from image/name/description
- `/product-pair` — Suggest complementary products
- `/csv-to-sif` — Convert to SIF for dealer procurement
- `/sif-to-csv` — Convert SIF to readable spreadsheets

### Presentations
- `/slide-deck-generator` — Self-contained HTML slide decks (22 types)
- `/color-palette-generator` — Palettes from description, image, or hex

### Dispatcher
- `/studio` — Smart router for intent classification
- `/skills-menu` — Show all available skills

---

## 7 Agents (Orchestration Layer)

| Agent | Domain |
|---|---|
| site-planner | Runs 4 site skills in parallel, synthesizes brief |
| nyc-zoning-expert | Full NYC property + zoning + 3D visualization |
| workplace-strategist | Headcount → space program, IBC compliance |
| product-and-materials-researcher | Find, extract, tag FF&E products |
| ffe-designer | Schedule building, QA, dealer export (SIF) |
| sustainability-specialist | EPDs, GWP, LEED eligibility, spec thresholds |
| brand-manager | Slide decks, color palettes, presentation QA |

## 7 Rules (Always-On)

units-and-measurements, code-citations, professional-disclaimer, csi-formatting, terminology, output-formatting, transparency

## 3 Hooks

- `post-write-disclaimer-check` — Warns if regulatory output lacks disclaimer
- `post-output-metadata` — Stamps YAML front matter
- `pre-commit-spec-lint` — Flags malformed CSI section numbers

---

## 4 Articles

| Title | URL | Summary |
|---|---|---|
| Claude Code Cheat Sheet | [link](https://alpa.llc/articles/claude-code-cheat-sheet) | Quick reference for architects using Claude Code |
| Introducing Architecture Studio | [link](https://alpa.llc/articles/introducing-architecture-studio) | 5-layer architecture announcement |
| Distributing Skills to Teams | [link](https://alpa.llc/articles/distributing-skills-to-teams) | Installation + team distribution patterns |
| Most AEC AI Apps Are Just Claude Wrappers | [link](https://alpa.llc/articles/evaluating-ai-tools-for-architecture) | Opinion: build your own skills, don't buy commodity wrappers |

---

## Data Sources Used (No API Keys Required)

- NYC Open Data via Socrata (18 datasets: DOB, LPC, ACRIS, HPD, BSA, PLUTO)
- EC3 / UL / Environdec for EPDs
- IBC code tables (bundled)
- Maldonado/TONE zoning data (Uruguay)

## Tech Stack (Site)

Astro (static), Tailwind CSS, Martian Mono font, Cloudflare hosting
