# infrared-agents — Session Handover

**Date:** 2026-04-02
**Session duration:** ~2 hours
**Phase:** Research complete, architecture decided, implementation not started
**Repo:** https://github.com/Infrared-city/infrared-agents (public, Infrared-city org)
**Local:** `~/01_Projects/infrared-agents/`
**Memory:** `~/.claude/projects/-Users-Joo/memory/project-infrared-agents.md`

---

## What This Project Is

A **public repository** for AI agent skills and plugins for the Infrared urban simulation platform. The goal is to let architects, urban planners, and developers run climate analysis (UHI, wind, solar, flood) through AI agents — Claude Code, Claude Desktop, Cursor, VS Code, Gemini CLI, and potentially ChatGPT.

**OKR context:** Item #13 in Q2 2026 OKRs — "Company AI Agent Skills, first release by May 2026, monthly workshops, 6+ people making PRs." Also ties to OKR #3 (SDK Plugin Skill) and the broader platform strategy. See `/Users/Joo/Downloads/q2-okr-prep/00-context.md` and `05-q2-okr-current-state.md` for full OKR context.

**Relationship to ir-team-skills:** `ir-team-skills` (private repo, Infrared-city org) is the internal plugin marketplace for the team (ir-dev, ir-org, ir-research, etc.). `infrared-agents` is the public-facing counterpart — what customers and the community use.

---

## What Was Done This Session

### 1. Repository Created
- GitHub: `Infrared-city/infrared-agents` (public, MIT)
- Remote uses `github.com-company` SSH alias (serjoscha-ir account)
- 4 commits, all pushed to main

### 2. Landscape Research (13 files)

**Market landscape (3 files):**
- `references_urban-planning-agents.md` — 15+ tools: PlanGPT, CityGPT, Urban-GPT-Alpha, GIS Copilot, GeoColab, EuclidHL, LLM-Geo, DRL Urban Planning, TestFit, Giraffe, CityEngine, etc. Plus curated lists (Awesome-Urban-LLM-Agents, Awesome-Urban-Foundation-Models).
- `references_climate-sim-agents.md` — Autodesk Forma, ENVI-met, Orbital Stack, Digital Blue Foam, Ladybug Tools, SynxFlow, ML surrogate flood models, UrbanFootprint, One Concern, Google Environmental Insights.
- `references_competitive-landscape.md` — Gap analysis with positioning matrix. Key finding: **no agent-native urban climate platform exists.** All competitors (Forma, ENVI-met, Orbital Stack) are GUI-first.

**ALPA deep-dives (5 files) — the most valuable research:**
- `references_alpa-overview.md` — Full inventory: 36 skills, 9 plugins, 7 agents, 7 rules, 3 hooks. GitHub: AlpacaLabsLLC/skills-for-architects (MIT, 72 stars).
- `deepdive_plugin-architecture.md` — Marketplace.json structure, plugin.json schema, four-layer architecture (rules/skills/agents/dispatcher), composability model, bundled domain data pattern.
- `deepdive_skill-design.md` — SKILL.md anatomy, sidecar data files pattern (conditional loading based on input classification), tool usage archetypes, output formatting (markdown/JSON/HTML/CSV), error handling patterns, agent vs skill distinction.
- `deepdive_agent-orchestration.md` — Router pattern (/studio as pure intent classifier), agents as plain markdown files (prompt-as-architecture), parallel vs sequential orchestration, synthesis rules, fault tolerance, handoff points. Includes proposed climate-studio router design with 6 agents.
- `deepdive_visualization.md` — Three tiers (markdown, self-contained HTML, CDN-loaded 3D). Three.js zoning envelope viewer pattern, 822-line slide deck generator, color palette generator. Key finding: ALPA does NOT visualize analytical data (no charts, maps, wind roses). Proposed Deck.gl/MapLibre/D3 approach for Infrared.

**Claude Code analysis (2 files):**
- `references_claude-code-features-2026.md` — Plugin system, skills, Cowork, MCP, subagents, hooks, limitations (cache regression, 20% skill activation, 5 March outages).
- `references_claude-code-domain-analysis.md` — Capabilities and limitations for urban/climate domain specifically. Core insight: "intelligent pipeline orchestrator above existing simulation infrastructure, not a replacement."

**Critical evaluation (1 file):**
- `references_critical-evaluation.md` — Reality-checks all findings. 72 stars is marginal. "Production safe via hooks" is oversold. Text-in/text-out is the fundamental constraint. 20% activation failure = use slash commands. Cost of parallel subagents needs modeling.

**Cross-platform compatibility (1 file):**
- `references_cross-platform-compatibility.md` — Full compatibility matrix across Claude Code, Desktop, Cowork, Cursor, VS Code, Gemini CLI, ChatGPT. SKILL.md portability confirmed. MCP as universal interop layer. MCP Apps for inline visualization. Architecture recommendation with three layers.

**Adaptation blueprint (1 file):**
- `deepdive_adaptation-blueprint.md` — Proposed 7 plugins / 21-22 skills. What to copy from ALPA vs what to change. SDK integration (CLI-via-Bash preferred). Router design. Data sources. Visualization strategy. What ALPA lacks that Infrared needs (async jobs, numerical interpretation, shared data pipeline, cost awareness, design iteration, multi-resolution).

