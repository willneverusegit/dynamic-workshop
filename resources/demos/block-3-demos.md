# Block 3: Demo Scripts for Moderator

> Live demos for the afternoon advanced block.
> Each demo has a clear talking point — the security analogy that ties it to the audience's world.

---

## Demo 3.1: Multi-Agent Orchestration

**Goal:** Show two agents running simultaneously on independent tasks.

### Setup

Have a mid-sized project open (the workshop demo project or any real codebase with 10+ files).

### Steps

**Step 1: Ask Claude to analyze from two angles in parallel**

Tell Claude (type this out loud so the audience sees the prompt):

> "Analyze this project using two separate agents running in parallel:
> Agent 1 maps the overall architecture — directory structure, key files, main entry points, technology stack.
> Agent 2 scans for all TODO, FIXME, HACK, and XXX comments and lists them with file and line number.
> Run both agents simultaneously and give me a combined report."

Watch the two agent threads appear in the output.
Point out that both are running at the same time — not waiting for each other.

**Step 2: Show /agent-orchestrator for structured multi-model work**

```
/agent-orchestrator
```

Select: Haiku for brainstorming, Sonnet for analysis.
Task: "Generate 5 architectural improvement ideas for this project, then evaluate each one for feasibility and impact."

Show how Haiku generates ideas quickly (fast, cheap), Sonnet analyzes them with more depth.

### Talking Point

"We just dispatched two security teams to sweep different floors simultaneously.
One team is mapping the building layout, the other is checking for hazards.
They report back independently — we get both reports in parallel, not sequentially.
This is exactly how a well-run SOC operates."

---

## Demo 3.2: Codex Swarm

**Goal:** Show multi-model pipeline — Claude plans, Codex builds in parallel, Claude reviews.

### Prerequisite

Codex CLI must be installed and authenticated.
Verify with: `codex --version`

### Steps

**Step 1: Launch Codex Swarm with decompose**

```
/multi-model-orchestrator:codex-swarm --decompose
```

When prompted for the task, enter:

> "Build a Python CLI security tool with three commands:
> 1. scan: given a hostname, attempt connections to ports 21, 22, 23, 25, 80, 443, 3306, 5432, 8080, 8443 and report which are open
> 2. check: given a URL, make an HTTP GET request with a 5-second timeout and report the status code and response time in milliseconds
> 3. report: run both scan and check on a given target and output a JSON report with timestamp, target, open ports, and HTTP status
> Include a CLI entry point using argparse, proper error handling, and a test file."

**Step 2: Watch decomposition**

Point out that Claude is analyzing the task and breaking it into independent subtasks before spawning any Codex agents.
Name each subtask as Claude identifies it.

**Step 3: Watch parallel execution**

Show the N Codex agents spinning up simultaneously.
Highlight that they are working in parallel — the timer is running for all of them at once.

**Step 4: Claude reviews**

Watch Claude read through all generated files.
Point out what it catches — integration issues, missing error handling, test coverage gaps.

### Talking Point

"This is the architect-contractor-inspector model.
Claude Opus designed the spec.
Codex agents built the components in parallel — like contractors installing readers on different floors at the same time.
Claude Opus is now inspecting each component before sign-off.
Three different intelligence profiles.  One pipeline.  The result is better than any one of them alone."

---

## Demo 3.3: Devil's Advocate — Adversarial Security Testing

**Goal:** Show automated penetration testing pipeline.  This demo is the highlight for the CySec audience.

### Setup

Prepare a small demo project with intentional vulnerabilities.
Create `demo_vulnerable.py`:

```python
import subprocess
import sqlite3

def run_diagnostic(device_name):
    # Intentional vulnerability: shell injection via unvalidated input
    result = subprocess.run(f"ping -c 1 {device_name}", shell=True, capture_output=True, text=True)
    return result.stdout

def get_device_status(device_id):
    conn = sqlite3.connect("devices.db")
    cursor = conn.cursor()
    # Intentional vulnerability: SQL injection via string formatting
    query = "SELECT * FROM devices WHERE id = " + device_id
    cursor.execute(query)
    return cursor.fetchall()

def load_config():
    # Intentional vulnerability: hardcoded credential
    api_key = "sk-prod-abc123xyz789"
    return {"api_key": api_key}
```

