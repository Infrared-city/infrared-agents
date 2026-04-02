# Deep-Dive: ALPA Skill Design Patterns

**Date:** 2026-04-02
**Source:** Opus deep-dive agent
**Focus:** Skill anatomy, data embedding, tool usage, output formatting, error handling, complexity spectrum

---

## Skill Anatomy

Every SKILL.md follows a consistent pattern:

```yaml
---
name: environmental-analysis
description: Climate and environmental site analysis — temperature, precipitation...
allowed-tools: [WebSearch, WebFetch, Write, Edit, Read, Bash]
user-invocable: true
---
```

Body sections:
1. **Title** — `# /skill-name — Human-Readable Name`
2. **Role sentence** — persona assignment
3. **Usage block** — invocation syntax with examples
4. **On Start / Input** — what to ask, what to parse
5. **Workflow** — numbered steps
6. **Output Format** — complete template in fenced code block
7. **Edge Cases** — explicit failure handling
8. **Notes / Caveats** — domain wisdom

**Complexity range:** `/skills-menu` = 88 lines (static display). `/zoning-analysis-nyc` = 387 lines + 1,540 lines of sidecar data. `/slide-deck-generator` = 823 lines (bulk is CSS/JS template).

## Data Embedding: The Critical Pattern

**Conditional-loading architecture with sidecar data files.**

`/zoning-analysis-nyc` has 10 files in `zoning-rules/`:
- `overview.md` (133 lines) — always loaded
- `residential.md` (188 lines) — loaded if R-district
- `commercial.md` (165 lines) — loaded if C-district
- `manufacturing.md` (123 lines) — loaded if M-district
- `contextual-districts.md` (166 lines) — loaded if A/B/D/X suffix
- Plus: special-districts, use-groups, parking, city-of-yes, pluto-fields

**Key insight:** The skill routes through data files based on input classification. For R7A: load overview + residential + contextual-districts. For M1-1: load overview + manufacturing. Keeps each Read under context limits while accessing ~1,540 lines of regulatory tables.

**Shared schemas:** `plugins/06-materials-research/schema/product-schema.md` (33 columns) is reused by all 11 materials-research skills via relative path.

### Transfer to Infrared
Climate data as sidecar files:
- `climate-zones/cfb.md`, `climate-zones/dfa.md` — loaded by location
- `regulations/kang-federal.md`, `regulations/bavaria-water-law.md` — loaded by jurisdiction
- `thresholds/din-19712.md`, `thresholds/starkregen-index.md` — loaded by analysis type

## Tool Usage Archetypes

| Archetype | Tools | Example |
|-----------|-------|---------|
| API-querying | WebFetch | `/zoning-analysis-nyc` — Socrata endpoints with exact URLs |
| Web-research | WebSearch + WebFetch | `/environmental-analysis` — curated source list (NOAA, FEMA) |
| PDF-processing | Bash (Python/PyMuPDF) | `/epd-parser` — embedded Python script |
| HTML-generation | Write | `/slide-deck-generator` — 400+ lines CSS/JS template |

**Tool chaining pattern:** query → classify → load context → compute → format → write

## Output Formatting Patterns

1. **Markdown reports with YAML frontmatter** — default for text analysis
2. **Structured JSON embedded in markdown** — interoperability (zoning → 3D viewer)
3. **Self-contained HTML** — interactive outputs (slide decks, 3D viewers)
4. **CSV with rigid schema** — tabular data (42-column EPD schema)

## Error Handling Patterns

- **Graceful degradation per domain** — "If any query fails, note error and continue"
- **Input validation with fallbacks** — BBL → address → BIN fallback chain
- **Detection of unprocessable input** — scanned PDFs, password-protected, non-English
- **Rate limiting** — HTTP 429 → wait 5s, retry once, then suggest API token
- **Data honesty** — "Never fabricate data" reinforced by global transparency rule

## Agent vs Skill Distinction

| Aspect | Skill | Agent |
|--------|-------|-------|
| Purpose | Single atomic operation | Multi-skill orchestration |
| Location | `plugins/*/skills/*/SKILL.md` | `agents/*.md` |
| Path decisions | Executes one path | Chooses among multiple paths |
| Cross-domain | Cannot see other skills' output | Synthesizes across skills |
| Handoff | Never hands off | Explicit handoff points |
| Boundaries | Implicit (tool whitelist) | Explicit "What You Don't Do" |

## Key Patterns for Infrared Climate Skills

1. **Sidecar data files** for regulatory thresholds, climate zones, DIN standards
2. **Curated source lists** (DWD, Copernicus, ISIMIP — ban commercial sources)
3. **Machine-readable JSON blocks** for downstream consumption (simulation → visualization)
4. **Conditional context loading** based on location/jurisdiction
5. **Agent orchestration** for multi-domain climate risk assessment
