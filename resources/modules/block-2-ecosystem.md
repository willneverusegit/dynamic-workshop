# Block 2: The Claude Code Ecosystem

**Target audience:** Experienced programmers. Security analogies used throughout — especially relevant for the CySec engineer in the group.
**Duration:** ~90 minutes
**Goal:** Understand and use Skills, Hooks, Plugins, MCP, and RAG/NotebookLM to extend Claude Code

---

## Module 2.1: Skills & Commands

### The Core Idea

When you work with Claude Code repeatedly, you notice patterns: you always start sessions the same way, you always ask for commits in the same format, you always want tests written before implementation. Writing these instructions from scratch every time is wasteful and error-prone.

**Skills** solve this. A skill is a reusable prompt template — a set of instructions stored in a file that gets loaded into Claude's context when invoked. Instead of typing "follow TDD, write the failing test first, then implement..." every session, you type `/tdd` and Claude already knows exactly what to do.

**Commands** are the entry points — what the user types. A command can load a skill, pass arguments to it, or trigger a workflow. Think of it as the button on the wall vs. the procedure it triggers.

### Security Analogy: SOPs and Alarm Buttons

In physical security, you have **Standard Operating Procedures (SOPs)** — laminated documents in the control room that tell guards exactly what to do when a specific alarm fires. The SOP for "motion detected in Server Room A" might be: check CCTV feed, call supervisor, dispatch patrol, log incident.

The alarm button itself is just a trigger. The SOP is the intelligence behind it.

- **Skills = SOPs.** Detailed, reusable instructions. The "what to do."
- **Commands = Alarm buttons.** What the operator presses. The "how to trigger it."

A command `/tdd` is the button. The SKILL.md file for TDD is the SOP that Claude reads and follows.

### Anatomy of a Skill

Skills live in `skills/*/SKILL.md` inside a plugin, or in `~/.claude/skills/*/SKILL.md` for user-level skills. The file has two parts:

**YAML Frontmatter** (the header):
```yaml
---
name: tdd
description: >
  Test-Driven Development workflow. Use when user wants to write tests first,
  implement after, or says "write tests before code", "TDD", "red-green-refactor".
  Trigger phrases: TDD, test first, write tests, red-green, failing test
version: "1.0"
author: your-name
tags: [testing, workflow, tdd]
---
```

Key frontmatter fields:
- `name` — the identifier for this skill
- `description` — **critically important**: includes trigger phrases. Claude Code uses these to auto-detect when to use this skill without the user explicitly invoking it. The description acts as a semantic matcher.
- `version` — for tracking changes
- `tags` — for discoverability

**Markdown Body** (the actual instructions Claude follows):
```markdown
# TDD Workflow

You are following strict Test-Driven Development. Follow these steps exactly:

## Phase 1: Red (Write the Failing Test)
1. Ask the user what behavior needs to be implemented
2. Write the test FIRST — before any implementation code
3. Run the test and confirm it FAILS
4. Show the user the red output

## Phase 2: Green (Make It Pass)
1. Write the MINIMUM code needed to pass the test
2. No gold-plating, no extras — just enough to go green
3. Run the tests and confirm they PASS

## Phase 3: Refactor
1. Now clean up the code — remove duplication, improve naming
2. Tests must stay green throughout
3. Commit with a meaningful message

## Rules
- Never write implementation before a test exists
- Never write more code than necessary to pass the current tests
- If the user skips a phase, remind them of the process
```

### Anatomy of a Command

Commands live in `commands/*.md` inside a plugin, or can be defined in settings. They are what the user types directly (e.g., `/tdd`, `/commit`, `/review`):

```yaml
---
name: commit
description: Create a structured git commit with conventional format
user_invocable: true
arguments:
  - name: message
    description: Optional commit message prefix
    required: false
---
```

The command body can be short (just trigger a skill) or contain its own instructions:

```markdown
# Commit Command

When invoked, follow this commit workflow:
1. Run `git status` to see what changed
2. Run `git diff --staged` to review staged changes
3. Write a commit message following Conventional Commits format:
   - feat: new feature
   - fix: bug fix
   - refactor: code restructure without behavior change
   - test: adding tests
   - docs: documentation only
4. Ask for confirmation before committing
5. Create the commit
```