### Steps

**Step 1: Run the swarm**

```
/devil-advocate-swarms:swarm
```

Point at `demo_vulnerable.py`.

**Step 2: Watch Stage 1 — Scanners**

Two scanners run in parallel.
Name what each is looking for.
Point out: they will both find the injection issues — overlap = validation.

**Step 3: Watch Stage 2 — Debate**

This is the most important part.  Slow down and explain what is happening:
- Prosecutor is arguing each finding as if writing an exploit report
- Defender is looking for reasons it is not exploitable
- For the hardcoded credential: Defender has no good argument.  Confirmed.
- For the shell injection: same.  Confirmed.
- Point out if any finding gets argued away — that is the system saving engineer time.

**Step 4: Watch Stage 3 — Consensus**

Show the CONFIRMED vs FALSE POSITIVE split.
Each confirmed finding has a full audit trail — the debate transcript is the evidence.

**Step 5: Watch Stage 4 — Fixers**

Show the fixes being applied.
Each fix is minimal and targeted.
Each has a regression test.

**Step 6: Show the planted vulnerabilities were all found**

Scroll through findings.  The three planted issues should all be CONFIRMED.

### Talking Point

"This is an automated penetration test with a built-in legal review.
The Prosecutor is your pentester writing the exploit report.
The Defender is your developer explaining what is and is not actually exploitable.
The Consensus agent is your security manager deciding what gets a CVE.
The Fixers are your patching team.
The entire thing just ran in under 5 minutes.
For the CySec engineers in the room — this is your world applied to code."

---

## Demo 3.3b: Permission Modes — From Visitor to Master Key (~5 minutes)

### Goal
Show the 6 permission modes live. Demonstrate how the same task behaves differently under different clearance levels.

### Steps

**Step 1: Show default mode (1 min)**

Start Claude Code normally (default mode). Ask:
```
Show me the contents of package.json
```
This works without asking — reads are always allowed.

Now ask:
```
Add a comment to the top of README.md
```
Claude asks for permission. Say: *"Default mode. Visitor badge. Reading is free, writing needs approval."*

**Step 2: Switch to acceptEdits (1 min)**

```
/permissions
```

Switch to `acceptEdits`. Now ask:
```
Add a comment to the top of README.md
```
This time it goes through without asking. But:
```
Run npm test
```
Still asks. Say: *"Maintenance badge. You can open office doors but the server room still needs authorization."*

**Step 3: Show plan mode (2 min)**

Start a new session with:
```bash
claude --permission-mode plan
```

Ask:
```
Refactor this file to use async/await instead of callbacks, add error handling, and run tests
```

Claude shows the full plan first. One approval covers all steps.

Say: *"Plan mode is the security briefing. You approve the whole mission, not each step. Useful for complex multi-step tasks where constant approving is exhausting."*

**Step 4: Mention dontAsk and bypass (1 min)**

Don't demo these live (too dangerous for live demo). Just explain:
- `dontAsk` = pre-approved work orders for CI/CD, only allow-listed operations run
- `bypassPermissions` = master key, only inside sealed test facilities (Docker, sandbox)

Say: *"You would never give a contractor a master key in a live building. Same rule applies here. Bypass mode is for sealed test environments only."*

### Talking Point
*"6 clearance levels. From visitor badge to master key. You pick the right one for the situation — just like you'd never give a delivery driver the same access as the building engineer."*

---

## Demo 3.3c: CVE-Fix Pipeline — From Advisory to PR (~5 minutes)

### Goal
Show Claude fixing a real dependency vulnerability using web search + plan mode + automated PR. This is the "Research-to-Patch" blueprint.

### Steps

**Step 1: Set up a vulnerable dependency (before demo)**

In the demo project's `package.json` or `requirements.txt`, include a package with a known CVE (e.g., an older version of a popular library).

**Step 2: Ask Claude to fix it (3 min)**

```
/plan Find and fix any known CVEs in our dependencies.
Search the web for current advisories, identify the fix version,
update the lockfile, run tests, and create a PR.
```

