# Block 3: Advanced & Mind-Blowing — Teaching Material

> **Audience:** Experienced programmers. Security analogies used throughout —
> especially relevant for the CySec engineer in the group.

### Feature Maturity Overview

| Symbol | Meaning |
|--------|---------|
| :white_check_mark: | **Stable** — Part of Claude Code, production-ready |
| :test_tube: | **Experimental** — Feature flag required, may change |
| :wrench: | **Custom** — Workshop-specific implementation |

| Feature | Status |
|---------|--------|
| Subagents (Explore, Plan, Custom) | :white_check_mark: |
| Git Worktrees | :white_check_mark: |
| `/loop`, Cron, Scheduled Tasks | :white_check_mark: |
| Permission Modes (6 levels) | :white_check_mark: |
| OS-Level Sandboxing | :white_check_mark: |
| Agent Teams | :test_tube: |
| Codex Swarm (`multi-model-orchestrator`) | :wrench: |
| Devil's Advocate Debate Chain | :wrench: |
| Self-Improve Loop (`agentic-os`) | :wrench: |

---

## Module 3.1: Agents & Multi-Agent Orchestration

### What Is an Agent?

An agent is an **autonomous Claude instance** with a specific role, a specific set of tools,
and its own context window.  It is not a skill (a skill guides a single Claude instance).
An agent **spawns a separate Claude process** with its own permissions and focus.

Think about your access control infrastructure: you do not have one guard do everything —
monitor cameras, respond to alarms, manage card access, and write incident reports.
You have **specialized roles**.  Agents work the same way.

**Typical agent roles in a software project:**
- **Code Explorer** — reads and analyzes structure, finds patterns, understands the codebase
- **Code Reviewer** — reads changes, finds bugs, security issues, style violations
- **Implementer** — writes code, applies patches, creates files
- **Test Writer** — generates and runs tests, validates behavior
- **Security Auditor** — scans for vulnerabilities, injection flaws, secrets leaks

Each agent is good at its job because it is **not distracted** by anything else.

---

### Why Specialization Matters

A single Claude instance doing everything:
- Has a limited context window — long conversations degrade quality
- Can get confused by contradictory instructions across tasks
- Must context-switch, just like a human multitasking

Multiple specialized agents:
- Each starts fresh with a clean context tailored to its role
- Can work **in parallel** — massively faster on independent subtasks
- Output from one feeds cleanly into the next

**Security analogy:** Your SOC (Security Operations Center) dispatches teams.
Camera operators do not respond to physical intrusions.
Response teams do not configure card readers.
The SOC orchestrates — it does not do everything itself.

---

### The Agent Tool

Claude Code's built-in Agent tool launches subagents.  When Claude uses it:

1. Creates a new Claude instance
2. Passes it a role description, instructions, and tool permissions
3. Waits for results (or fires-and-forgets for parallel work)
4. Receives the output and uses it in the orchestrator's reasoning

Each subagent gets:
- Its own context window (blank slate, purpose-built)
- Specific tool allowances (Read, Write, Bash, or none)
- A focused task description

The orchestrating Claude stays at the top, aggregating results.

---

### Agent Definition Anatomy

Agents are defined as YAML frontmatter + body text in `.md` files:

```yaml
---
name: code-explorer
description: >
  Analyzes codebases to understand structure, dependencies, and patterns.
  Use when you need: project overview, file map, dependency graph, entry points.
  Examples: "explore this project", "what does this codebase do", "map the architecture"
model: claude-haiku-3-5
color: cyan
allowed_tools:
  - Read
  - Glob
  - Grep
---

You are a code exploration specialist.  Your job is to understand, not to change.

When asked to explore a project:
1. Start with the directory structure
2. Read key files: README, main entry points, config
3. Map the dependency graph
4. Identify architectural patterns
5. Report clearly — structure, purpose, notable patterns, risks

Never modify files.  Never execute code.  Explore only.
```

**Key fields explained:**

- **`description`** — This is how the orchestrator decides *when* to use this agent.
  Write it with trigger phrases and examples.  This is the routing logic.
- **`model`** — Haiku for quick reads.  Sonnet for analysis.  Opus for architecture decisions.
- **`allowed_tools`** — **Security through least privilege.**  An explorer has no Write.
  A reviewer has no Bash.  Lock down to exactly what is needed.
- **`color`** — Visual differentiation in logs.  Matters for readability during parallel runs.

