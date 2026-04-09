# Block 1: Foundations — Teaching Content

**Audience:** Experienced programmers. Security analogies used throughout — especially relevant for the CySec engineer in the group.
**Duration:** ~90 minutes
**Goal:** Participants understand what Claude Code is, how context and memory work, how to write effective prompts, and how to use git integration.

---

## Module 1.1: What is Claude Code?

### Overview

Claude Code is not a chat interface. It is a command-line tool that gives an AI agent full, active access to your development environment. Understanding the distinction between Claude's different interfaces is the first mental model to establish.

---

### The Three Interfaces

**1. claude.ai (Web Chat)**

The browser-based chat you may already know. You type, Claude responds. You can share files by uploading them manually. Claude reads and reasons but has no ability to execute code, write files, or interact with your system directly.

This is a consultation interface. Claude gives you advice and you implement it yourself.

**2. Claude Code CLI**

Installed as a terminal command (`claude`). When you run it, you get an interactive session where Claude has:

- Full read/write access to your filesystem (within your working directory)
- The ability to run shell commands on your behalf
- Git integration (commits, branches, PRs)
- Web search capability
- Access to external tools via MCP (Model Context Protocol)
- The ability to spawn sub-agents for complex parallel tasks
- Persistent memory across sessions

This is not a chat interface. This is an agent operating in your environment.

**3. Desktop App & Web App**

Claude Code is also available as a **Desktop App** (Mac/Windows) and a **Web App** at claude.ai/code. Both provide the same agent capabilities as the CLI — file access, command execution, git integration — but with a graphical interface. Useful for people who prefer a GUI over a terminal.

**4. IDE Extensions (VS Code, JetBrains)**

An IDE extension that runs Claude Code as a sidecar chat panel in your editor. It has access to your open files and project context, and can make edits directly. Think of it as a senior colleague sitting next to you who can see your screen and make changes when you ask — but it's not passively watching everything you type in real time.

This is a pair programming interface.

---

### Security Analogy: The Three Consultant Modes

Your participants deal with physical security every day. This analogy maps cleanly onto what they already know.

**claude.ai = Security Consultant on the Phone**

You call a security expert. They listen to your description of the building, the lock placement, the camera angles. They give you advice. They might say "your server room door should have a card reader AND a PIN pad." But they never touch anything. They have no physical access. Whatever they say, you go implement it yourself.

Capability: advice, analysis, review — zero physical footprint.

**Claude Code CLI = Consultant with Badge Access**

Same expert, but now they have a visitor badge and an escort has walked them through the building. They can open doors, physically inspect locks, review wiring in the comms room, pull the access log off the controller, make configuration changes on the panel. They operate in your environment, under your supervision, with real effects.

Capability: reads your files, writes your files, runs your commands, commits your code, creates your branches.

**Desktop/Web App = Consultant in Your Office with a Nice Desk**

Same expert, same badge access, but now sitting at a proper desk with a monitor instead of standing at a terminal. The work is the same — file access, commands, git. The interface is just more comfortable. Some consultants prefer a standing desk (CLI), others prefer a chair (Desktop App).

Capability: identical to CLI — different wrapper, same engine.

**IDE Extension = Consultant Sitting Next to You**

Same expert, at a desk next to yours. They can see your editor, your open files, your project structure. When you ask, they make changes directly. They don't passively watch every keystroke, but they have full context of what you're working on and can jump in when asked.

Capability: file context, direct edits, project-aware suggestions.

---

### Boris Cherny's Philosophy

Boris Cherny is the original creator of Claude Code. His design philosophy is deliberately anti-prescriptive:

> "Claude Code is a power tool. There's no one right way to work with it. Everyone uses it differently for their tasks. You have to figure out what works best for you."

This matters because it sets the right expectation for the workshop: we are not teaching "the correct way to use Claude Code." We are teaching you the mechanics, the mental models, and a range of patterns. What you build from those is yours.

Compare this to a circular saw: the manual tells you how the blade works, the safety features, the feed rate. But how you use it to build your project is your decision. The tool does not constrain your creativity.

---

### What Claude Code Can Do

- **Read codebases**: Traverse directories, read files, understand entire projects at once
- **Write and edit files**: Create new files, modify existing ones, refactor across multiple files
- **Run commands**: Execute shell commands, run test suites, build projects, start services
- **Manage git**: Stage, commit, branch, merge, push, create pull requests — all from conversation
- **Search the web**: Research documentation, find packages, look up error messages
- **Orchestrate agents**: Spawn parallel sub-agents for independent tasks
- **Connect via MCP**: Integrate with external tools — databases, APIs, monitoring systems, GitHub, Slack
- **Remember across sessions**: Persist context using CLAUDE.md and the memory system