### User Skills: Your Personal Toolkit

User skills live in `~/.claude/skills/` and are available in **every project** on your machine — not just one repo. This is your personal toolkit.

```
~/.claude/skills/
  my-workflow/
    SKILL.md          # your custom instructions
  code-review/
    SKILL.md
  agent-orchestrator/
    SKILL.md
```

To create a user skill:
```bash
mkdir -p ~/.claude/skills/my-workflow
# create SKILL.md with frontmatter + instructions
```

Then in any Claude Code session, you can invoke it: `/my-workflow` or just describe what you want — Claude will match the trigger phrases in the description.

### The Difference Between Skills and Commands: Summary

| Aspect | Command | Skill |
|--------|---------|-------|
| What it is | Entry point / trigger | Instructions / procedure |
| Who uses it | End user (types it) | Claude (reads and follows it) |
| Where it lives | `commands/*.md` | `skills/*/SKILL.md` |
| Analogy | Alarm button | SOP document |
| Can contain | Arguments, short instructions | Full workflow, rules, examples |
| Invocation | User types `/commit` | Claude loads it when triggered |

A command can load a skill. A skill can be invoked without a command (via trigger phrase matching). They work together but serve different roles.

### Bundled Skills (Built-in, Always Available)

Claude Code ships with **bundled skills** — prompt-based playbooks available in every session without installation. These are different from built-in commands (which execute fixed logic):

| Skill | What it does | Example |
|-------|-------------|---------|
| `/batch <instruction>` | Parallel codebase changes across git worktrees | `/batch migrate src/ from Solid to React` |
| `/claude-api` | Loads API reference + Agent SDK docs for your language | `/claude-api` |
| `/debug [description]` | Activates debug logging and analyzes the log | `/debug failing mcp auth` |
| `/loop [interval] <prompt>` | Executes a prompt periodically | `/loop 5m check deploy status` |
| `/simplify [focus]` | Runs parallel reviews + fixes on recently changed files | `/simplify focus on perf` |

**Security analogy:** Bundled skills are like the standard operating procedures that come pre-installed with a security system. The `/batch` skill is like running a firmware update across all door controllers simultaneously — each in its own isolated worktree so a failure in one doesn't brick the others.

### Advanced Skill Frontmatter

Beyond `name` and `description`, skills support control fields that matter for security and automation:

```yaml
---
name: deploy
description: Deploy the current branch to staging
disable-model-invocation: true    # ONLY manual /deploy — never auto-triggered
allowed-tools: Read Grep Bash     # Intent scoping (not hard security!)
context: fork                     # Run in isolated subagent context
agent: sonnet                     # Use Sonnet model for the subagent
user-invocable: true              # Show in /skills list (false = background knowledge)
---
```

| Field | Effect | When to use |
|-------|--------|-------------|
| `disable-model-invocation` | Only manual trigger, never auto-detected | Critical actions: deploy, commit, delete |
| `allowed-tools` | Suggests which tools this skill needs | Scoping intent (not a hard security boundary!) |
| `context: fork` | Runs in a separate subagent context | Isolate the skill's context from your main session |
| `agent: <model>` | Specifies which model to use for the forked context | Cost control: use haiku for simple skills |
| `user-invocable: false` | Hides from command list, only auto-triggered | Background knowledge skills that Claude loads silently |

### Skill Discovery: `/skills`

Use `/skills` in any session to see all available skills — bundled, user, project, and plugin skills. Useful for discovering what's installed and available.

---

## Module 2.2: Hooks

### The Core Idea

Hooks are automated actions that run in response to Claude Code events — without you having to remember to ask. They execute shell commands (scripts, binaries, echo statements, curl calls) at specific moments in Claude's workflow.

Think of hooks as **event listeners** for Claude's behavior. When something happens — Claude uses a tool, Claude finishes a response, Claude is about to run a bash command — a hook can fire.

### Hook Types

**PreToolUse — Before, Can Block**