---

### Orchestration Patterns

#### Pattern 1: Fan-Out / Fan-In

Launch N agents in parallel, collect all results, synthesize.

```
Orchestrator
  |-> Agent A (Task 1) -> Result A -+
  |-> Agent B (Task 2) -> Result B -+-> Synthesize -> Final Output
  `-> Agent C (Task 3) -> Result C -+
```

**Use when:** Tasks are independent.  Analyzing 3 modules.  Running scans on 5 files simultaneously.

**Speed gain:** Each task takes 60 seconds.  3 sequential = 180s.  3 parallel = ~60s.  **3x faster.**

#### Pattern 2: Pipeline (Sequential)

Output of Agent A becomes input of Agent B.

```
Orchestrator -> Agent A (Explore) -> Agent B (Review) -> Agent C (Fix)
```

**Use when:** Each step depends on the previous.  Plan -> Implement -> Test -> Review.

#### Pattern 3: Hierarchical

Orchestrator spawns sub-orchestrators, each managing their own agents.

```
Master Orchestrator
  |-> Sub-Orchestrator 1 (Security)
  |     |-> Scanner Agent
  |     `-> Validator Agent
  `-> Sub-Orchestrator 2 (Quality)
        |-> Reviewer Agent
        `-> Test Writer Agent
```

**Use when:** Problem requires decomposition at multiple levels.  Enterprise-scale codebases.

---

### Quick-Start: Triggering Multi-Agent Work

Tell Claude:

> "Analyze this project from two angles simultaneously: one agent maps the architecture,
> another finds all TODO and FIXME comments.  Run them in parallel and give me a combined report."

Claude will use the Agent tool to do exactly that.
The skill `/agent-orchestrator` automates the full orchestration workflow.

### `/batch` — Parallel Refactoring Across Worktrees

The bundled skill `/batch` is the built-in way to run large-scale parallel changes:

```
/batch migrate all src/ files from Solid to React
```

What happens:
1. Claude analyzes the scope and breaks it into independent units
2. Each unit runs in a **separate git worktree** — isolated file state
3. Multiple subagents work in parallel, one per worktree
4. Tests run per worktree to validate each change independently
5. Results are aggregated into PRs or a summary report

**When to use:** Large refactors, file migrations, bulk changes across many files.

### `/tasks` — Background Task Management

Use `/tasks` (alias `/bashes`) to see all running background tasks in your session. When you spawn parallel agents or long-running operations, this is your dashboard.

### Agent Teams (Experimental)

Beyond subagents (which are one-shot workers), Claude Code supports **Agent Teams** — multi-session coordination where agents can communicate with each other:

```
TeamCreate — create a new teammate agent (e.g., reviewer, QA, docs writer)
SendMessage — send messages between team members
```

Each teammate runs as a **separate session** with its own context window. They can:
- Work on different parts of a task in parallel
- Send findings to each other
- The lead agent synthesizes results

**Cost warning:** Token cost scales linearly per teammate. Each idle teammate still consumes tokens. Use Sonnet for teammates, keep teams small, clean up after completion.

**Security analogy:** A full SOC team — lead operator, camera monitor, patrol dispatcher, incident reporter. Each has their own workstation (context), their own radio channel (SendMessage), and their own access level (tools). The lead operator (orchestrator) coordinates.

---

## Module 3.2: Nested Orchestration & Multi-Model Pipelines

### Different Models, Different Strengths

| Model | Strengths | Best Used For |
|---|---|---|
| **Claude Opus** | Deep reasoning, architecture, judgment | Planning, review, quality decisions |
| **Claude Sonnet** | Fast, capable, good at most tasks | General implementation, analysis |
| **Claude Haiku** | Very fast, cheap, focused | Bulk processing, simple reads, brainstorming |
| **Codex (OpenAI)** | Fast code generation, different training | Implementation, second opinion |

Key insight: **Codex generates code fast and cheap.  Claude Opus reviews with high judgment.**
Using both together beats either alone.

---

### The Claude -> Codex -> Claude Pipeline

**Phase 1: Claude Opus Plans**
- Analyzes requirements, designs architecture
- Breaks task into clear, unambiguous implementation tickets
- Writes detailed specs for each ticket

**Phase 2: Codex Implements (Fast, Parallel)**
- Receives specs from Phase 1
- Generates code — fast, no hesitation
- Multiple Codex agents work on different tickets simultaneously

