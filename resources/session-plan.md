# Workshop Session Plan — 3 Sessions

> Claude Code Dynamic Workshop for Security Professionals
> Format: 3 separate sessions, each ~3 hours
> Style: Demo-heavy, lots of live examples, exercises optional/self-paced

---

## Session 1 — Foundations

> "What is this thing and why should I care?"

### Part 1: Orientation (~45 min)

**Module 1.1 — What is Claude Code?**
- CLI vs Desktop vs Web vs IDE Extensions — live zeigen
- Security Analogy: Claude Code as the new team member with clearance levels
- **Live Demo:** Von Null zum funktionierenden Log-Parser in 3 Minuten
- **Live Demo:** Gleiche Aufgabe in CLI, Desktop App, und VS Code Extension

**Module 1.2 — Context & Memory**
- Context Window erklaeren (Opus 1M vs Sonnet/Haiku 200K)
- CLAUDE.md als "Dienstanweisung" — was rein gehoert, was nicht
- **Live Demo:** CLAUDE.md anlegen, Session neustarten, Verhalten beobachten
- **Live Demo:** `/compact` in Aktion — vorher/nachher Token-Verbrauch mit `/cost`

*— Kurze Pause (10 min) —*

### Part 2: Hands-on Basics (~50 min)

**Module 1.3 — Effective Prompting**
- The Contractor Analogy: vage vs. praezise Auftraege
- Security-Kontext: "Schreib einen Port-Scanner" vs. volle Spec mit Edge Cases
- **Live Demo Side-by-Side:** Vager Prompt vs. detaillierter Prompt → Qualitaetsunterschied zeigen
- **Live Demo:** Iteratives Verfeinern — vom ersten Versuch zum Production-Code

**Module 1.4 — Git Integration**
- Worktrees als Security-Airlocks — isolierte Entwicklung
- **Live Demo:** Branch → Feature implementieren → Diff reviewen → Commit mit `/commit`
- **Live Demo:** Worktree erstellen, parallel in zwei Features arbeiten

### Part 3: Selber ausprobieren (~40 min)

Teilnehmer arbeiten in eigenem Tempo an den Exercises. Moderator geht rum und hilft.

- **Exercise 1.1:** Log-Parser bauen (Einstieg, ~8 min)
- **Exercise 1.2:** CLAUDE.md + Memory einrichten (~10 min)
- **Exercise 1.3:** Prompt-Vergleich — vage vs. praezise (~10 min)
- **Exercise 1.4:** Git-Workflow durchspielen (~12 min)

### Bonus fuer Zuhause

- Eigenes Projekt mit CLAUDE.md aufsetzen
- 3 verschiedene Prompting-Strategien am gleichen Problem vergleichen
- `claude -c` testen: Session fortsetzen nach Neustart

---

## Session 2 — Ecosystem

> "How do I make this thing really powerful?"

### Part 1: Extending Claude Code (~50 min)

**Module 2.1 — Skills & Commands**
- Skills als wiederverwendbare Workflows
- **Live Demo:** Eigenen Skill erstellen, der Security-Checks automatisiert
- **Live Demo:** `/tdd` Skill in Aktion — Red-Green-Refactor live

**Module 2.2 — Hooks**
- Security Analogy: Alarm-Sensoren — PreToolUse, PostToolUse, Stop
- **Live Demo:** Hook bauen der `rm -rf` blockiert
- **Live Demo:** PostToolUse Hook der alle Bash-Commands loggt
- Praxis-Beispiel: Kosten-Waechter Hook der bei teuren Operationen warnt

*— Kurze Pause (10 min) —*

### Part 2: Integrations (~50 min)

**Module 2.3 — Plugins**
- Plugin-Anatomie: plugin.json, Skills, Agents, Commands
- **Live Demo:** Plugin-Verzeichnis durchgehen, ein Plugin installieren
- Ueberblick: Welche Plugins gibt es, was machen sie

**Module 2.4 — MCP (Model Context Protocol)**
- External Integrations mit Authentifizierung — Security-Perspektive
- **Live Demo:** Playwright MCP — Claude steuert einen Browser
- **Live Demo:** GitHub MCP — PRs und Issues direkt aus Claude Code
- `.mcp.json` Konfiguration erklaeren

**Module 2.5 — RAG & Knowledge Bases**
- NotebookLM als Wissens-Backend
- **Live Demo:** Notebook erstellen, Quellen hinzufuegen, abfragen
- Wann RAG vs. wann Context Window reicht

### Part 3: Selber ausprobieren (~40 min)

