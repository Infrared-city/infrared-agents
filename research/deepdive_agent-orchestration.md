# Deep-Dive: ALPA Agent Orchestration Patterns

**Date:** 2026-04-02
**Source:** Opus deep-dive agent
**Focus:** Router, agents, multi-skill orchestration, rules, hooks

---

## The Router Pattern (/studio)

The `/studio` command is a **pure intent-classification layer with zero orchestration logic**. It uses a static routing table — not keyword matching, but natural language understanding against 11 intent categories mapping to 7 agents + 2 direct-to-skill routes + a catch-all.

**Ambiguity handling:** Rule 3 allows exactly one clarifying question. Example: "Analyze 123 Main St, Brooklyn NY" → "Do you need site context or property and zoning analysis? Or both?" If "both" → state the sequence and route to first agent.

**What the router deliberately does NOT do** (most instructive design choice):
- Does not contain orchestration logic (agents do)
- Does not call skills in sequence (agents decide)
- Does not add steps, QA checks, or synthesis
- Only allowed tools: `Read`, `Glob`, `Grep` (just enough to load agent definitions)

**This separation of routing from orchestration is the core architectural pattern.**

---

## Agent Definitions: Prompt-as-Architecture

The 7 agents are **plain markdown files** in `/agents/`. Not code, not config schemas, not subagent API definitions. Natural-language workflow specifications Claude reads and follows at runtime.

Each agent file has consistent structure:
- **Persona** — one-sentence role ("You are a senior site planning consultant")
- **When to Use** — scope boundary
- **How You Work** — multiple named paths (A, B, C) for different input scenarios
- **Synthesis Rules** — cross-domain interpretation (the most valuable section)
- **Handoff Points** — explicit agent-to-agent transition rules
- **What You Don't Do** — hard scope boundaries

**This is prompt-as-architecture. The agent file is both the spec and the implementation. Claude's instruction-following capability IS the orchestrator.**

---

## Multi-Skill Orchestration

### Parallel Pattern (Site Planner)
Step 2: "Run all four analyses in parallel: /environmental-analysis, /mobility-analysis, /demographics-analysis, /history."

After parallel execution → **synthesis rules** (cross-domain connections no individual skill can produce):
- Environmental + mobility: "A flood zone near a transit hub changes the calculus"
- Demographics + history: "Rapidly gentrifying neighborhood + historic district = design review scrutiny"
- Mobility + demographics: "High walk score + low car ownership = parking requirements may be negotiable"

Instruction: **"Don't repeat what each skill already said. Add the connections they can't see individually."**

### Staged Parallel-then-Sequential (NYC Zoning Expert)
1. Step 2: Run 6 due diligence queries in parallel
2. Step 3: Run zoning analysis (needs property data from step 2)
3. Step 4: Generate 3D envelope (needs zoning results from step 3)

### Fault Tolerance (nyc-property-report)
Each domain query is independent. "If any query fails, note the error and continue." A single API timeout does not crash the whole report.

---

## Agent-to-Skill Handoff

Each agent has an **input-assessment step** that picks the right path:
- Product & Materials Researcher: 3 paths — (A) brief→search→specs, (B) URLs/PDFs→extraction, (C) reference product→alternatives

**Quality gates:** FF&E Designer runs a QA checklist with 3 severity levels:
- Blocking (must fix before presenting)
- Warnings (flag to user)
- Completeness checks (by room)

**Agent-to-agent handoff** uses explicit directional rules:
- Site Planner → NYC Zoning Expert (if site is in NYC and needs zoning)
- NYC Zoning Expert → Site Planner (if needs site context)
- These create a directed graph, not a rigid pipeline

---

## Rules System (7 Always-On)

Reference documents loaded automatically — not invoked, not gated. Global system prompt layer:

1. **Units/measurements** — imperial default, specific abbreviations
2. **Code citations** — always include edition year, use § symbol, link to public sources
3. **Professional disclaimer** — mandatory on regulatory outputs, specific language prohibitions
4. **CSI formatting** — MasterFormat 2018, six-digit with spaces
5. **AEC terminology** — preferred terms ("program" not "room list")
6. **Output formatting** — tables for comparisons, YAML front matter
7. **Transparency** — verifiability standard: user can verify every number, reproduce every calculation, update results with changed assumptions

The transparency rule is the most philosophically significant — it's a fundamental constraint on reasoning, not cosmetic.

---

## Hooks (3 Event-Driven)

All registered via `settings-snippet.json`, receive JSON on stdin, parse with `jq`:

1. **post-write-disclaimer-check** (PostToolUse Write) — scans for regulatory keywords (zoning, setback, FAR, flood zone), warns if no disclaimer. Exit 0 (warn-only).
2. **post-output-metadata** (PostToolUse Write) — prepends YAML front matter to .md files. Skips README/SKILL/CLAUDE.md.
3. **pre-commit-spec-lint** (PreToolUse Bash) — catches malformed CSI section numbers before commit. 10-15s timeout.

---

## Application to Climate-Studio

### Proposed Router (`/climate-studio`)
Same thin-dispatch pattern. Intent categories: site-level climate risk, UHI, flood/pluvial, green infrastructure, regulatory compliance, building-level energy, reporting.

### Proposed Agents (markdown files)

| Agent | Parallel Skills | Synthesis |
|---|---|---|
| **Climate Risk Analyst** | flood-risk, heat-island, wind-comfort, air-quality | "High UHI zone + flood corridor = dual-stress in summer events" |
| **Green Infrastructure Planner** | intervention-catalog, cooling-potential, stormwater-retention | Cost-benefit synthesis of interventions |
| **Regulatory Compliance Expert** | kang-compliance, eu-taxonomy, foerderung-search | German KAnG, BauGB, WHG, EU taxonomy mapping |
| **Urban Data Analyst** | overture-landuse, building-stock, population-vulnerability | Exposure + vulnerability overlay |
| **Simulation Coordinator** | synxflow-run, ml-surrogate, simulation-qc | Full physics vs ML surrogate decision |
| **Report Builder** | klima-report-generator, dashboard-builder | Klimaanpassungskonzept, ESG docs, dashboards |

### Proposed Rules
- **Units** — metric-only, DIN formatting
- **Data citation** — always link to DWD/UBA/BfN sources with access date
- **Professional disclaimer** — German liability context ("Keine Gewähr")
- **Regulatory citation** — specific paragraph of KAnG/BauGB/WHG with publication date
- **Transparency** — same verifiability standard as ALPA

### Proposed Hooks
- Post-write regulatory check (Hochwasserrisiko/Hitzebelastung → verify disclaimer)
- Post-write metadata stamp (municipality, analysis date, data vintage)
- Pre-commit data-provenance check (DWD/Copernicus data references include access date + dataset version)

### Key Insight
**Orchestration logic lives in natural-language agent definitions, not code.** Domain experts (climate scientists, urban planners) can author and modify agent workflows without touching a codebase. The router stays thin. The rules stay global. The agents stay autonomous within their domain but have explicit handoff contracts.