Fires before Claude uses a tool (runs bash, edits a file, calls an MCP server). The hook receives information about what Claude is about to do. It can:
- Log the action
- Warn the user
- **Block the action entirely** by exiting with a non-zero code

Use cases: prevent dangerous commands, enforce code review before deploy, require confirmation for irreversible actions.

**PostToolUse — After, Can React**

Fires after Claude uses a tool and receives the result. It can:
- Log what happened
- Trigger follow-up actions
- Send notifications
- Write to audit logs

Use cases: log all file edits, send Slack notification when tests pass, update a dashboard after each tool call.

**Stop — When Claude Finishes**

Fires when Claude finishes a response and is waiting for the next user input. Use cases: end-of-session summaries, cleanup tasks, status updates.

### Security Analogy: Access Control Sensors

In an access control system:

- The **card reader** fires when someone presents a card. Before the door opens, the system checks: Is this card authorized? Is it within allowed hours? Is the area currently accessible? **This is PreToolUse** — a check that can block the action.

- A **door-open sensor** fires after the door opens and logs: who entered, when, which door. **This is PostToolUse** — reactive logging after the fact.

- An **end-of-shift alarm** fires at 18:00 to remind the control room to check that all zones are secured. **This is Stop** — a scheduled/trigger-based end event.

The sensors don't replace the guards. They automate the repetitive checking so guards can focus on exceptions.

### Hook Configuration

Hooks are configured in `settings.json`. There are two locations:

- `~/.claude/settings.json` — global, applies to all projects
- `.claude/settings.json` — project-level, only for this repo

The structure:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "bash ~/.claude/hooks/pre-bash-check.sh"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "command",
            "command": "bash ~/.claude/hooks/audit-log.sh"
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Session ended' >> ~/.claude/session.log"
          }
        ]
      }
    ]
  }
}
```

The `matcher` field is a regex pattern that matches against the tool name (e.g., `"Bash"`, `"Edit"`, `".*"` for all tools).

### A Real Hook Example: Security Warning

This hook warns before any bash command containing `rm -rf` or `git push --force`:

**`~/.claude/hooks/pre-bash-check.sh`:**
```bash
#!/bin/bash

# Claude passes tool input via stdin as JSON
INPUT=$(cat)
COMMAND=$(echo "$INPUT" | jq -r '.command // ""')

# Check for dangerous patterns
if echo "$COMMAND" | grep -qE 'rm\s+-rf|git push.*--force|DROP TABLE|truncate'; then
  echo "WARNING: Potentially destructive command detected: $COMMAND" >&2
  echo "Pausing for confirmation..." >&2
  # Exit 1 to BLOCK the command from running
  exit 1
fi

exit 0
```

When Claude tries to run `rm -rf /tmp/build`, this hook fires, prints the warning, and exits with code 1 — Claude Code sees the block and stops.

### What Hooks Can Do

1. **Block dangerous operations** — prevent `rm -rf`, force pushes, production deploys without approval
2. **Enforce standards** — require tests to pass before any file edit is committed
3. **Log activity** — write every tool call to an audit file for compliance
4. **Add guardrails** — warn when Claude touches files outside the project directory
5. **Automate workflows** — after a successful test run, automatically open a PR draft
6. **Security scanning** — check for secrets, API keys, or `innerHTML` assignments before committing

Hooks are the mechanism that turns Claude Code from a helpful assistant into an **automated, policy-enforced development environment**.

### Hook Execution Types

Hooks don't just run shell commands. There are four execution types:

| Type | How it works | Best for |
|------|-------------|----------|
| **command** | Runs a shell command (default) | Simple checks, scripts, logging |
| **http** | Sends HTTP request to a URL | Webhook notifications, external APIs |
| **prompt** | Sends a prompt to Claude for evaluation | Complex, context-aware decisions |
| **agent** | Spawns a subagent for evaluation | Multi-step validation logic |

**Example: prompt hook** that evaluates whether a Bash command is safe:
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Evaluate if this command is safe for a production environment. Block if it modifies system files, deletes data, or accesses network resources outside the project scope."
          }
        ]
      }
    ]
  }
}
```

### Circuit Breaker Pattern

