# Cross-Platform Compatibility: Skills, MCP, and Distribution

**Date:** 2026-04-02
**Source:** 4 Sonnet research agents
**Status:** Complete

---

## The Compatibility Matrix

| Capability | Claude Code | Claude Desktop | Cowork | Cursor | VS Code/Copilot | Gemini CLI | ChatGPT |
|---|---|---|---|---|---|---|---|
| SKILL.md | Yes | No | No | Yes | Yes | Yes | No |
| MCP servers | Yes | Yes | Unclear | Yes | Yes | Yes | No* |
| MCP Apps (inline UI) | **No** | **Yes** | Unclear | Unclear | Yes | Unclear | Yes |
| Plugin marketplace | Yes | No | No | No | No | No | No |
| Hooks | Yes | No | No | No | No | No | No |
| Local Bash execution | Yes (built-in) | Via MCP only | No | Yes | Yes | Yes | No |
| Local file access | Yes (built-in) | Via MCP Roots | Limited | Yes | Yes | Yes | No |
| GPT Actions (HTTP) | N/A | N/A | N/A | N/A | N/A | N/A | Yes |

*ChatGPT supports remote MCP with OAuth 2.1 + DCR (announced but depth unclear)

---

## Key Findings

### 1. SKILL.md Is Cross-Platform (for dev tools)
Same file format works in Claude Code, Cursor, VS Code/Copilot, Gemini CLI. Confirmed from real usage (IAAC project uses both `.claude/skills/` and `.gemini/skills/`). Directory prefix differs per tool but content is identical.

**Caveats:**
- `allowed-tools` is Claude Code-specific (others ignore it)
- Plugin packaging (`.claude-plugin/`) is Claude Code-only
- Activation semantics differ per tool

### 2. MCP Is the Universal Interop Layer (for dev tools)
Works in Claude Code, Claude Desktop, Cursor, VS Code, Gemini CLI. Not ChatGPT (needs GPT Actions).

**MCP Apps (inline UI) are the game-changer:**
- Render interactive HTML (maps, 3D, charts) directly in the conversation
- Supported by Claude Desktop, Claude.ai (web), VS Code, ChatGPT
- **NOT supported by Claude Code** (terminal can't render iframes)
- Built with TypeScript (@modelcontextprotocol/ext-apps)

### 3. Claude Desktop vs Claude Code: Different Strengths

| | Claude Code | Claude Desktop |
|---|---|---|
| Best at | Full dev workflows, Bash, subagents, skills | MCP tools + inline UI visualization |
| MCP Apps | No (terminal) | Yes (inline iframes) |
| Skills | Yes | No |
| Hooks | Yes | No |
| File access | Direct (built-in) | Via MCP Roots |

### 4. Cowork Is Poorly Documented
Not listed as a distinct MCP client. Likely inherits Claude Desktop's capabilities but not confirmed. Don't build Cowork-specific integrations without testing.

### 5. Infrared SDK/CLI Stack
- **Python SDK** (`infrared-api-sdk`) — requests, pydantic, numpy
- **Python CLI** (`infrared-cli`) — Click framework, wraps SDK
- **TypeScript SDK** — exists (per user), location TBD
- **.NET SDK** — exists (`InfraredCity-dotNET`, "for Connectors")

---

## Architecture Recommendation

### Three layers, each extending reach:

```
Layer 1: MCP Server (TypeScript)
├── Wraps TS SDK
├── Exposes tools: run-wind, run-solar, get-buildings, etc.
├── MCP App UI: Deck.gl maps, wind roses, shadow studies (inline in Desktop)
├── Local project state: parquet/geojson files
├── Works in: Claude Code, Claude Desktop, Cursor, VS Code, Gemini CLI
│
Layer 2: SKILL.md files (markdown)
├── Domain knowledge, workflow instructions, interpretation guides
├── Sidecar data: climate zones, regulatory thresholds, Lawson criteria
├── Works in: Claude Code, Cursor, VS Code, Gemini CLI
├── Enhances Layer 1 with domain intelligence
│
Layer 3: GPT Actions (future, optional)
├── HTTP wrapper over the MCP server
├── OpenAPI spec
├── Works in: ChatGPT
```

### Why MCP Server in TypeScript:
- MCP Apps require JS/TS for the UI layer
- TS SDK exists for Infrared
- Same language for server, tools, and visualization
- Can run locally (Node.js) or remotely (Cloudflare Workers)
- Claude Desktop has built-in Node.js runtime (no install needed)

### Why SKILL.md files alongside MCP:
- MCP tools are generic ("run wind analysis with these params")
- Skills add domain intelligence ("for worst-case summer analysis in Central Europe, use...")
- Skills add workflow orchestration ("run these 4 analyses in parallel, then synthesize")
- Skills add interpretation ("7.2 m/s at pedestrian level = uncomfortable per Lawson criteria")
- Cross-platform bonus (works in Cursor, VS Code, Gemini too)

### Local project state:
- MCP server reads/writes local files (parquet, geojson)
- Buildings, results, weather data persist between sessions
- Portable to QGIS, pandas, DuckDB
- MCP Roots capability tells Claude Desktop which directories the server can access

---

## Sources
- [MCP Client Feature Matrix](https://modelcontextprotocol.io/clients)
- [MCP Apps docs](https://modelcontextprotocol.io/extensions/apps/overview)
- [ext-apps GitHub](https://github.com/modelcontextprotocol/ext-apps)
- [Claude Code Skills docs](https://code.claude.com/docs/en/skills)
- [Agent Skills open standard](https://agentskills.io)
- [Claude Cowork announcement](https://claude.com/blog/cowork-research-preview)
- [Cloudflare remote MCP guide](https://developers.cloudflare.com/agents/guides/remote-mcp-server/)
