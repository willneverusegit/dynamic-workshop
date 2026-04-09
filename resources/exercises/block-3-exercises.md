# Block 3: Exercises

> These exercises move from guided exploration to open architecture design.
> The final exercise — Architecture Discussion — is the goal of the entire workshop.
> Leave enough time for it.

### Exercise Priority Guide

| Priority | Exercise | Realistic Time |
|----------|----------|---------------|
| **Must-do** | 3.1 Multi-Agent Task | ~15 min |
| **Must-do** | 3.5 Architecture Discussion | ~45 min |
| **Should-do** | 3.3 Security Audit **OR** 3.4 Automation | ~30 min / ~20 min |
| **Nice-to-have** | 3.2 Codex Swarm (demo-only) | ~15 min |
| **Nice-to-have** | Bonus 3.3 HIPAA Hook | ~15 min |

> **Realistic total: 90–120 minutes.** Prioritize Must-do exercises first.

---

## Exercise 3.1: Your First Multi-Agent Task

**Type:** Individual, ~10 minutes
**Goal:** Experience the difference between single-instance and multi-agent Claude.

### Task

Think of a task in your current work that has 2-3 clearly independent parts.

Examples:
- "Analyze this config file for security issues AND document what each setting does"
- "Find all hardcoded values in this file AND write unit tests for the main functions"
- "Map the directory structure of this project AND list all external dependencies"

Ask Claude to use **separate agents running in parallel** for each part.
Be explicit:

> "Use separate agents running in parallel for each part.
> Part 1: [describe Part 1]
> Part 2: [describe Part 2]
> Report the results together."

### What to Watch

- The two agent threads appearing in the output
- Each agent having its own context — it does not know what the other is doing
- The orchestrator combining the results
- Total time vs. what it would have been sequentially

### Reflection Questions

1. Were the tasks actually independent?  Did the agents need to coordinate?
2. Did running in parallel introduce any issues?
3. Where in your actual work could you use this pattern?

### Hints

- Tasks must be genuinely independent.  If Agent 2 needs Agent 1's output, it is a pipeline, not fan-out.
- Keep each agent's task focused.  Vague tasks produce vague results.
- Start small.  Two agents is enough to see the pattern.

---

## Exercise 3.2: Watch a Codex Swarm (Group Exercise)

**Type:** Group observation and discussion, ~15 minutes
**Goal:** Understand the multi-model pipeline and when to use it.

### Format

The moderator runs the demo from Demo 3.2.
Everyone watches.

### Discussion Questions (after the demo)

Work in groups of 2-3.  Discuss:

1. **Decomposition quality:** How well did Claude break the task into independent subtasks?
   Were the boundaries clean?  Would you have decomposed differently?

2. **Codex vs. Claude comparison:** Looking at what Codex generated — does it match what Claude would have written?
   Where are the differences?  Does Codex code feel different?

3. **Review effectiveness:** What did the Claude review step catch?
   Were there issues in the generated code that you can see?

4. **Your use case:** Where in your current work would you use a Codex Swarm?
   What task would benefit from the architect-contractor-inspector model?

5. **The cost/speed tradeoff:** This pipeline uses Opus for planning and review, Codex for generation.
   When does the cost make sense?  When would you just use Claude directly?

### Report Back

Each group shares their most interesting answer.  5 minutes total.

---

## Exercise 3.3: Security Audit Your Project

**Type:** Individual or pair, ~20 minutes
**Goal:** Run adversarial security testing on real code and see what it finds.

### Setup

Use a project from earlier in the workshop, or the workshop demo project.

**Step 1: Plant a vulnerability (important)**

This makes the exercise concrete.  Add this to a Python file in your project:

```python
def run_command(user_input):
    import subprocess
    # This code passes user input directly to a shell command — intentional vulnerability
    result = subprocess.run("echo " + user_input, shell=True, capture_output=True, text=True)
    return result.stdout
```

Note where you added it.  We want to see if the swarm finds it.

**Step 2: Run the swarm**

```
/devil-advocate-swarms:swarm
```

Point it at your project.

**Step 3: Wait and watch**

Do not skip ahead.  Watch each stage:
- Scanners: what did each one identify?
- Debate: which findings are being argued?  Who is winning?
- Consensus: how many CONFIRMED vs FALSE POSITIVE?
- Fixers: what does the fix for your planted vulnerability look like?