### Built-in Tools Reference

Claude Code works through **tools** — each capability has a specific tool name. These names matter for permissions, hooks, and agent configuration:

| Tool | What it does | Needs permission? |
|------|-------------|-------------------|
| `Read` | Read files | No |
| `Glob` | Find files by pattern | No |
| `Grep` | Search file contents | No |
| `Edit` | Modify files (targeted replacement) | Yes |
| `Write` | Create/overwrite files | Yes |
| `NotebookEdit` | Edit Jupyter notebooks | Yes |
| `Bash` | Execute shell commands | Yes |
| `WebSearch` | Search the web | Yes |
| `WebFetch` | Fetch URL content (isolated context) | Yes |
| `LSP` | Code intelligence via Language Server | No (setup needed) |
| `Skill` | Invoke a skill | Yes |
| `Agent` | Spawn a subagent | No |

**Security analogy:** Each tool is like a specific card-access zone. `Read` is the lobby — everyone gets in. `Bash` is the server room — you need explicit clearance. When you configure permissions (allow/deny rules) or hook matchers, you use these exact tool names.

---

### Model Selection & Cost Awareness

Claude Code supports multiple models. Choosing the right one matters for both quality and cost:

| Model | Context | Strengths | Cost |
|-------|---------|-----------|------|
| **Claude Opus 4.6** | 1M tokens | Deep reasoning, architecture, complex tasks | Highest |
| **Claude Sonnet 4.6** | 1M tokens (beta) | Fast, capable, everyday coding | Medium |
| **Claude Haiku 4.5** | 200K tokens | Quick tasks, brainstorming, bulk operations | Lowest |

**How to switch:**
- At startup: `claude --model sonnet`
- In session: `/model` command
- Effort level: `/effort high` for deep thinking, `/effort low` for quick answers
- Check spend: `/cost` shows token usage and cost for the current session
- Check context: `/context` visualizes how much of the context window is used

**Cost guidance:** Average ~$6/dev/day with Sonnet 4.6. Roughly $100-200/month per developer (varies heavily with usage). Reduce costs with: Skills instead of long CLAUDE.md, Subagents for isolation, `/compact` proactively, Sonnet for routine work, Haiku for bulk operations.

**Rule of thumb:** Start with Opus for planning and architecture. Switch to Sonnet for implementation. Use Haiku for bulk reads and simple tasks. Use `/cost` regularly to stay aware of spend.

---

### Permission System

Claude Code has a built-in permission system that controls which tools it can use. This is security through least privilege — a concept your participants already know well.

**Permission modes (6 levels, set via `claude --permission-mode <mode>` or `/permissions`):**

| Mode | Behavior | Analogy |
|------|----------|---------|
| **default** | Only reads allowed, everything else asks for approval | Visitor badge — lobby access only |
| **acceptEdits** | Reads + file edits allowed, bash still asks | Maintenance badge — utility rooms too |
| **plan** | Shows full plan upfront, approves all steps at once | Security briefing — approve the mission |
| **auto** | ML classifier decides risk level (Team/Enterprise only) | Smart badge — system decides per door |
| **dontAsk** | Never prompts — relies entirely on allow/deny rules | Automated system — rules only, no guard |
| **bypassPermissions** | Accepts everything (DANGER — isolated VMs only!) | Master key — no locks at all |

**Allow/deny rules in `settings.json`:**
```json
{
  "permissions": {
    "allow": ["Read", "Glob", "Grep", "Bash(npm test)"],
    "deny": ["Bash(rm *)", "Bash(curl*)"]
  }
}
```

**Allowlists via CLI flag:** `claude --allowedTools "Read,Glob,Grep"` — only these tools are available.

**Security analogy:** This is card access clearance levels. A visitor badge gets you through the lobby but not the server room. A maintenance badge opens utility closets but not the executive floor. Each Claude session has a clearance level — you decide what it can access. The permission mode sets the *default* clearance, and allow/deny rules fine-tune individual doors.

---

### What Claude Code Cannot Do