Walk through what Claude does:
1. **WebSearch** — finds the advisory on NVD/GitHub
2. **Plan** — identifies the affected package, the fix version, and the migration path
3. **Edit** — updates the version in the dependency file
4. **Bash** — runs `npm install` / `pip install` and tests
5. **Git** — commits and creates a PR with the CVE reference

**Step 3: Show the PR description (1 min)**

Point out that Claude included:
- CVE ID and link to advisory
- What was vulnerable and why
- What was changed
- Test results

Say: *"From 'there's a CVE' to 'here's a PR with tests' — in under 3 minutes. This is vulnerability management at machine speed."*

### Talking Point
*"In your world, a vulnerability in a door controller firmware means: find the advisory, identify affected units, plan the update path, test on a bench, deploy, verify. Same process here — but Claude does steps 1 through 5 automatically. You review and approve."*

---

## Demo 3.4: Self-Improve Loop

**Goal:** Show a system that analyzes its own weaknesses and fixes them autonomously.

### Setup

Use a project with some test failures or quality issues.
The workshop demo project works well.

### Steps

**Step 1: Show the current state**

Run the quality gate first so the audience sees the baseline:
```
/quality-gate
```
Note the score and any failures.

**Step 2: Start the self-improve loop for 1 iteration**

```
/agentic-os:run-loop
```

When asked for iterations, enter `1`.
Walk through what is happening in each phase:
- Analysis: "Reading the quality gate results and iteration history"
- Planning: "Identifying the highest-impact issue to address"
- TDD: "Writing a failing test — watch, it should fail"
- Implementation: "Writing the minimum code to make the test pass"
- Quality gate: "Running the full check — this is the go/no-go decision"
- Commit: "Only happens if the gate passes — otherwise the iteration is discarded"

**Step 3: Show the iteration log**

```
cat .agent-memory/iterations/iteration-001.md
```

Walk through the log: what it analyzed, what it decided, what it changed, what it verified.

**Step 4: Run the quality gate again**

Show the score improved.

### Talking Point

"The system just improved itself.
It analyzed its own weaknesses, designed a fix, verified the fix with tests, confirmed quality did not regress, and committed the improvement.
To pull the security analogy one more time: this is a security system that ran its own penetration test, found a vulnerability, patched it, tested the patch, and logged the entire operation.
With no human involvement.
This is what continuous security hardening looks like when applied to software."

---

## Demo 3.5: The Full Stack — Architecture Discussion

**Goal:** Connect all the pieces.  Show the full vision.  Finish with the Telegram demo.

### Steps

**Step 1: Worktree isolation (live)**

```bash
git worktree add ../demo-sandbox -b demo/sandbox
```

Show that `../demo-sandbox` is a clean working copy.
Have an agent do something in the sandbox.
Show the main directory is untouched.
Discard it:
```bash
git worktree remove ../demo-sandbox
```

"The agent worked in a locked room.  We unlocked the door, checked the work, and locked it again."

**Step 2: Whiteboard — the full architecture diagram**

Draw on the whiteboard (or project the ASCII diagram from the module):
- Telegram Bridge -> Orchestrator
- Orchestrator -> specialized agents in worktrees
- One path leads to Devil's Advocate Swarm
- Results aggregate back to orchestrator
- Report sent to Telegram

Walk through each node and explain what it does and what its security boundary is.

**Step 3: Inception (if Docker is available)**

```
/multi-model-orchestrator:inception
```

Task: "List all Python files in /workspace and count the lines of code in each."

Show that Claude runs inside a container.  The container's filesystem is isolated from the host.

**Step 4: Telegram demo (if configured)**

Send a real message from your phone.
Read the response aloud when it arrives.
"I just triggered a full code analysis from my phone.
While I was walking to the coffee machine.
Claude is running agents, which are running tools, which are scanning files — all from that one message."

### Talking Point

"You have an always-available operations center in your pocket.
Every workflow we built today — the multi-agent scans, the adversarial security testing, the self-improving quality checks — all of it can be triggered from a Telegram message and reported back to your phone.
You do not need to be at a laptop.
You do not need to be awake.
Claude is on call."

---

*End of Block 3 Demo Scripts*
