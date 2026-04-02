# Claude Code for Domain-Specific AI Agent Plugins: Urban Planning & Environmental Simulation

**Date:** 2026-04-02
**Source:** Sonnet research agent
**Status:** Pending critical review

---

## The Architecture You Are Working With

Claude Code's extensibility rests on three interlocking layers: plugins, skills, and tools. A plugin is a directory with a `.claude-plugin/plugin.json` manifest that packages skills, slash commands, and resources into a distributable unit. Skills are `SKILL.md` files loaded from `.claude/skills/` that inject specialized instructions into the conversation context via a meta-tool dispatcher — they do not run as separate processes, but reframe how Claude interprets subsequent requests. This progressive-disclosure architecture means domain knowledge is loaded on demand rather than flooding the context upfront, which matters considerably when you are working with GIS schemas, simulation parameter ontologies, or IFC building data vocabularies.

For urban and climate work, this means a plugin can bundle a "flood risk analysis" skill alongside a "GeoJSON formatting" skill and a "Windmill workflow push" slash command, all activating contextually when the agent detects relevant file types or explicit invocation. The marketplace ecosystem as of early 2026 contains hundreds of community skills in a universal `SKILL.md` format compatible across Claude Code, Cursor, and Gemini CLI — though activation remains heuristic and can be inconsistent when two skills have overlapping trigger conditions.

## Tool Ecosystem: What the Agent Can Actually Touch

The built-in tool set is the real workhorse. `Read`, `Write`, `Edit`, `Bash`, `Grep`, and `Glob` give the agent full filesystem access. For an urban simulation workflow, this translates to reading a GeoJSON polygon input, invoking a Python FEM solver or a QGIS processing script via `Bash`, parsing the output CSV, and writing formatted results back — all within a single conversational turn. `WebFetch` and `WebSearch` extend the agent's reach to live OpenStreetMap overpass queries, Copernicus climate APIs, or Eurostat data endpoints.

MCP server integration is architecturally the most significant capability for domain-specific agents. An MCP server can wrap a DocumentDB, a PostGIS database, a running simulation daemon, or a proprietary urban model API, exposing it as callable tools inside the agent's context. A climate agent could connect via MCP to a SynxFlow pluvial model, query mesh outputs, and feed results into a report generation pipeline — all without the user leaving the terminal. The practical caveat is that MCP server reliability is highly dependent on the quality of the server implementation; schema mismatches and authentication edge cases surface more often in production than in demos.

## Hooks, Memory, and Orchestration

The hooks system has matured substantially in 2026, now covering 21 lifecycle events with four handler types: command, HTTP, prompt, and agent. For urban planning workflows with high data-integrity stakes, pre-tool-use hooks function as deterministic guardrails — blocking destructive `Bash` commands, enforcing output schema validation before writing simulation results to shared storage, or routing specific task types to specialist subagents automatically. This is not probabilistic guidance embedded in a system prompt; it is middleware that fires regardless of what Claude decides.

File-based memory (`CLAUDE.md` and project memory files) provides the closest thing to persistent state. A climate simulation agent can maintain a memory file tracking the current study area boundaries, previously completed analysis modules, parameter choices made in prior sessions, and unresolved data gaps. This is text, not a database, which means it scales poorly once complexity grows past a few hundred lines — but it is sufficient for maintaining project-level continuity across weeks of iterative work.

Multi-agent orchestration, which launched alongside Opus 4.6 in February 2026, allows up to ten parallel subagents with worktree isolation. For an architectural simulation pipeline — running solar irradiance analysis, pedestrian wind comfort modeling, and flood risk scoring simultaneously on the same site — this is genuinely transformative. Each subagent operates in an isolated git worktree with its own context window, preventing the cross-contamination of intermediate results.

## Constraints That Shape What Is Achievable

The 1M token context window is now available at standard pricing as of March 2026, but it is not a free pass. Auto-compaction — which summarizes conversation history when the buffer fills — can silently drop fine-grained simulation parameters, intermediate reasoning steps, or specific numeric outputs from earlier in a long session.

**The most structurally significant limitation for the urban and climate domain is the absence of native visualization.** Claude Code is text-in, text-out. It cannot render a flood inundation map, display a 3D building shadow study, or stream a time-series simulation result as an animation. Every visualization artifact must be delegated to an external system — a Python script writing a matplotlib PNG, a Windmill flow triggering a Deck.gl frontend, or a QGIS headless render. This is workable but adds coordination overhead.

Plugin discovery and activation remain partially probabilistic. Claude infers which skill is relevant based on context — file types present, keywords in the request, prior conversation — which works well for unambiguous cases but produces inconsistent behavior when multiple domain plugins are loaded and their trigger conditions overlap.

Real-time simulation streaming is unsupported. A long-running EnergyPlus or CFD simulation cannot pipe progress updates into the agent's reasoning loop; the agent either waits for final output or polls via repeated `Bash` tool calls.

## Where the Agent Pattern Adds Genuine Value

The agent pattern's advantage over traditional GUI tools in this domain is not raw analytical power — QGIS, Rhino, and established simulation solvers are far superior for their specific purposes. **The value is in orchestration intelligence, data plumbing, and iterative reasoning across heterogeneous tools.** A climate adaptation agent that reads a municipal building footprint dataset, identifies the ten highest-risk parcels using elevation and impervious surface criteria, calls a flood model via MCP, formats a regulatory-compliant risk report, and pushes it to a Windmill workflow for stakeholder distribution — all in response to a single natural language request — is doing something that no single GUI application can replicate.

The hooks system makes this safe enough for production use: guardrails can enforce that no simulation output overwrites validated baseline data without explicit confirmation.

**Where the pattern falls short** is anywhere requiring tight real-time visual feedback, sub-second latency, or persistent spatial state that exceeds what a handful of text files can represent. A climate simulation agent built on Claude Code is best understood as an **intelligent pipeline orchestrator sitting above existing simulation infrastructure** — not as a replacement for it.

---

Sources:
- [Claude API Docs — Agent Skills](https://platform.claude.com/docs/en/agent-sdk/skills)
- [Claude API Docs — Plugins](https://platform.claude.com/docs/en/agent-sdk/plugins)
- [Claude Code Docs — Sub-agents](https://code.claude.com/docs/en/sub-agents)
- [Claude Code Docs — Hooks](https://code.claude.com/docs/en/hooks)
- [claudefa.st — Context Buffer Management](https://claudefa.st/blog/guide/mechanics/context-buffer-management)
- [DEV Community — Claude Code to AI OS Blueprint](https://dev.to/jan_lucasandmann_bb9257c/claude-code-to-ai-os-blueprint-skills-hooks-agents-mcp-setup-in-2026-46gg)
- [Medium — Claude Code Extensions Explained](https://muneebsa.medium.com/claude-code-extensions-explained-skills-mcp-hooks-subagents-agent-teams-plugins-9294907e84ff)