### What to Report

1. Did the swarm find the planted vulnerability?  At which stage was it confirmed?
2. What else did it find?  Were there real issues you had missed?
3. Were there false positives?  What did the Defender argue for each one?
4. How does the fix compare to what you would have written manually?

### Hints

- The debate phase is the most interesting part.  Read the Prosecutor and Defender arguments.
- Some real findings will be false positives.  The Defender should win those.
- If the swarm misses the planted vulnerability, that is also interesting — why?
- Pay attention to the regression test the Fixer writes.  Is it testing the right thing?

### For the CySec Engineer

Your professional instincts will tell you whether the Prosecutor's arguments are realistic.
Are the attack scenarios in the debate plausible?  Would you write a CVE this way?
Where does the automated analysis fall short of human expert judgment?
Where does it match or exceed it?

---

## Exercise 3.4: Set Up Automation

**Type:** Individual, ~15 minutes
**Goal:** Configure a real automated task that will run without your involvement.

### Task

Choose something you actually want to automate.  Options:

**Option A: Quality Gate on a Schedule**
```
/schedule
```
Task: run the quality gate every day at a time of your choice.
Configure it for your current project.

**Option B: Security Scan on a Schedule**
```
/schedule
```
Task: run a security audit every week on Monday.
Configure it for a project with external input handling.

**Option C: Monitoring Loop**
```
/loop 1m
```
Task: check if a specific file has been modified in the last 60 seconds and report.
(Useful during active development to catch accidental changes.)

**Option D: Your Own Idea**
What would you actually automate at work?
Design it, set it up, verify it triggers at least once.

### Verification

After setting up your automation, verify it runs:
- For `/schedule`: check the schedule list with `/schedule` and confirm your task appears
- For `/loop`: watch it trigger at least twice in your terminal

### Discussion (with the person next to you)

- What did you choose to automate?  Why that task?
- What would break if the automation ran on bad code?  What is your safety net?
- What would you actually automate in your work environment, given 30 minutes to set it up?

---

## Exercise 3.5: Architecture Discussion (Group)

**Type:** Group, ~30 minutes
**Goal:** Design an ideal Claude Code workflow for a real project.
This is the synthesis exercise — the goal of the entire workshop.

> **Note:** Before starting, review the Use Case Blueprints below for inspiration.

### Format

Groups of 3-4 people.  Each group designs a workflow.

### Your Task

Pick a real project (your work, a side project, the workshop demo).
Sketch the ideal Claude Code workflow for that project on the whiteboard or paper.

**Your workflow should address:**

1. **Hooks:**
   What automation happens without being asked?
   (Before commits? After file saves? When tests fail?)

2. **Skills:**
   What workflows are complex enough to formalize as skills?
   (Code review? Deployment? Specific kinds of analysis?)

3. **Scheduled tasks:**
   What runs automatically on a schedule?
   (Daily quality check? Weekly security scan? Dependency monitoring?)

4. **Multi-agent work:**
   Where would parallel agents help?
   (Parallel analysis? Fan-out scanning? Hierarchical review?)

5. **Security integration:**
   Where does adversarial testing fit?
   (Before every PR? Weekly? On-demand when external input changes?)

6. **Telegram triggers (optional):**
   What would you want to trigger from your phone?
   (Incident investigation? Build status? Deployment?)

7. **Continuous improvement:**
   Is there anything you would run the self-improve loop on?
   (Test coverage? Code style? Documentation?)

### Constraints to Consider

- What is the minimum viable workflow?  Not everything at once.
- What is the highest-value first automation to implement?
- What needs human review — where should Claude stop and ask?
- What could go wrong?  Where are the safety nets?

### Presentation

Each group presents their workflow in 5 minutes.
Use the whiteboard or walk through your sketch.

Focus on:
- What problem does this solve?
- What is the highest-value automation?
- What is the first thing you would actually implement this week?

### This Exercise IS the Workshop Goal

The ability to design this workflow is what we came here for.
You now have the vocabulary, the tools, and the mental models.
The workflow you sketch today should be something you can actually start building tomorrow.

---

## Bonus Exercise 3.3: HIPAA-Style Security Guardrails

**Type:** Individual, ~15 minutes
**Goal:** Build a hook that scans every file write for sensitive data patterns — simulating compliance guardrails.