- **Access your screen directly**: It cannot see your GUI, your browser, your monitor display. Only what it can read from the filesystem or run as commands. *(Note: Since March 2026, Computer Use allows Claude to control the desktop on macOS — mouse, keyboard, screenshots. This is a separate feature and not enabled by default.)*
- **Run persistent services**: It can start a server but does not maintain background processes between sessions.
- **Access private networks without configuration**: VPN, internal APIs, on-prem systems require explicit MCP configuration or tunneling.
- **Make production decisions without approval**: Best practice is always human approval for anything touching production. Claude Code does not push to production by itself.
- **Know what it doesn't know**: Like any LLM, it can be confidently wrong. Always verify critical outputs.

---

### Practical Implication for Physical Security Developers

Your domain involves:
- Firmware for access controllers
- Integration protocols (OSDP, Wiegand, RS-485)
- Event log parsing and alarm correlation
- Configuration management for large panel installations
- Regulatory compliance in safety-critical systems

Claude Code can read your controller configuration files, understand your protocol implementations, write parsers for your event log formats, generate test harnesses for alarm state machines, and help you navigate compliance requirements. But it does not touch your live panels. That boundary is yours to enforce.

---

## Module 1.2: Context & Memory

### Overview

Claude Code does not have unlimited awareness of your project. It has a context window — a finite working memory. Understanding how this works, and how to manage it, is essential for reliable sessions.

---

### The Context Window

The context window is Claude's active memory for a session. Everything Claude "knows" during your conversation — your files, your instructions, your conversation history, the results of commands it ran — lives in this window.

**Size:** Up to 1 million tokens for Claude Opus (roughly 750,000 words, or about 5,000 pages of text). This sounds enormous, but a large codebase with many files can fill it quickly.

**What goes in:**
- Your messages and Claude's responses
- Files Claude reads
- Command output (stdout/stderr)
- CLAUDE.md contents
- Memory items loaded at session start

**What happens when it fills:**
Auto-compression. Claude summarizes the oldest parts of the conversation. The summary replaces the full history. Detail is lost. This is why having a persistent external memory (CLAUDE.md, memory files) matters — they are reloaded fresh each session.

---

### Security Analogy: The Control Room with Limited Monitors

Your security operations center has a wall of monitors. Each shows a camera feed. But you have 200 cameras and only 20 monitor slots.

When a new camera feed is critical — say, an active alarm at Door 47 — you bring it up. But you only have 20 slots. One of the older feeds gets archived. The operator can still request the archived feed, but it takes time and effort to pull it back up.

Claude's context window works the same way. Active information is on the monitors. Old information gets archived (compressed). You can reference it again, but with less fidelity than when it was live.

The implication: **don't rely on Claude remembering details from early in a long session.** If something is important, write it down (CLAUDE.md, a file, a note). Make Claude reread it if it's critical.

---

### CLAUDE.md: Your Project Access Policy

CLAUDE.md is a Markdown file that Claude Code reads automatically at the start of every session. Think of it as the standing orders, the briefing document, the project access policy.

**Two levels:**

- `./CLAUDE.md` — project-level, checked into the repo, applies to this project
- `~/.claude/CLAUDE.md` — user-level, applies to all your Claude Code sessions

**What to put in CLAUDE.md:**
- Technology stack and versions
- Coding conventions (naming, formatting, test framework, lint rules)
- Project-specific terminology and domain knowledge
- What not to do (e.g., "never use global state", "never modify the legacy parser")
- Deployment notes, key file locations
- Contact info or escalation notes if relevant

**What not to put in CLAUDE.md:**
- Secrets (API keys, passwords) — these should never be in plaintext files
- Temporary task context — use the conversation for that
- Lengthy documentation — keep it concise, Claude reads it every session

---

### Security Analogy: CLAUDE.md as Access Policy

In physical security, a new contractor arriving on site is handed the site access policy document: which areas are authorized, which are off-limits, what procedures to follow if an alarm triggers, who to contact. They read it before they start work.

CLAUDE.md is exactly this. Every session, Claude reads the policy before doing anything. If the policy says "always run pytest before committing," Claude will always run pytest before committing. If it says "never modify the legacy firmware parser," Claude will treat that as a hard boundary.

You write the policy once. Claude follows it every session, without being reminded.

---

### The Memory System

Beyond CLAUDE.md, Claude Code has a structured memory system stored in `~/.claude/projects/*/memory/`. This allows Claude to remember things you tell it explicitly across sessions.

**Memory types:**

- **User preferences**: "I prefer German for communication but English for code." Stored and applied every session.
- **Feedback**: "Last time you refactored the parser this way, it broke the alarm correlation module. Note this for future." Stored as a lesson.
- **Project context**: "This project uses a custom OSDP variant — see osdp-custom-spec.md for deviations." Stored as reference.
- **Reference**: Frequently needed but session-specific information like table schemas, API endpoint lists.