**Phase 3: Claude Opus Reviews**
- Reads all generated code
- Checks against original requirements
- Finds bugs, security issues, spec violations
- Decides: accept, request changes, or reject

**Why this works:**
- Opus is expensive but used only where judgment matters — plan and review
- Codex handles the mechanical generation — cheap, fast, parallel
- Two AI systems have different blind spots.  What one misses, the other catches.

**Security analogy:** Senior architect writes the spec.
Contractors install hardware in parallel.
Senior engineer inspects every door, every reader, every cable run before sign-off.

---

### Codex Swarm

> **:wrench: Custom Component:** `multi-model-orchestrator` is a custom plugin, not part of Claude Code.

```
/multi-model-orchestrator:codex-swarm --decompose "Build a Python CLI with scan, check, report commands"
```

With `--decompose`:
1. Claude analyzes the task and breaks it into N independent subtasks
2. N Codex agents spawn in parallel, one per subtask
3. All work simultaneously — total time is the slowest agent, not the sum
4. Claude reviews all results together, catches integration issues

**When to use Codex Swarm:**
- Large, parallelizable implementation tasks
- You want a second opinion from a different AI model
- Speed matters more than cost per token
- Tasks are well-specified and mechanical

---

### Practical Example: Security CLI Tool

Task: "Build a Python security CLI with three commands:
- `scan` — lists open ports on a given host
- `check` — verifies a URL returns HTTP 200
- `report` — generates a JSON security report"

With `--decompose`, Claude might decompose into:
1. Agent 1: CLI framework + scan command (socket-based port enumeration)
2. Agent 2: check command (HTTP request, timeout handling, status validation)
3. Agent 3: report command (JSON output, aggregates results)
4. Agent 4: Test suite for all three commands

All 4 Codex agents run simultaneously.  Claude reviews the assembled result.

---

## Module 3.3: Security & Adversarial Testing

> **Note to moderator:** Frame this as automated penetration testing with a trial system.
> Because that is literally what it is.  The CySec person will feel very at home.

### The Devil's Advocate Swarms Pipeline

A **multi-agent adversarial system** for finding and fixing security and quality issues.
It mirrors real professional security review — with one difference: it runs automatically.

**Four stages:**

---

#### Stage 1: Scanners

Multiple scanner agents analyze code simultaneously from different angles:

- **Security Scanner** — injection flaws, hardcoded credentials, unsafe patterns, missing auth
- **Quality Scanner** — error handling gaps, missing input validation, unsafe type assumptions
- **Architecture Scanner** — trust boundary violations, privilege escalation paths, data flow risks

**Why multiple scanners with overlap?**

Two scanners finding the same issue = high confidence it is real.
One finding something the other misses = investigate carefully.

Overlap is validation through consensus.
This is exactly how you run a physical security audit: two independent teams,
compare findings, overlapping results are high-confidence.

---

#### Stage 2: Debate

For each finding, two agents argue:

**Prosecutor** presents the attack scenario:
> "An attacker can inject arbitrary commands through the search parameter.
> The input reaches a shell call with no sanitization.
> Here is a proof-of-concept payload."

**Defender** challenges the finding:
> "Input is validated against a whitelist of allowed characters before it reaches that code path.
> The validation rejects all shell metacharacters.
> This is not exploitable as written."

This debate eliminates false positives.
In traditional security audits, 30-50% of automated scanner findings are false positives.
The debate stage filters them automatically — before any engineer touches them.

The Prosecutor's job: argue every finding as if writing an exploit report for a paying client.
The Defender's job: find every reason the finding is not actually exploitable.

A finding survives only if the Prosecutor wins convincingly.

---

#### Stage 3: Consensus

A consensus agent reviews the full debate and decides:
- **CONFIRMED** — real vulnerability, promote to fix queue
- **FALSE POSITIVE** — not exploitable in context, discard with explanation
- **NEEDS INVESTIGATION** — ambiguous, flag for human review

Only CONFIRMED findings proceed.  Everything else is logged — your audit trail.

---

#### Stage 4: Fixers

For each confirmed finding, a fixer agent:
- Reads the exact finding and full debate context
- Implements the **minimal targeted fix** — no refactoring, no scope creep
- Writes a regression test that would have caught the vulnerability
- Documents why the fix is correct

