# Claude Code: State of the Platform — April 2026

**Date:** 2026-04-02
**Source:** Sonnet research agent
**Status:** Pending critical review

---

## 1. Plugin System

Claude Code's plugin system is the primary packaging and distribution layer for extending the tool. A plugin lives in a git repository with a `.claude-plugin/` directory containing a `plugin.json` manifest (name, version, description, dependencies) and any associated skills, hooks, subagent definitions, and MCP server configs.

Plugins are distributed through **marketplaces** — also git repos with a `.claude-plugin/marketplace.json` that lists available plugins and their source locations. Anthropic maintains an official marketplace at `anthropics/claude-plugins-official`. Users add a marketplace with `/plugin marketplace add <url>` and install individual plugins with `/plugin install <name>`. Private teams can self-host their own marketplace with nothing more than a git repo in the right format.

The plugin model is composable: a single plugin can bundle skills, hooks, MCP server configuration, and subagent definitions together. Anthropic's official skills for Office documents (PowerPoint, Excel, Word) and PDF processing ship as plugins through this marketplace infrastructure.

## 2. Skills

Skills are the core mechanism for giving Claude domain-specific behavior. Each skill is a directory containing a `SKILL.md` file with two sections: a YAML frontmatter block (between `---` markers) and a markdown body. The frontmatter holds metadata fields — `name`, `description`, `disable-model-invocation`, `allowed-tools` — while the markdown body contains the instructions Claude executes when the skill activates.

The `description` field is the activation trigger. Claude continuously compares the conversation context against skill descriptions and loads a skill's content into its context when there is a match. This makes skills **flexible** — they activate automatically based on semantic match rather than explicit invocation. The contrast is with slash commands, which are **rigid** — they run a fixed instruction set only when explicitly called by the user, making them better for deterministic, repeatable operations like deployment scripts.

A known reliability issue: independent testing found that skill auto-activation based on description matching succeeds only around 20% of the time. Claude will often recognize a skill as relevant but proceed without using it. For critical workflows, slash commands remain more reliable.

## 3. Claude Code Cowork

Cowork launched January 12, 2026 as a research preview and is Anthropic's consumer-facing agentic product for knowledge workers. It runs inside the Claude desktop app (macOS and Windows), connects to local files and applications, and completes multi-step tasks autonomously — the same underlying agent architecture as Claude Code, but without the terminal interface.

The target users are non-developers: researchers, analysts, legal teams, and finance teams who work with documents and data. Cowork can draft, edit, search across local files, and chain multi-step workflows. It is currently available to Max, Pro, Team, and Enterprise subscribers. Agent Teams work across both Claude Code and Cowork contexts.

## 4. MCP (Model Context Protocol)

MCP is an open standard, introduced by Anthropic, for connecting AI agents to external tools and data sources. An MCP server exposes a defined set of tools (functions) and resources that Claude Code can discover and call. Claude Code ships with support for connecting to local or remote MCP servers configured in `settings.json`.

A key engineering detail: Claude Code uses **deferred tool loading** — MCP tool definitions are not injected into the context window until Claude actually needs them. This keeps base context overhead low even when many MCP servers are configured. Official MCP servers exist for GitHub, Slack, Google Drive, Postgres, Puppeteer, and dozens of other integrations. There is also a growing ecosystem of community MCP servers registered at `apps.extensions.modelcontextprotocol.io`.

## 5. Subagents

Subagents are separate Claude instances spawned by a parent agent via the **Agent tool**. Each subagent starts with a clean context window — the only information it receives is what the parent passes in the tool call prompt. This isolation prevents context contamination and lets subagents run specialized instructions without inheriting the full parent conversation history.

Subagents can be configured with their own `model`, `tools`, `skills`, `mcpServers`, `hooks`, `maxTurns`, and `permissionMode`. When `isolation: worktree` is set, the subagent runs in a temporary git worktree — a full copy of the repo at the current commit — so it can make changes without affecting the main working tree. Background execution is supported: the parent can spawn a subagent and continue working, with results returned asynchronously. `Ctrl+B` backgrounds the current task in the UI.

## 6. Hooks

Hooks are shell scripts or executables that run in response to agent lifecycle events. Three hook types cover the main use cases:

- **PreToolUse** hooks fire before any tool executes; they can inspect the tool name and input, and return exit code 2 to block the action (with stderr feedback returned to Claude). As of v2.0.10, PreToolUse hooks can also mutate tool inputs before execution — enabling transparent enforcement of patterns like adding `--dry-run` flags or redacting secrets.
- **PostToolUse** hooks fire after a tool completes successfully; they receive both the input and the result and can inject feedback into the agent's next step.
- **Stop hooks** fire when Claude finishes a response; returning `"decision": "block"` prevents Claude from stopping and forces it to continue — useful for enforcing task-completion criteria.

Hooks are defined in `settings.json` with matcher patterns specifying which tool names trigger them.

## 7. Key Limitations

**Context window and cost**: The 1 million token context window became generally available for Opus 4.6 and Sonnet 4.6 in March 2026, but using it is expensive. A caching regression bug discovered around March 23, 2026 was silently breaking prompt caching and inflating costs by 10–20x. Anthropic acknowledged users on $100–200/month Max plans were exhausting five-hour session windows in 90 minutes — described as the team's top priority.

**Reliability at scale**: March 2026 saw five major Claude platform outages in a single month, suggesting real infrastructure strain as usage has grown. Skill auto-activation is unreliable at roughly 20% success rate in practice. Agent Teams are still experimental and disabled by default. Context rot — degraded accuracy as context grows large — remains a fundamental model behavior, not just an infrastructure problem.

**Isolation overhead**: Worktree-isolated subagents and parallel agent teams significantly increase token spend and complexity. Background task coordination works but adds observability challenges. There is currently no native way to share state between subagents other than writing to and reading from the filesystem.

---

Sources:
- [Claude Code Docs — Plugin Marketplaces](https://code.claude.com/docs/en/plugin-marketplaces)
- [Claude Code Docs — Skills](https://code.claude.com/docs/en/skills)
- [Claude API Docs — Skill Best Practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- [Claude — Cowork Research Preview](https://claude.com/blog/cowork-research-preview)
- [Claude Code Docs — MCP](https://code.claude.com/docs/en/mcp)
- [Claude Code Docs — Sub-agents](https://code.claude.com/docs/en/sub-agents)
- [Claude Code Docs — Hooks](https://code.claude.com/docs/en/hooks)
- [Claude Code Docs — Agent Teams](https://code.claude.com/docs/en/agent-teams)
- [ahmadullin.com — Complete Guide to Building Skills for Claude](https://ahmadullin.com/claude-skills-complete-guide-2026/)
- [claudefa.st — Agent Teams Setup Guide](https://claudefa.st/blog/guide/agents/agent-teams)