**How to create memories:**
Say: "Remember that..." or "Note for future sessions that..."
Claude will store this in the memory system and load it at the start of future sessions.

---

### Context Compression: Why It Happens and How to Handle It

When the context fills, Claude auto-compresses. The compression:
- Summarizes early conversation turns
- Preserves recent turns in full
- Loses fine-grained detail from older parts of the session

**Signs you're hitting compression:**
- Claude starts forgetting things it "knew" earlier
- Responses become slightly less precise about earlier decisions
- You see a "compressing context..." message

**How to handle it:**
1. Use `/compact` proactively before hitting the limit — it compresses with an optional focus hint: `/compact focus on the API changes`
2. Use `/context` to visualize how much context you've consumed
3. Keep important decisions in CLAUDE.md, not just conversation
4. If you notice drift, say "Reread the CLAUDE.md" or paste the key constraint again
5. For very long tasks, consider breaking them into multiple sessions — use `/export` to save conversation history, `claude -r <name>` to resume named sessions
6. Use memory items for things you need persistent across all sessions

### Key Context Commands

| Command | What it does |
|---------|-------------|
| `/context` | Visualize context window usage — shows how full your "monitor wall" is |
| `/compact [focus]` | Compress context proactively (optionally with a focus hint) |
| `/export [file]` | Export conversation to a file for reference |
| `/resume <name>` | Resume a named session with full context |
| `/memory` | Manage auto-memory and CLAUDE.md entries |
| `/init` | Generate or improve project CLAUDE.md (interactive flow) |
| `/rewind` | Rewind to a checkpoint — undo multiple steps at once |

---

### Best Practices Summary

| Practice | Why |
|---|---|
| Keep CLAUDE.md concise | It's read every session; dense docs slow startup |
| Use memory for cross-session info | CLAUDE.md is static; memory is dynamic |
| Reread important files before critical actions | Don't assume Claude remembers from 50 turns ago |
| Break long sessions into focused tasks | Avoids compression drift on complex work |
| Never put secrets in CLAUDE.md | Security hygiene; version control is not a secret store |

---

## Module 1.3: Effective Prompting

### Overview

Claude Code's capability is constant. The quality of your results varies entirely with how you communicate. This module builds the habits that separate "meh" results from "exactly right" results.

---

### Clarity Over Cleverness

The most common mistake with AI tools is being too vague in an attempt to let the AI "figure it out." This is the wrong mental model. Claude Code is not psychic. It works with what you give it.

**Vague:**
```
Fix the bug.
```

Claude does not know which bug. It might guess, make a change, possibly introduce a new issue, and you have no way to validate whether it fixed the right thing.

**Specific:**
```
Fix the null pointer exception in auth.py at line 45. It occurs when a user account
has no email address set. The function assumes email is always present but new accounts
created via LDAP import can have null email fields. Add a null check before accessing
user.email and return an appropriate error response.
```

Claude now knows: which file, which line, the root cause, the triggering condition, and the expected fix strategy. The output will be dramatically better.

---

### The Contractor Analogy

Prompting Claude Code is like writing a work order for a contractor who has full building access.

You would not hand a contractor a note that says "fix the door." You would write:

> "The card reader on the north server room door (asset ID CR-047) is not triggering the relay after a valid card swipe. The controller log shows the card is being read (event type 0x01) but no relay event (0x04) follows. Check the relay wiring on panel P-03, terminal block TB-6. If the relay tests good, check the controller config for door group assignment. Document findings."

This is how to write prompts. Specific. Contextual. Actionable. Expected output defined.

---

### Scope Control

Smaller, focused requests produce better results than broad ones.

**Too broad:**
```
Refactor the entire alarm processing module.
```

This gives Claude latitude to change things you didn't expect. You lose control of what changes.

**Better:**
```
Refactor the alarm_deduplicator.py file only. Current issue: it uses a list for O(n)
lookups. Replace with a set or dict for O(1) lookups. Do not change the function
signatures or the public interface. Tests are in tests/test_alarm_deduplicator.py —
make sure they still pass.
```

Scope is defined. What not to touch is explicit. Success criterion is stated.

---

### Iterative Work Pattern

For complex tasks, a four-step pattern works well:

1. **Explain**: Give Claude the context. "Here's what we're building, here's the current state, here's the constraint."
2. **Propose**: Ask Claude to propose an approach before implementing. "What's your plan for handling this?"
3. **Refine**: Review the plan. Correct misunderstandings. Add constraints. "Good, but use the existing logger, not print statements."
4. **Execute**: "Go ahead and implement."