**The security analogy, complete:**
- Prosecutor = pentester writing the exploit report
- Defender = developer explaining what was and was not exploitable
- Consensus = security manager deciding what gets patched
- Fixers = patching team
- The whole process runs autonomously, with full documentation

---

### Security Audit Skill

The `/security-audit` skill provides automated scanning for any project receiving external input.

**What it checks:**

| Check | What It Finds |
|---|---|
| Command injection | Shell calls built from unvalidated external input |
| SQL injection | Queries built by string concatenation with user data |
| Hardcoded credentials | Passwords, API keys, tokens in source files |
| Unsafe deserialization | Loading serialized objects from untrusted sources |
| Open redirects | Redirect targets controlled by user-supplied input |
| Missing authentication | Endpoints reachable without auth check |
| Supply chain risks | Dependencies with known CVEs, unpinned versions |
| Missing input validation | External parameters used without sanitization |

Runs in minutes.  Integrates into CI/CD pipelines.

---

### Permission Modes in Detail

This is the most important security concept for autonomous workflows. The 6 modes form a spectrum from maximum control to maximum autonomy:

| Mode | What Claude can do without asking | Set via |
|------|----------------------------------|---------|
| **default** | Read files only | Default (no flag needed) |
| **acceptEdits** | Read + edit/write files | `--permission-mode acceptEdits` |
| **plan** | Shows full plan, user approves once for all steps | `--permission-mode plan` |
| **auto** | ML classifier auto-approves low-risk actions | `--permission-mode auto` (Team/Enterprise only) |
| **dontAsk** | Everything allowed by allow/deny rules, no prompts | `--permission-mode dontAsk` |
| **bypassPermissions** | Everything. Period. | `--dangerously-skip-permissions` |

**auto mode restrictions:** Only available for Team/Enterprise/API accounts, only with Sonnet/Opus 4.6, only via Anthropic API (not Bedrock/Vertex). Not available on Pro/Max consumer plans.

**CI/CD pattern:** Use `dontAsk` with strict allow rules:
```bash
claude -p "run tests and report" \
  --permission-mode dontAsk \
  --allowedTools "Read,Glob,Grep,Bash(npm test)" \
  --output-format json
```

**Security analogy:** Think of it as escalating clearance levels for a building contractor:
- **default** = Escort required everywhere (ask for each door)
- **acceptEdits** = Badge for office floors (files ok, commands still ask)
- **plan** = Security briefing + mission approval (approve the whole plan at once)
- **auto** = Smart badge system (the system decides per-door based on risk)
- **dontAsk** = Pre-approved work order (only the rooms on the list)
- **bypass** = Master key (only inside a sealed test facility!)

### Sandboxing Options

Claude Code offers multiple levels of isolation. Understanding these layers is critical for running autonomous agents safely.

**Level 0: OS-Level Sandbox (Built-in)**

Claude Code has native OS-level sandboxing for the `Bash` tool:

| Platform | Technology | What it restricts |
|----------|-----------|------------------|
| macOS | Seatbelt profiles | Filesystem paths, network access |
| Linux/WSL2 | bubblewrap (bwrap) | Filesystem paths, network access |
| Windows | (limited) | Relies on permission system |

Toggle with `/sandbox` in session. Applies to `Bash` + child processes only (not to Read/Write/Edit tools). Reduces permission prompts by ~84% according to Anthropic.

**Level 1: Git Worktrees (Lightweight)**

Separate working directory, same filesystem.
Protects main branch from agent mistakes.
Near-zero overhead. Use `claude --worktree` or `/batch`.

**Level 2: Docker / Inception (Full OS-Level)**

Separate filesystem, separate network, separate process space.
Run generated code without risk to host.
`multi-model-orchestrator:inception` (:wrench: custom plugin) automates setup and cleanup.

**Level 3: Remote Sandboxes**

Full VM per agent.  Complete network isolation.
For analyzing genuinely dangerous code samples.

### Data Retention & Privacy

Understanding data handling is essential for enterprise deployment and compliance:

| Plan | Training on your data? | Retention period | Notes |
|------|----------------------|-----------------|-------|
| **Free/Pro/Max** | Opt-in | Opt-in: 5 years, Opt-out: 30 days | Consumer plans |
| **Team/Enterprise** | No (by default) | 30 days | Commercial plans |
| **Enterprise + ZDR** | No | 0 days | Zero Data Retention (some features disabled) |

