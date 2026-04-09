# Claude Code Dynamic Workshop

> Interactive workshop for security professionals learning Claude Code.
> 3 Sessions, 13 modules, demo-heavy with optional hands-on exercises.

---

## Quick Start (Moderator)

### 1. Vorbereitung

- [ ] `resources/prerequisites.md` an Teilnehmer schicken (min. 1 Woche vorher)
- [ ] Eigene Installation pruefen: `claude --version`, `python3 --version`, `git --version`
- [ ] Workshop-Playground klonen und Tests laufen lassen: `cd workshop-playground && python3 -m pytest -v`
- [ ] Plugin testen: `/workshop guide 1.1` und `/workshop learn 1.1` in Claude Code ausfuehren

### 2. Kursmaterial

| Datei | Zweck |
|-------|-------|
| `resources/session-plan.md` | Zeitplan und Ablauf fuer alle 3 Sessions |
| `resources/cheatsheet.md` | Referenz-Karte fuer Teilnehmer (ausdrucken/verteilen) |
| `resources/prerequisites.md` | Setup-Anleitung fuer Teilnehmer |
| `resources/workshop-analysis-report.md` | QA-Report mit bekannten Issues |

### 3. Workshop durchfuehren

Jede Session folgt dem gleichen Muster:
1. **Teaching + Live Demos** — du zeigst, erklaerst, machst vor
2. **Selber ausprobieren** — Teilnehmer arbeiten an Exercises
3. **Q&A und Recap** — offene Fragen klaeren

---

## Kursstruktur

### Session 1 — Foundations (~3h)

> "Was ist Claude Code und warum sollte mich das interessieren?"

| Modul | Thema | Highlight-Demo |
|-------|-------|----------------|
| **1.1** | Was ist Claude Code? | Log-Parser in 3 Minuten bauen |
| **1.2** | Context & Memory | CLAUDE.md anlegen, `/compact` + `/cost` zeigen |
| **1.3** | Effective Prompting | Vager vs. praeziser Prompt Side-by-Side |
| **1.4** | Git Integration | Branch → Feature → Commit → PR in einem Flow |

**Exercises:** `resources/exercises/block-1-exercises.md`

### Session 2 — Ecosystem (~3h)

> "Wie macht man Claude Code richtig maechtig?"

| Modul | Thema | Highlight-Demo |
|-------|-------|----------------|
| **2.1** | Skills & Commands | Eigenen Security-Skill live bauen |
| **2.2** | Hooks | Hook der `rm -rf` blockiert |
| **2.3** | Plugins | Plugin-Anatomie durchgehen |
| **2.4** | MCP | Playwright: Claude steuert einen Browser |
| **2.5** | RAG & NotebookLM | Knowledge Base erstellen und abfragen |

**Exercises:** `resources/exercises/block-2-exercises.md`

### Session 3 — Advanced (~3h)

> "Jetzt wird's wild."

| Modul | Thema | Highlight-Demo |
|-------|-------|----------------|
| **3.1** | Agents | 2 parallele Agents auf echtem Task |
| **3.2** | Multi-Model Pipelines | Codex Swarm baut CLI-Tool |
| **3.3** | Security & Adversarial | Devil's Advocate Swarm auf vulnerablem Code |
| **3.4** | Automation | Self-Improve Loop: Vorher/Nachher |
| **3.5** | Full Stack Architecture | Gruppen-Discussion: Design your Workflow |

**Exercises:** `resources/exercises/block-3-exercises.md`

---

## Verzeichnisstruktur

```
dynamic_workshop/
├── README.md                  ← Du bist hier
├── workshop-dashboard.html    ← Interaktives Dashboard (Status-Tracking)
├── resources/
│   ├── session-plan.md        ← Zeitplan fuer alle 3 Sessions
│   ├── cheatsheet.md          ← Referenz-Karte (CLI, Shortcuts, Commands)
│   ├── prerequisites.md       ← Setup-Anleitung fuer Teilnehmer
│   ├── workshop-analysis-report.md  ← QA-Report
│   ├── modules/
│   │   ├── block-1-foundations.md   ← Teaching: Foundations
│   │   ├── block-2-ecosystem.md     ← Teaching: Ecosystem
│   │   └── block-3-advanced.md      ← Teaching: Advanced
│   ├── demos/
│   │   ├── block-1-demos.md         ← Demo-Scripts Block 1
│   │   ├── block-2-demos.md         ← Demo-Scripts Block 2
│   │   └── block-3-demos.md         ← Demo-Scripts Block 3
│   └── exercises/
│       ├── block-1-exercises.md     ← Uebungen Block 1
│       ├── block-2-exercises.md     ← Uebungen Block 2
│       └── block-3-exercises.md     ← Uebungen Block 3
├── workshop-playground/       ← Demo-Repo mit 3 geplanteten Vulnerabilities
├── skills/                    ← Workshop-Plugin Skills
├── agents/                    ← Workshop-Plugin Agents
└── commands/                  ← Workshop-Plugin Commands
```

---

## Workshop-Plugin

Das Plugin bietet zwei Modi:

- **`/workshop guide 1.1`** — Moderator-Modus: Talking Points, Demo-Scripts, Timing-Hinweise
- **`/workshop learn 1.1`** — Teilnehmer-Modus: Interaktives Lernen mit Erklaerungen und Verifikation

Modulnummern: `1.1` bis `1.4`, `2.1` bis `2.5`, `3.1` bis `3.5`

---

## Fuer Teilnehmer

1. **Vor dem Workshop:** `resources/prerequisites.md` durcharbeiten
2. **Waehrend des Workshops:** `resources/cheatsheet.md` als Referenz nutzen
3. **Nach dem Workshop:** Bonus-Exercises aus `resources/session-plan.md` ausprobieren

---

## Weitere Orientierung

- `WORKSHOP_EINFUEHRUNG.md` - Einstieg, Inhaltsverzeichnis, Einordnung der Ordner
- `resources/workshop-guide.md` - Guide fuer Selbststudium und Lernreihenfolge
- `resources/workshop-specials.md` - Erklaerung der workshop-spezifischen Extras
- `resources/zip-cleanup-checklist.md` - konservative Liste fuer Versand-Bereinigung

---

## Zielgruppe

Erfahrene Entwickler aus dem Bereich Physical Security (Zutrittskontrolle, Alarmsysteme, Kartenleser). Alle Konzepte werden mit Security-Analogien erklaert.