A critical hook pattern for preventing runaway token costs. When an agent executes the same command 3 times with the same error result, the hook detects the loop, stops the process, and asks the user for a strategy change.

**Security analogy:** A deadman switch in an alarm system. If the patrol guard stops checking in, the system escalates automatically. The circuit breaker prevents Claude from getting stuck in an "exploration trap" — endlessly retrying the same failing approach.

This is especially important when running autonomous loops or multi-agent workflows where token costs can escalate rapidly without human oversight.

---

## Module 2.3: Plugins

### The Core Idea

As you accumulate skills, hooks, agents, and commands, you want to package them together. A **plugin** is that package — a self-contained, distributable bundle that adds a coherent set of capabilities to Claude Code.

A plugin might contain:
- A set of related skills (e.g., all skills for code review workflows)
- Custom commands (e.g., `/review`, `/security-scan`)
- Agents (autonomous sub-processes that run within Claude)
- Hooks (automated behaviors)
- All wired together with shared context

### Security Analogy: Security Module

Think of a physical security system's **module** — say, a biometric access module. It contains:
- The fingerprint scanner hardware (sensors = hooks)
- The matching algorithm (logic = agents)
- The standard procedures for access granted/denied (procedures = skills)
- The control panel buttons (user interface = commands)
- The wiring diagram and configuration (metadata = plugin.json)

You buy the module, install it, and it integrates with your existing system. It's self-contained. You don't have to build each piece from scratch.

A Claude Code plugin is the same idea: install it once, and you get a coherent set of new capabilities.

### Plugin Structure

```
~/.claude/plugins/cache/my-plugin-marketplace/
  plugin.json              # metadata, version, dependencies
  skills/
    my-skill/
      SKILL.md             # skill instructions
    another-skill/
      SKILL.md
  commands/
    my-command.md          # command definitions
    another-command.md
  agents/
    my-agent.md            # agent definitions
  hooks/
    pre-tool-use.sh        # hook scripts
```

**plugin.json** — the manifest:
```json
{
  "name": "my-plugin",
  "version": "2.1.0",
  "description": "A plugin for automated code review and security scanning",
  "author": "your-name",
  "skills": ["my-skill", "another-skill"],
  "commands": ["my-command", "another-command"],
  "agents": ["my-agent"],
  "hooks": {
    "PreToolUse": ["hooks/pre-tool-use.sh"]
  },
  "dependencies": [],
  "enabled": true
}
```

To disable a plugin without deleting it, rename `plugin.json` to `plugin.json.disabled`.

### Notable Plugins in the Ecosystem

> **Custom Components:** The plugins listed below (`agentic-os`, `devil-advocate-swarms`,
> `multi-model-orchestrator`, `superpowers`, `hookify`) are **custom-built extensions**,
> not part of the official Claude Code installation. They demonstrate what's possible
> with the plugin system. The patterns they implement (adversarial testing, multi-model
> pipelines, meta-cognition workflows) are real — the specific implementations are our own.

**agentic-os** — the core orchestration plugin. Contains session bootstrapping, self-improvement loops, quality gates, research pipeline, iteration logging, and wrap-up workflows. Most other plugins depend on it.

**devil-advocate-swarms** — adversarial analysis. Spawns multiple Claude agents that argue against your implementation. Finds flaws through structured disagreement.

**multi-model-orchestrator** — routes tasks between Claude, Codex, and other models. Coordinates parallel agent execution. Contains the codex-swarm command for spawning multiple coding agents simultaneously.

**superpowers** — meta-cognition skills. Teaches Claude Code how to reason about its own workflow: when to brainstorm vs. plan vs. execute, how to do TDD, how to review code systematically.

**hookify** — makes it easy to create and manage hooks through a guided interface instead of editing JSON directly.

### Plugin Scopes

Plugins can be installed at different scopes — controlling who gets them and who controls them:

| Scope | Config Location | Who controls | Use case |
|-------|----------------|-------------|----------|
| **user** | `~/.claude/settings.json` | Individual developer | Personal tools |
| **project** | `.claude/settings.json` | Team (checked into repo) | Shared team tools |
| **local** | `.claude/settings.local.json` | Individual (not shared) | Personal overrides |
| **managed** | Org-managed settings | Enterprise admin | Organization-wide policies |