---

## Key Architecture Decisions Made

### 1. Dual-interface architecture
- **SKILL.md files** calling CLI via Bash → for Claude Code, Cursor, Gemini CLI (token-efficient)
- **MCP server (TypeScript)** wrapping TS SDK → for Claude Desktop, Cowork, VS Code (richer UI)
- Same TS SDK underneath both paths

### 2. Why two interfaces
- CLI via Bash is dramatically more token-efficient than MCP (one tool call vs 15+ tool schemas in context)
- But Cowork/Desktop cannot run Bash (confirmed — Cowork has no terminal)
- MCP Apps can render inline visualizations (maps, charts, 3D) in Desktop/Cowork — Claude Code cannot
- So Claude Code users get lean CLI skills; Desktop users get MCP tools + inline viz

### 3. Three-layer product concept (Jo's idea)
1. **Core plugin** — simulation skills (wind, solar, thermal, flood)
2. **Self-skill-builder** — meta-skill that helps users create their own workflow skills on top of the core
3. **Reference playbooks** — curated example workflows, each = LinkedIn post + video (GTM engine built into product)

### 4. Local-first project state
- API/CLI works with local files, not just remote jobs
- Fetch buildings → they land as a file (parquet/geojson)
- Run analysis → results land as a file
- Everything portable to QGIS, pandas, DuckDB
- Format decisions (parquet vs geojson for different data types) still open

### 5. Slash commands, not auto-activation
- Skill auto-activation is ~20% reliable → unacceptable for infrastructure tools
- All Infrared skills should be explicit slash commands (`/wind-comfort`, `/flood-risk`, etc.)

### 6. Start simple, earn complexity
- Don't build ALPA's 5-layer architecture upfront
- Start with 4-6 standalone skills that each do one thing well
- Add router/dispatcher only when you have enough skills that manual composition is painful
- ALPA built 36 skills before adding the router — earn that complexity

---

## What Is NOT Yet Documented

The following decisions and information emerged in conversation but are not captured in any research file:

1. **Cowork Bash confirmation** — Cowork cannot run Bash (confirmed by research agent with 10+ sources). This finding is not in `references_cross-platform-compatibility.md` yet. The file says "Unclear" for Cowork MCP support — should be updated to "Yes (remote natively, local via Desktop bridge)" with the caveat that Cowork does support SKILL.md files (shares `~/.claude/skills/` with Claude Code) but skills relying on Bash won't work.

2. **TypeScript SDK existence** — Jo confirmed a TS version of the Infrared SDK exists. The research files only document the Python SDK/CLI (pyproject.toml, Click, Pydantic). The TS SDK location, package name, and npm status are unknown. This needs to be found and documented before building the MCP server.

3. **The "CLI is more token-efficient than MCP" insight** — Discussed in conversation but not written to any file. Important architectural rationale.

4. **Parquet as results format** — Jo suggested parquet for analysis results and potentially for buildings/trees/surfaces too, with geojson as the alternative for interop. Not documented beyond the memory file.

5. **The dual-interface architecture diagram** (SKILL.md + CLI for Code, MCP server for Desktop) — Only in conversation and memory, not in any research file.

---

## Files That Need Updates

1. **`references_cross-platform-compatibility.md`** — Update Cowork row: confirm no Bash, confirm MCP support (remote + local bridge), confirm SKILL.md support (shared `~/.claude/skills/`), note MCP Apps status unclear.

2. **`README.md`** — Currently a placeholder. Should be updated to reflect the dual-interface architecture and the three-layer product concept once implementation starts.

3. **`project-infrared-agents.md` (memory)** — Should be updated with: Cowork confirmation, dual-interface architecture, TS SDK location (once found), and the cross-platform compatibility matrix.

---

## Next Steps (When Resuming)

1. **Find the TypeScript SDK** — Ask Jo where it lives. Check if it's published on npm or internal. This is a prerequisite for the MCP server.

2. **Build the first SKILL.md** — Start with `/site-buildings` or `/wind-comfort` as a proof-of-concept. Keep it simple: one skill, one CLI command, one output file. Let the format and project structure decisions emerge from actually building it.

3. **Decide parquet vs geojson** — For buildings (3D mesh → probably geojson or dotbim), results (gridded data → parquet makes sense), surfaces/trees (vector → geojson). May need both.

4. **Prototype the MCP server** — Once TS SDK is located. Start with one tool (e.g., `get-buildings`) and one MCP App visualization (Deck.gl building viewer). Test in Claude Desktop.

5. **Write a CLAUDE.md for the repo** — Project conventions, contribution guide, skill format requirements.

6. **Plan the first playbook** — Pick one end-to-end workflow (e.g., "Wind Comfort Assessment for a Development Site") that becomes both the first usable skill chain and the first LinkedIn/video content piece.

---

## Token/Cost Context

This session used substantial resources: 3 Sonnet agents (initial research) + 1 Sonnet (critical evaluation) + 5 Opus deep-divers (ALPA analysis) + 4 Sonnet agents (cross-platform/Cowork/MCP research) + 1 Sonnet (Cowork bash verification) = **14 subagent dispatches** plus the main conversation. The research is thorough enough that the next session should be implementation-focused, not more research.
