# Competitive Landscape — Urban AI Agent Platforms

**Date:** 2026-04-02
**Status:** Complete
**Goal:** Map competitors and adjacent players offering AI agents/plugins for urban analysis.

---

## The Gap: Nobody Has Done This Yet

Three things that **do not exist** as of April 2026:

1. **No Claude Code / GPT / Cursor plugin for urban climate analysis.** Zero developer-tool integrations for climate simulation in any AI coding assistant.
2. **No agent-native urban climate platform.** All existing tools (Forma, ENVI-met, Orbital Stack) are GUI-first. None expose an agent-friendly API where an LLM orchestrates simulations conversationally.
3. **No unified agent combining planning + climate.** PlanGPT handles text/policy, CityGPT handles spatial cognition, Ladybug handles simulation — nothing ties them together in an agent framework.

---

## Direct Competitors (Urban Simulation Platforms)

| Company | Product | Strengths | Weaknesses vs Infrared Agents |
|---------|---------|-----------|------------------------------|
| **Autodesk** | Forma | 100+ criteria, massive install base, Autodesk ecosystem | GUI-only, no agent API, vendor lock-in |
| **ENVI-met** | ENVI-met | Gold standard validation (3,000+ studies), Google AI partnership | Desktop-only, slow (hours), no conversational interface |
| **RWDI** | Orbital Stack | Real CFD + AI, wind engineering pedigree, freemium | GUI-first, no SDK/agent layer, limited to wind/thermal |
| **DBF** | Digital Blue Foam | Fast feasibility, sustainability analytics, modern UX | Singapore focus, no API, no agent integration |

## Adjacent Players (AI + Urban)

| Company | Product | Focus | Why Not a Threat |
|---------|---------|-------|-----------------|
| **Esri** | CityEngine + EuclidHL | Procedural 3D + zoning AI | GIS ecosystem, not simulation-focused |
| **TestFit** | TestFit | Real estate feasibility | Economics/unit counts, not climate |
| **Giraffe** | giraffe.build | Browser-based urban design | Light analytics, no deep simulation |
| **One Concern** | Resilience-as-a-Service | Climate risk intelligence | Asset-level risk, not design-phase |
| **UrbanFootprint** | Decision intelligence | 160M US parcels, climate hazard | Data/analytics, not simulation |

## Open Source Ecosystem

| Project | What | Opportunity for Infrared |
|---------|------|------------------------|
| **Ladybug Tools** | Environmental analysis (500K+ downloads) | Wrap as agent backend — "conversational Ladybug" |
| **SynxFlow** | GPU-accelerated flood sim | Flood agent backend, `pip install synxflow` |
| **DRL Urban Planning** | Reinforcement learning for land use (Nature pub) | Planning optimization backend |
| **CityGPT** | Spatial cognition LLM | Reference architecture for spatial reasoning |
| **PlanGPT** | Planning-specific LLM | Reference for domain-specific fine-tuning |
| **GeoColab** | Multi-agent geospatial code gen | Multi-agent architecture reference |
| **LLM-Geo** | Autonomous GIS agent | Data retrieval agent reference |

## Research Community

- **HKUST** runs [Awesome-Urban-LLM-Agents](https://github.com/usail-hkust/Awesome-Urban-LLM-Agents) and [Awesome-Urban-Foundation-Models](https://github.com/usail-hkust/Awesome-Urban-Foundation-Models) — canonical lists
- **AAAI 2025** had a dedicated [AI for Urban Planning workshop](https://ai-for-urban-planning.github.io/AAAI25-workshop/)
- **Nature Computational Science** published DRL urban planning and LLM urban planning era reviews

---

## Where Infrared Agents Fits

### Positioning

```
                    GUI-first                    Agent-first
                    ┌─────────────────────┬─────────────────────┐
  Design-phase      │ Forma, DBF, Orbital │                     │
  (architects,      │ Stack, ENVI-met     │  ★ INFRARED AGENTS  │
  planners)         │                     │                     │
                    ├─────────────────────┼─────────────────────┤
  Operations/Risk   │ Cityzenith, Hexagon │ One Concern (partial│
  (asset managers,  │ UrbanFootprint      │ but risk-focused)   │
  insurers)         │                     │                     │
                    └─────────────────────┴─────────────────────┘
```

### Strategic Plays

1. **Agent layer on simulation engines** — Wrap Ladybug Tools, SynxFlow, ENVI-met, or ML surrogates behind an LLM agent that interprets intent, configures sims, runs them, explains results.

2. **Multi-modal urban reasoning** — Combine CityGPT-style spatial cognition with climate simulation outputs. Agent looks at a site plan, understands geometry, runs wind/solar/flood analysis, recommends design interventions.

3. **Developer-first interface** — Claude Code skill or API-first agent that urban tech developers integrate into workflows. Not another GUI SaaS.

4. **Real-time climate feedback during design** — ML surrogate models (flood, UHI) enable sub-second feedback that makes conversational agent interaction viable. Physics simulation is too slow for interactive agent use.

---

## Key Takeaway

> The entire urban simulation industry is GUI-first and designed for human-in-the-loop operation. Nobody has built the **agent-native** version yet — where an LLM orchestrates simulations, interprets results, and suggests design changes through conversation or code. Infrared's existing simulation engines + SDK + this agent layer = first mover in a wide-open space.