### Background

In healthcare (HIPAA), in finance (PCI-DSS), and in physical security (access logs with personal data), there are strict rules about what data can be written to files. This exercise builds a PreToolUse hook that blocks Claude from accidentally writing sensitive patterns into code or config files.

**Security analogy:** This is the equivalent of a scanner at a secure facility exit — checking that nobody carries classified documents out of the building.

### Steps

**Step 1: Define your sensitive patterns**

Choose patterns relevant to your domain:
- Social security numbers: `\d{3}-\d{2}-\d{4}`
- Credit card numbers: `\d{4}[\s-]?\d{4}[\s-]?\d{4}[\s-]?\d{4}`
- API keys: `(sk-|pk_|AKIA)[A-Za-z0-9]{20,}`
- Access card IDs (your domain!): patterns from your card reader format
- IP addresses of internal infrastructure: `10\.\d+\.\d+\.\d+` or `192\.168\.`

**Step 2: Create the scanner hook**

Create `~/.claude/hooks/sensitive-data-scanner.sh`:
```bash
#!/bin/bash
INPUT=$(cat)
CONTENT=$(echo "$INPUT" | jq -r '.content // .new_content // ""')

# Check for sensitive patterns
PATTERNS='(\d{3}-\d{2}-\d{4}|sk-[A-Za-z0-9]{20,}|AKIA[A-Z0-9]{16}|password\s*=\s*["\x27][^"\x27]+)'

if echo "$CONTENT" | grep -qEi "$PATTERNS"; then
  MATCH=$(echo "$CONTENT" | grep -oEi "$PATTERNS" | head -3)
  echo "BLOCKED: Sensitive data pattern detected in file content:" >&2
  echo "$MATCH" >&2
  echo "Redact or remove sensitive data before writing." >&2
  exit 1
fi

exit 0
```

**Step 3: Register and test**

Add to settings.json with matcher `Write|Edit`. Test by asking Claude to create a config file with a hardcoded API key.

### Success Check

- [ ] Hook blocks writes containing sensitive patterns
- [ ] Hook allows normal writes without sensitive data
- [ ] You adapted at least one pattern to your specific domain
- [ ] You understand the difference between blocking (exit 1) and allowing (exit 0)

### Reflection

1. What sensitive data patterns exist in your actual projects?
2. Could you run this as a PostToolUse hook instead (log but don't block)?
3. How would you handle false positives (e.g., test data that looks like real credentials)?

---

## Use Case Blueprints — Inspiration for Exercise 3.4

These blueprints come from deep research into advanced Claude Code workflows. Use them as building blocks for your Architecture Discussion:

| # | Blueprint | Components | Security Relevance |
|---|-----------|-----------|-------------------|
| 1 | **Secure Diff Gate** — Block writes to `.env`, secrets, credentials | PreToolUse Hook + matcher `Write\|Edit` | Prevent accidental secret exposure |
| 2 | **Token Firewall** — Filter noisy test/build output | PreToolUse Hook on Bash + filter script | Cost control, context management |
| 3 | **Circuit Breaker** — Stop agents stuck in retry loops | PostToolUse Hook detecting 3x same error | Prevent runaway token costs |
| 4 | **CVE-Fix Pipeline** — From advisory to PR automatically | WebSearch + Plan Mode + Bash + Git | Vulnerability management |
| 5 | **CI-Locked Agent** — Claude as CI worker with strict rules | `dontAsk` mode + allow rules + `--json-schema` | Deterministic pipeline integration |
| 6 | **Repo Onboarding** — `/init` + Skills + Hooks as team standard | `/init` + Plugin packaging + managed scope | Team standardization |
| 7 | **Autonomous Refactor** — `/batch` across worktrees | `/batch` + subagents + worktrees | Safe large-scale changes |
| 8 | **Living Architecture Map** — Auto-generated Mermaid diagrams | LSP + Skill + PostToolUse hook after merge | Documentation stays current |
| 9 | **Two-Device Debugging** — Remote control from phone/browser | `/remote-control` + local execution | Mobile incident response |
| 10 | **Agent Team as Virtual Dev Org** — Lead + Reviewer + QA + Docs | TeamCreate + SendMessage + Sonnet teammates | Parallel specialized work |

---

*End of Block 3 Exercises*
