# Critical Evaluation of Research Findings

**Date:** 2026-04-02
**Source:** Sonnet critical reviewer
**Purpose:** Reality-check the Claude Code and ALPA research before building on it

---

## Accuracy Concerns

- **"~20% skill auto-activation reliability"** — presented as a precise metric with no stated methodology. May be anecdotal, not measured. Treat as directional, not definitive.
- **"Up to 10 parallel subagents"** — sounds like a soft default, not a documented hard constraint. May vary by context window pressure.
- **March 2026 cache regression + 5 outages** — plausible but unverified. Should be cited or qualified as approximate.
- **"Hooks make it safe enough for production"** — strongest unverified claim. PreToolUse hooks are a safety *layer*, not a safety *guarantee*. Calling this "production safe" without qualification is irresponsible for climate/infrastructure tooling where hallucinated file paths or missed validation could corrupt simulation outputs.
- **ML surrogates as "the solution"** — directionally correct but glosses over the hard problem: training data, physics-based validation, domain-specific uncertainty quantification. Understates engineering work by an order of magnitude.

## Gaps in the Research

- **Cost dimension almost entirely ignored.** Running 10 parallel subagents against 1M context is expensive enough to alter architectural decisions. Agent orchestration costs need explicit modeling.
- **No failure recovery discussion.** What happens when a subagent fails mid-pipeline? No checkpoint strategies, idempotency, or rollback discussed. Critical for long-running geospatial pipelines.
- **Data freshness.** NYC Socrata datasets have irregular update schedules. Making recommendations on stale data without surfacing staleness is a liability.

## Bias Assessment

- Documents 1 & 2 are moderately optimistic but not egregious. Limitations are named, even if undersold.
- Document 2 is most promotional: "intelligent pipeline orchestrator" and "safe enough for production" read as advocacy, not analysis.
- Document 3 (ALPA) is structurally biased — citing their own article ("Most AEC AI Apps Are Just Claude Wrappers") as evidence for a position is circular.

## ALPA Reality Check

**72 stars / 15 forks is marginal.** A well-received weekend project by a solo dev in a niche domain routinely hits 200-500 stars. A 36-skill, MIT-licensed system with published articles should hit 500+ if genuinely useful and discoverable. The gap between architectural ambition and community traction suggests:
1. Documentation/setup is harder than claimed
2. NYC-only due diligence limits generalizability
3. The architecture may be impressive-looking but not practically useful to target practitioners

**Five-layer architecture is a yellow flag at this scale.** Tracing bugs through router → agent → plugin → rules → hook is non-trivial. Makes sense at scale; at 72 stars it's likely over-engineered.

## Applicability to Infrared

### What transfers well
- **Hooks pattern** for guarding destructive operations (file writes, cloud API calls)
- **Subagent isolation** maps to simulation pipeline (each analysis module runs independently)
- **"Orchestration over infrastructure" framing** — Infrared has working simulation backends; the value-add is routing, parameter management, result synthesis
- **Rules** (always-on conventions: units, disclaimers) — lightweight, high value

### What does NOT transfer
- **Text-in/text-out limitation is fundamental, not a footnote.** Infrared's deliverables are maps, flood depth grids, heatmaps — not paragraphs. This is the core constraint.
- **Skill activation approach** (semantic matching) is poorly suited to reliability requirements. 20% activation failure is acceptable for a productivity tool, unacceptable for safety-relevant infrastructure.
- **ALPA's document/regulatory workflows** (human-readable I/O) vs Infrared's domain (raster data, vector geometries, numerical simulation outputs) are fundamentally different.
- **ML surrogates** belong in a separate research track with realistic timelines, not as a design assumption for near-term architecture.

## Key Decisions This Implies

1. **Use slash commands, not auto-activation** for all climate analysis skills
2. **Budget agent orchestration costs** before designing multi-subagent workflows
3. **Solve the visualization problem first** — without it, the agent is half-useful
4. **Start with 4-6 standalone skills**, earn complexity, don't architect it
5. **Don't copy ALPA's layering** at Infrared's current scale