This pattern prevents the scenario where Claude writes 300 lines of code in a direction you didn't want.

---

### Plan Mode

Claude Code has a built-in planning mode accessible via `/plan` or by pressing `Shift+Tab` before submitting your message.

In plan mode, Claude:
- Reads relevant files first
- Generates a structured implementation plan
- Lists the files it intends to change
- Asks for approval before writing a single line

Use plan mode for:
- Tasks touching more than 2-3 files
- Anything where you need to review the approach before code is written
- Refactors that span a module or subsystem
- Any task where mistakes would be expensive to undo

---

### Effective Prompting Patterns

These are battle-tested patterns to adopt immediately:

**"Read X first, then suggest"**
```
Read src/alarm_correlator.py and src/event_parser.py first. Then suggest
how we should add support for zone-group correlation without breaking
the existing per-zone logic.
```
Forces Claude to ground its suggestions in actual code, not assumptions.

**"Show plan before implementing"**
```
Show me your implementation plan before writing any code. I want to
review the approach and the list of files you'll change.
```
Gives you a checkpoint before any changes happen.

**"Only change X, don't touch Y"**
```
Update the database connection pool settings in config/db.py.
Do not touch any other configuration files or the connection pool
implementation itself — only the settings values.
```
Explicit exclusions prevent unintended scope creep.

**"Test by running Z"**
```
After making changes, run pytest tests/test_auth.py -v and show me
the output before we move on.
```
Embeds verification into the task. You see test results before you commit.

**"Explain what you did"**
```
Explain the changes you made and why, in plain language. Then show
the diff.
```
Forces Claude to articulate its reasoning, making it easier for you to catch misunderstandings.

---

### Bad vs Good: Side-by-Side Examples

| Bad Prompt | Good Prompt |
|---|---|
| "Fix the code" | "Fix the off-by-one error in event_parser.py line 78. The loop should iterate from index 1, not 0, because the first byte is always the sync byte." |
| "Make it faster" | "The alarm_correlator.process() function takes 200ms per call with 1000 events. Profile it and optimize — the bottleneck is probably the nested loop in lines 45-67." |
| "Add tests" | "Write pytest unit tests for the IPv4 validator in validators.py. Cover: valid addresses, leading zeros (invalid), out-of-range octets, too few octets, non-numeric characters, empty string." |
| "Refactor this" | "Refactor the state machine in door_controller.py to use Python's enum module instead of integer constants. Keep all function signatures identical. Run the existing tests to confirm nothing broke." |

---

### What Not to Do

- **Do not chain unrelated tasks in one prompt.** "Fix the bug AND add the feature AND update the docs" splits Claude's focus and makes it hard to verify each piece.
- **Do not rely on Claude's memory for critical constraints.** If "never modify the legacy parser" matters, put it in CLAUDE.md.
- **Do not accept the first output blindly.** Ask Claude to explain its reasoning. Ask it to consider edge cases. Ask "what could go wrong with this approach?"
- **Do not use vague approval.** "That looks good" may cause Claude to proceed to a next step you didn't intend. Be explicit: "Looks good, go ahead and implement" vs "Looks good, stop here."

---

## Module 1.4: Git Integration & Worktrees

### Overview

Claude Code has native git integration. This means you can manage your entire version control workflow — branching, committing, pushing, creating pull requests — without leaving the Claude Code session. This module covers the workflow and introduces git worktrees for parallel development.

---

### Built-in Git Capabilities

Claude Code can execute git operations as naturally as it writes code:

- `git status` — check what's changed
- `git diff` — review changes before committing
- `git add` — stage specific files or all changes
- `git commit` — create commits with meaningful messages
- `git branch` / `git checkout -b` — create and switch branches
- `git push` — push to remote
- `git log` — review commit history
- `gh pr create` — create GitHub pull requests (requires GitHub CLI)

You can ask for these in plain language:

```
Create a branch called feature/alarm-dedup, implement the deduplication
change we discussed, commit it with a good message, and push it.
```

Claude handles the git mechanics. You review the diff and approve.

### Git-Related Slash Commands

| Command | What it does |
|---------|-------------|
| `/diff` | Interactive diff viewer — see all changes at a glance |
| `/rewind` | Rewind to a checkpoint — undo multiple steps, not just the last edit |
| `/pr-comments <nr>` | Fetch PR comments from GitHub directly into the session |
| `/commit` (skill) | Structured commit with conventional format |