**Telemetry opt-out:**
```bash
export DISABLE_TELEMETRY=1           # No operational metrics (Statsig)
export DISABLE_ERROR_REPORTING=1     # No error logging (Sentry)
```

**Network:** Prompts and outputs are transmitted via TLS. According to docs, data is "not encrypted at rest."

**Security analogy:** This is like your CCTV retention policy. Consumer = 30-day loop with opt-in archive. Enterprise = 30-day loop, no sharing. ZDR = cameras on but no recording — maximum privacy, but you lose playback capability.

### Known Vulnerabilities (Teaching Examples)

Real CVEs relevant to understanding the security model:

- **CVE-2025-53110:** Path traversal in MCP server validation. The `startsWith()` check could be bypassed with `../` sequences. Fixed with strict path normalization. Lesson: never trust simple string matching for path validation.
- **Supply chain risk (research paper, March 2026):** Academic analysis of agent skill marketplaces found abandoned repos that could be hijacked, injecting malicious skills into unsuspecting users' environments.

These are excellent teaching examples for your security-focused audience — real-world demonstrations of why the permission system and plugin vetting matter.

---

### Trust Boundaries in Practice

| Boundary | Mechanism | What It Prevents |
|---|---|---|
| Tool permissions | `allowed_tools` in agent definition | Explorer cannot modify files |
| Filesystem | Worktree isolation | Agent cannot touch main branch |
| OS-level | Docker / Inception | Agent cannot reach host filesystem |
| Process | Hooks blocking actions | Specific commands blocked entirely |
| Network | Docker network none flag | Agent cannot make outbound connections |

The principle of least privilege applies to agents exactly as it applies to user accounts.
This is not optional hardening — it is the default design.

---

## Module 3.4: Scheduled Tasks, Loops & Automation

### Cronjobs with `/schedule`

Remote agents that run on cron schedules — even when your laptop is closed.

The `/schedule` skill walks you through:
1. What task to run (skill, command, or custom prompt)
2. When to run it (cron syntax or natural language: "every day at 8am")
3. What context to pass

**Practical scheduled tasks:**

| Task | Schedule | What It Does |
|---|---|---|
| Quality gate | Daily 06:00 | Test suite and code review on main branch |
| Security scan | Daily 02:00 | Devil's Advocate swarm on changed files |
| Dependency check | Weekly Monday | Scan all dependencies for new CVEs |
| Credential scan | Every commit | Detect accidentally committed secrets |
| Performance regression | After every deploy | Compare response times to baseline |

---

### Loops with `/loop`

Runs a command on a recurring interval in your current session:

```
/loop 5m /quality-gate
```

Runs the quality gate every 5 minutes.  Useful during active development.

**Loops vs. Schedules:**
- **Loops:** live in your terminal, stop when you close the session
- **Schedules:** persist across sessions, fire even when you are offline

---

### Self-Improve Loops

> **:wrench: Custom Component:** `agentic-os` is a custom plugin, not part of Claude Code.

The `/agentic-os:run-loop` skill implements **autonomous self-improvement cycles**.

**Each iteration:**

1. **Analysis** — reads quality metrics, test failures, review findings, iteration history
2. **Planning** — designs a targeted fix for the highest-impact weakness
3. **TDD Implementation** — writes failing test first, then code to pass it
4. **Quality Gate** — tests pass?  Quality above threshold?  No regressions?
5. **Commit (only if gate passes)** — git commit with detailed iteration log
6. **Repeat** — next iteration starts with updated state

**Safety mechanisms:**

| Mechanism | What It Does |
|---|---|
| Quality Gates | Refuses to commit if tests fail or quality drops |
| Git history | Every iteration is a reversible commit |
| Hooks | Block specific dangerous operations |
| Human review gates | Pause and wait for approval before committing |
| Max iterations | Cap autonomous runs per session |

**The Agentic OS memory system records everything:**
- `.agent-memory/iterations/` — full log of every change and decision
- `.agent-memory/quality/` — quality scores over time
- `.agent-memory/learnings/` — patterns identified across runs

**Validated results:** 7 consecutive runs with zero regressions.
6 fixes applied in 2 iterations in one documented session.
Later iterations were faster — the memory system prevented repeated mistakes.