- **Exercise 2.1:** Eigenen Skill bauen (~15 min)
- **Exercise 2.2:** Security-Hook implementieren (~15 min)
- **Exercise 2.4:** MCP Server konfigurieren (~15 min)

### Bonus fuer Zuhause

- Exercise 2.3: Plugin-Ecosystem erkunden — installierte Plugins durchgehen
- Exercise 2.5: Eigene Knowledge Base mit NotebookLM aufsetzen
- Eigenen PreToolUse Hook schreiben der sensitive Dateien schuetzt (.env, credentials)

---

## Session 3 — Advanced & Mind-Blowing

> "Now it gets wild."

### Part 1: Multi-Agent Orchestration (~50 min)

**Module 3.1 — Agents**
- Agent-Definition, Spezialisierung, wann Subagents sinnvoll sind
- Patterns: Single → Parallel → Pipeline → Fan-out/Fan-in
- **Live Demo:** 2 parallele Agents auf echtem Task
- **Live Demo:** Agent-Orchestrator — komplexe Aufgabe automatisch zerlegen

**Module 3.2 — Multi-Model Pipelines**
- Claude → Codex → Claude Review Pipeline
- Welches Modell fuer welche Aufgabe (Staerken-Tabelle)
- **Live Demo:** Codex Swarm — 3-4 parallele Agents bauen ein CLI-Tool
- Kosten/Speed Tradeoff diskutieren

*— Kurze Pause (10 min) —*

### Part 2: Security & Automation (~50 min)

**Module 3.3 — Adversarial Testing**
- Devil's Advocate Swarms: Scanner → Debate → Consensus → Fix
- Security Analogy: Automatisierter Pentest
- **Live Demo:** Swarm auf dem Workshop-Playground mit 3 geplanteten Vulnerabilities
- Live die Debate-Phase beobachten — Agents argumentieren ueber Severity

**Module 3.4 — Scheduled Tasks & Self-Improve**
- Automatisierung: `/schedule`, `/loop`, Self-Improve Iterations
- **Live Demo:** Quality-Gate Baseline → 1 Self-Improve Iteration → Vorher/Nachher
- Safety Gates: Warum der Loop sich selbst stoppt

**Module 3.5 — Full Stack Architecture**
- Worktree Isolation, Docker/Inception, Telegram Bridge
- **Live Demo:** Architektur-Diagramm — wie alles zusammenspielt
- Das grosse Bild: Von einzelnem Prompt bis autonomem Agent-System

### Part 3: Architecture Discussion (~30 min)

**Gruppen-Exercise 3.5 — Design your Workflow**
- Jeder Teilnehmer stellt ein reales Projekt vor
- Gemeinsam designen: Welche Hooks, Skills, Agents, Schedules wuerden helfen?
- Diskussion: Security-Boundaries, Trust-Levels, Kosten-Management
- Das ist das Highlight — hier kommt alles zusammen

### Bonus fuer Zuhause

- Exercise 3.1: Parallele Agents auf eigenem Projekt laufen lassen
- Exercise 3.3: Eigene Vulnerability einbauen und Swarm drauf loslassen
- Exercise 3.4: Eigene Automatisierung einrichten (Quality-Gate Schedule oder Security-Scan)
- Self-Improve Loop auf eigenem Codebase ausprobieren

---

## Session-Ueberblick

| Session | Thema | Schwerpunkt | Vibe |
|---------|-------|-------------|------|
| **1** | Foundations | CLI, Context, Prompting, Git | "Das kann das Tool" |
| **2** | Ecosystem | Skills, Hooks, Plugins, MCP, RAG | "So macht man es maechtig" |
| **3** | Advanced | Agents, Multi-Model, Security, Automation | "Jetzt wird's wild" |

### Empfohlener Abstand zwischen Sessions

- **1-2 Wochen** zwischen den Sessions
- Gibt Zeit zum Ausprobieren und Fragen sammeln
- Jede Session beginnt mit kurzem Recap + Q&A (~5 min)

---

## Moderator-Hinweise

- **Demos > Slides:** Lieber eine Demo mehr als eine Folie mehr
- **Tempo anpassen:** Wenn eine Demo Fragen ausloest, lieber ausdiskutieren als hetzen
- **Exercises sind optional:** Wer schneller ist, macht Bonus. Wer langsamer ist, schaut zu.
- **Pausen flexibel:** Wenn die Energie nachlässt, Pause einlegen — egal wo im Plan
- **Jede Session steht fuer sich:** Teilnehmer sollen auch was mitnehmen wenn sie nur 1 Session besuchen

---

**Last Updated:** 2026-04-03 | **Workshop:** Claude Code Dynamic Workshop