**Security analogy:** Like access control zones. User scope = your personal locker. Project scope = the team equipment room. Managed scope = the building-wide security policy that nobody except the security manager can change.

### Plugin CLI Management

```bash
# Install from marketplace or git
claude plugin install <source>        # --scope user|project

# Manage installed plugins
claude plugin list                    # Show all plugins
claude plugin enable <name>           # Enable a disabled plugin
claude plugin disable <name>          # Disable without removing
claude plugin uninstall <name>        # Remove completely
claude plugin update <name>           # Update to latest version

# Test local plugin during development
claude --plugin-dir ./my-plugin       # Load from local directory

# In-session management
/plugin                               # Interactive plugin manager
/reload-plugins                       # Hot-reload after changes
```

### Plugin Security: Supply Chain Risks

**Important warning from the official Plugin Directory:** Plugin contents (MCP servers, files, executables) are not fully controllable by Anthropic. Only install plugins you trust.

Real supply chain risks:
- Abandoned repos can be hijacked — a new maintainer pushes malicious code
- Plugin `bin/` executables run with your user permissions
- Plugin hooks execute shell commands in your environment
- Plugin MCP servers can access external systems

**Mitigation:** Review plugin code before installing. Pin versions. Use project scope (not user) for team plugins so changes go through code review. Use `/plugin` to inspect what's installed.

---

## Module 2.4: MCP (Model Context Protocol)

### The Core Idea

By default, Claude Code can read files, run bash commands, and search the web. MCP (Model Context Protocol) extends this with **connections to external services** — giving Claude Code access to real browsers, databases, communication platforms, and custom APIs.

MCP is an open standard (developed by Anthropic, now adopted broadly). Any service can implement an MCP server, and Claude Code can connect to it. The protocol defines how tools, resources, and prompts are exposed from external systems to the AI.

### Security Analogy: Integrated Building Management

A modern building management system doesn't just control doors in isolation. It **integrates**:
- Fire alarm system — automatic lockdown on smoke detection
- CCTV — camera feeds accessible from the access control console
- Visitor management — pre-authorized visitors appear in the door system
- HR system — employee terminations automatically revoke card access
- Time & attendance — door logs feed into payroll

Each external system exposes an interface. The central control system connects to all of them. Claude Code + MCP works the same way: Claude is the control system, external services expose MCP interfaces, and you configure the connections.

### Available MCP Servers

**Playwright (Browser Control)**
The most powerful for developers. Gives Claude a real browser it can control:
- Navigate to URLs
- Click buttons and links
- Fill forms
- Take screenshots
- Read page content
- Execute JavaScript

Use cases: automating admin panels, scraping dynamic content, testing UI flows, monitoring dashboards.

**Slack**
Read channels, send messages, search conversations. Use cases: Claude can notify your team when tests fail, post deployment summaries, answer questions by searching Slack history.

**Gmail / Google Calendar**
Read emails, create drafts, manage calendar events. Use cases: Claude can draft responses, schedule meetings, parse meeting notes into tasks.

**Databases (PostgreSQL, SQLite, etc.)**
Query databases directly. Claude can analyze data, generate reports, find anomalies without you writing SQL by hand.

**Context7** (documentation lookup)
Fetches current documentation for any library or framework. Prevents hallucination on API syntax by pulling real, current docs.

**Custom MCP Servers**
Any team can build an MCP server that exposes their internal tools. Deploy pipeline? Monitoring system? Bug tracker? Expose it via MCP and Claude can interact with it directly.

### MCP Transport Types

MCP servers communicate with Claude Code via different transport protocols:

| Transport | How it works | Best for | Status |
|-----------|-------------|----------|--------|
| **HTTP** | Remote server over HTTPS | Cloud services, shared team servers | **Recommended** |
| **stdio** | Local process, communicates via stdin/stdout | Local tools, custom scripts, system access | Good for development |
| **SSE** | Server-Sent Events | (legacy) | **Deprecated** — use HTTP instead |

### MCP CLI Management