`/diff` is especially useful before committing — it gives you a visual overview of everything Claude changed, so you can catch issues before they enter your git history.

`/rewind` is the "undo" for multi-step changes. If Claude made 5 edits and the last 3 went wrong, `/rewind` lets you go back to a specific checkpoint without manually reverting each file.

---

### The Full PR Workflow in One Flow

Here is a complete feature development cycle as a single Claude Code session:

1. **Start with context**: "We're adding IPv4 validation to the access controller API. Read the existing validators.py to understand the current pattern."
2. **Create the branch**: "Create a branch called feature/ipv4-validation."
3. **Implement**: "Implement the IPv4 validator following the existing pattern. Include input sanitization and tests."
4. **Review**: "Show me the diff."
5. **Test**: "Run pytest and show me the results."
6. **Commit**: "Commit with message: 'Add IPv4 validation to access controller API with full test coverage'"
7. **Push and PR**: "Push the branch and create a pull request with a description of what this does and why."

This entire flow happens in conversation. No terminal window switching, no copy-pasting commit messages, no manual `git push` after forgetting to add `-u origin`.

---

### Git Worktrees: Parallel Development Without the Risk

A git worktree is a separate working directory that shares the same git repository. You can have multiple worktrees checked out to different branches simultaneously.

**Why this matters:**

Imagine you are working on a major refactor in branch `refactor/alarm-correlator`. A critical bug is reported in production. Normally you would:
- Stash your refactor changes
- Switch branch
- Fix the bug
- Commit and push
- Unstash
- Continue refactor

With worktrees:
- Your refactor branch stays exactly where it is in its directory
- You create a new worktree for the hotfix
- Fix the bug in the hotfix worktree
- Commit and push from there
- Return to your refactor worktree unchanged

The two branches coexist as separate directories. No stashing. No context switch in your working tree.

---

### Security Analogy: Worktree as Test Lab

In physical security, when you need to test a new firmware version on a controller, you don't flash it on the live system first. You have a test bench — a replica of the production setup in a separate room. You flash the test bench, run your tests, confirm behavior, then schedule the production update.

A git worktree is your software test bench. It's a separate room with the same equipment, completely isolated from the live system. You can break things in the test bench without affecting production. When you're confident, you merge.

```
# Create a worktree for an experiment
git worktree add ../experiment-async-processing feature/async-experiment

# Now you have:
# ./                          (main branch, ongoing work)
# ../experiment-async-processing   (experiment branch, isolated)
```

---

### Key Git Commands in Claude Code

**Check status before anything:**
```
What's the current git status?
```

**Create a branch:**
```
Create a new branch called feature/zone-group-correlation
```

**Stage and commit:**
```
Stage all changed files and commit with message:
"Add zone-group correlation support to alarm correlator"
```

**Review before committing:**
```
Show me the full diff of what would be committed. I want to review before we commit.
```

**Create a worktree:**
```
Create a worktree at ../alarm-refactor-experiment on a new branch
called experiment/alarm-refactor so I can test this approach in isolation
```

**Push and PR:**
```
Push this branch and create a GitHub PR. Title: "Add zone-group correlation".
Description should explain that this adds support for correlating alarms
by zone group, not just individual zones, and that tests cover the
3 new correlation patterns.
```

**The /commit skill:**

If you have the commit skill installed, `/commit` triggers a structured commit workflow: review diff, generate conventional commit message, confirm, commit. Useful for maintaining consistent commit message style.

---

### Worktrees in Practice: When to Use Them

| Scenario | Use Worktree? |
|---|---|
| Hotfix while feature branch is in-progress | Yes — keep feature branch untouched |
| Comparing two approaches side-by-side | Yes — run both simultaneously |
| Risky refactor you might want to abandon | Yes — keep main branch clean |
| Normal feature development | No — a single branch is fine |
| Running two different test configurations | Yes — each worktree has its own working state |

---

### Summary: Block 1 Key Takeaways

1. **Claude Code is an agent, not a chat tool.** It has real access to your filesystem, your git, your commands.
2. **The context window is finite.** CLAUDE.md and memory exist to persist what matters beyond a session.
3. **Specificity is everything in prompts.** Vague in → vague out. Contractor analogy: write a work order, not a wish.
4. **Git is built in.** Branch, commit, push, PR — all from conversation.
5. **Worktrees are your test lab.** Parallel branches, isolated environments, no stashing drama.

---

*End of Block 1 Teaching Content*