**Security analogy:** Your access control system runs overnight diagnostics.
It detects a door reader responding 300ms slower than baseline.
It identifies the cause (outdated firmware).
It applies the update.
It verifies the door responds correctly.
It logs everything.
It continues patrol.
No human involvement.  Full audit trail.

---

## Module 3.5: Telegram Bridge, Inception & Worktree Isolation

### Telegram Bridge — Claude in Your Pocket

1. Send a Telegram message: "Run a security audit on the checkout service"
2. The bridge receives it and dispatches a Claude Code task
3. Claude executes the full workflow — can spawn multiple agents, run scans, write reports
4. Results arrive in your Telegram chat

**Why this matters:**
- Security incident at 2am?  Trigger a full investigation from your phone without opening a laptop
- On call?  Claude monitors and reports while you are in transit
- Full remote SOC access to your automated workflow — from your pocket

---

### Inception — Claude Code Inside Docker

`/multi-model-orchestrator:inception` runs Claude Code inside a Docker container with full isolation.

**Why:**

| Scenario | Why Inception |
|---|---|
| Running generated code | Generated code might be dangerous.  Run it inside. |
| Untrusted repositories | Analyze without exposing your host system |
| Parallel isolated tasks | Each instance has its own filesystem, no conflicts |
| Compliance requirements | Full network and filesystem audit logging |
| Destructive operations | Restructure, delete, format — with clean rollback |

**Containment chamber analogy:**
You want to know if this suspicious device is dangerous?
You do not open it in the lobby.
You put it in the containment chamber, examine it remotely.
If it explodes, the lobby is fine.

---

### Worktree Isolation

Git worktrees: separate working directories sharing the same git history,
with independent file states.

```bash
git worktree add ../agent-task-1 -b agent/task-1

# Agent works in ../agent-task-1
# Your main directory is completely untouched
# Changes are in git but isolated to that branch

git merge agent/task-1               # Accept
git worktree remove ../agent-task-1  # Or discard entirely
```

**Benefits:**
- Lightweight — no Docker overhead, instant setup
- Multiple agents work in parallel on the same repo without conflicts
- Each agent has its own branch — clean merge or clean discard

---

### The Full Architecture

```
You (Phone / Telegram)
    |
    v
Telegram Bridge
    |
    v
Claude Code -- Orchestrator
    |
    +------------------+------------------+
    |                  |                  |
    v                  v                  v
Agent 1 (Explorer)  Agent 2 (Tests)  Agent 3 (Docs)
Worktree A          Worktree B       Worktree C
Read-only           Read+Write       Write only
    |                  |                  |
    +-------results----+------------------+
                       |
                       v
             Agent 4 (Implementer)
             Worktree D
                       |
                       v
        Agent 5 -- Devil's Advocate Swarm
        Worktree E (adversarial)
             +--------+--------+
             v                 v
        Scanner 1         Scanner 2
        Security          Quality
             +--------+--------+
                      |
                      v
               Debate Agents
         Prosecutor vs. Defender
                      |
                      v
              Consensus Agent
                      |
              CONFIRMED only
                      |
                      v
              Fixer Agents
                      |
         +------------+
         v
Orchestrator aggregates all results
         |
         v
Report sent to Telegram
```

Every piece of this architecture exists and works today — though several components
(:wrench: marked above) are custom implementations built for this workshop, not
out-of-the-box Claude Code features.

---

### The Security Analogy — Complete Picture

| Claude Code Concept | Physical Security Equivalent |
|---|---|
| Orchestrator | SOC (Security Operations Center) |
| Specialized agents | Teams with defined roles and access |
| Worktrees | Locked rooms within the same building |
| Inception / Docker | Containment / isolation chamber |
| Telegram Bridge | Remote SOC access from mobile device |
| Devil's Advocate Swarms | Automated pentest with built-in tribunal |
| Quality Gates | Compliance checklist before sign-off |
| Self-Improve Loop | Continuous autonomous security hardening |
| Scheduled Tasks | Automated patrol routes |
| Hooks | Standing orders requiring authorization |
| Agent memory | Incident logs and after-action reports |

**The complete picture:**
You have built a Security Operations Center for your software.
It monitors continuously.
It investigates automatically.
It dispatches specialists.
It isolates risky work.
It runs adversarial tests against itself.
It patches confirmed findings.
It keeps a full audit trail.
It reports to you from your phone, at 2am, while you are asleep.

---

*End of Block 3 Teaching Material*