```bash
# Add remote HTTP server (recommended for cloud services)
claude mcp add --transport http notion https://mcp.notion.com/mcp

# Add with auth header
claude mcp add --transport http --header "Authorization: Bearer xxx" myapi https://api.example.com/mcp

# Add local stdio server
claude mcp add github -- npx -y @modelcontextprotocol/server-github

# Add with environment variables
claude mcp add --transport stdio --env GITHUB_TOKEN=xxx github -- npx -y @modelcontextprotocol/server-github

# Manage servers
claude mcp list                  # List all configured servers
claude mcp get <name>            # Show server details
claude mcp remove <name>         # Remove a server

# Load servers from config file
claude --mcp-config servers.json     # Load additional MCP config
claude --strict-mcp-config           # ONLY use servers from config (no others)

# In-session
/mcp                             # Check status, manage OAuth, troubleshoot
```

### MCP OAuth

Claude Code supports OAuth flows for compatible remote MCP servers. When you connect to an OAuth-enabled server, Claude opens a browser for authentication. The callback port is fixable, and pre-configured credentials are supported for team setups.

### MCP Output Limits

Large MCP tool outputs can flood your context window. Claude Code has built-in limits:

| Threshold | Value | What happens |
|-----------|-------|-------------|
| Warning | 10k tokens | Claude warns about large output |
| Default max | 25k tokens | Output truncated beyond this |
| Per-tool override | up to 500k chars | Set via `_meta["anthropic/maxResultSizeChars"]` |

This matters especially for database queries (MCP Postgres) or large file listings. If you need more data, use the `_meta` override on the specific MCP tool — but be aware of the context cost.

### MCP Security

**Official Anthropic warning:** Third-party MCP servers are "use at your own risk." Anthropic does not audit them.

Key risks:
- **Prompt injection** — MCP servers that fetch untrusted content (web pages, tickets, emails) can inject instructions into Claude's context
- **Data exfiltration** — a malicious MCP server could read your project files via tool calls
- **Token theft** — OAuth tokens stored for MCP servers could be compromised

**Mitigation:** Only install trusted servers. Use `/mcp` to inspect what's connected. Use permissions to limit what Claude can do with MCP tools. Consider sandboxing for high-risk integrations.

### MCP Configuration (File-Based)

MCP servers can also be configured in `.mcp.json` (project-level) or `~/.claude/.mcp.json` (global):

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp@latest"],
      "env": {}
    },
    "slack": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "${SLACK_BOT_TOKEN}",
        "SLACK_TEAM_ID": "${SLACK_TEAM_ID}"
      }
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "POSTGRES_CONNECTION_STRING": "postgresql://user:pass@localhost/mydb"
      }
    }
  }
}
```

Claude Code reads this config at startup and connects to each server. The tools then appear in Claude's available tool list automatically.

### What MCP Changes

Without MCP: Claude is a powerful assistant that works with your local filesystem and terminal.

With MCP: Claude becomes an **orchestrator** that can:
- Control browsers to automate web workflows
- Read Slack and draft responses in your name
- Query your databases and generate reports
- Monitor your CI/CD pipeline and act on failures
- Interact with any system your organization uses

The boundary between "AI assistant" and "automated agent" blurs significantly with MCP. This is why hooks and guardrails (Module 2.2) matter more when MCP is involved.

---

## Module 2.5: RAG & NotebookLM

### The Problem: Training Cutoff and Niche Knowledge

Claude's training data has a cutoff date. Ask about a library released last month and Claude might guess, hallucinate plausible-looking but wrong API calls, or admit uncertainty.

Beyond recency, there's specificity: your internal APIs, your organization's coding standards, your proprietary architecture decisions, your team's accumulated knowledge — none of this is in Claude's training data. It doesn't exist in the general internet either.

**RAG (Retrieval-Augmented Generation)** solves this: instead of relying solely on training data, you give Claude access to a specific, curated knowledge base. When Claude needs to answer a question, it retrieves relevant content from your sources first, then generates an answer grounded in that material.

### Security Analogy: Building Blueprints vs. General Knowledge

Imagine hiring a security consultant. You could say: "We have a 3-story office building, here are the floor plans, here is our current access log format, here is our incident history." The consultant works from *your actual blueprints*.

Or you could say nothing and let them work from "general security knowledge." They'll give reasonable advice — but it won't match your specific door numbering, your particular alarm zones, your actual cable runs.

RAG is giving the consultant your actual blueprints. **Verified, specific, current knowledge — not generalizations.**

### NotebookLM: RAG Without Infrastructure

Building a RAG system from scratch requires: embedding model, vector database, chunking pipeline, retrieval logic, prompt engineering. It's a real engineering project.

**Google NotebookLM** is a hosted RAG system you can use immediately:

1. Create a notebook in NotebookLM
2. Add sources: URLs, PDFs, YouTube videos, text pastes, Google Docs
3. NotebookLM indexes and embeds everything
4. Query it via the web UI or API

> **Custom Component:** The `notebooklm` user skill shown below is a **custom-built wrapper**,
> not part of the official Claude Code installation. It demonstrates how RAG can be integrated
> via custom skills. Without this skill, use the NotebookLM web UI and bring results into
> Claude Code via `@`-file includes.

Claude Code has a `notebooklm` user skill that wraps the API, so you can query your notebooks directly from a Claude Code session:

```
notebooklm use my-api-docs
notebooklm ask "What is the authentication format for the /events endpoint?"
```

### The Full Workflow

**Step 1: Create a Notebook**
```
/notebooklm create "Anthropic Claude Code Docs"
```

**Step 2: Add Sources**
```
/notebooklm add-source https://docs.anthropic.com/en/docs/claude-code
/notebooklm add-source https://docs.anthropic.com/en/docs/build-with-claude/tool-use
# Add PDFs, internal docs, YouTube tutorial transcripts
```

**Step 3: Query from Claude Code**
```
notebooklm use claude-code-docs
notebooklm ask "What is the correct format for hook configuration in settings.json?"
```

NotebookLM returns the answer with citations to specific source pages — verifiable, not hallucinated.

### Use Cases

**Internal documentation**
Add your team's Confluence pages, architecture decision records, runbooks. Claude can answer "how do we handle database migrations in this project?" with your actual documented process.

**API documentation (current)**
Add the latest API docs for any library. Claude answers with current syntax, not potentially stale training data.

**Research collections**
Gather papers, articles, blog posts on a topic. Ask Claude to synthesize, compare approaches, identify gaps.

**Curated best practices**
Build a notebook of "how we do things here" — coding standards, review checklist, deployment checklist. Claude follows your actual standards, not generic advice.

**Regulatory / compliance knowledge**
Add the specific regulations your industry operates under. Claude's compliance advice cites your actual regulatory text.

### Why This Matters for Experienced Developers

If you're building anything non-trivial with Claude Code, you will eventually hit the limits of its general knowledge. The pattern that scales is:

1. Identify what Claude needs to know that isn't in its training
2. Build a notebook with those sources
3. Configure Claude Code to check the notebook before answering questions in that domain

This transforms Claude from "smart generalist" to "expert in our specific stack and context" — without fine-tuning, without retraining, without any ML infrastructure.

---

## Summary: The Ecosystem Stack

```
┌─────────────────────────────────────────────────────┐
│                   Your Workflow                      │
├─────────────────────────────────────────────────────┤
│  Commands (/tdd, /commit)  →  Skills (SKILL.md)     │ ← Module 2.1
├─────────────────────────────────────────────────────┤
│  Hooks (PreToolUse, PostToolUse, Stop)               │ ← Module 2.2
├─────────────────────────────────────────────────────┤
│  Plugins (bundle of skills + commands + agents)      │ ← Module 2.3
├─────────────────────────────────────────────────────┤
│  MCP (Playwright, Slack, DBs, custom services)       │ ← Module 2.4
├─────────────────────────────────────────────────────┤
│  RAG / NotebookLM (your knowledge base)              │ ← Module 2.5
└─────────────────────────────────────────────────────┘
```

Each layer extends Claude Code's reach — from automating your own workflow (skills/commands), to enforcing policies automatically (hooks), to deploying shared team capabilities (plugins), to connecting external systems (MCP), to grounding Claude in your specific knowledge (RAG).
