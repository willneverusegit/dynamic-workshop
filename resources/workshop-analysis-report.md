# Workshop-Analyse: Claude Code Dynamic Workshop

**Datum:** 2026-04-02  
**Analysiert:** 10 Dateien (3 Module, 3 Demos, 3 Exercises, 1 Cheatsheet)

---

## Gesamtbewertung

| Kriterium | Bewertung | Kommentar |
|-----------|-----------|-----------|
| Gliederung & Progression | 9/10 | Exzellenter Aufbau: Foundations → Ecosystem → Advanced |
| Inhaltliche Korrektheit | 7/10 | Mehrere Inkonsistenzen und veraltete Angaben |
| Vollstaendigkeit | 6/10 | Cheatsheet duenn, wichtige Features fehlen |
| Didaktik | 9/10 | Hervorragend: Theorie → Demo → Exercise Triade |
| Security-Analogien | 10/10 | Perfekt auf die Zielgruppe zugeschnitten |
| Praxistauglichkeit | 8/10 | Zeitplan ambitioniert, aber machbar |

---

## Strukturanalyse

### Was hervorragend funktioniert

1. **Dreiteilige Didaktik**: Jeder Block hat Teaching Content + Demo Script + Exercises. Das ist professionelles Workshop-Design.

2. **Security-Analogien durchgaengig**: Jedes Konzept wird auf Physical Security gemappt. Die Zielgruppe wird sich sofort wiederfinden.

3. **Progression**: Block 1 baut Grundlagen, Block 2 erweitert, Block 3 zeigt die volle Power. Kein Modul setzt Wissen voraus, das nicht vorher vermittelt wurde.

4. **Demo-Scripts**: Exakte Befehle, Recovery-Notes, Talking Points — das ist moderator-ready.

5. **Exercises mit Success Checks**: Teilnehmer wissen genau, wann sie fertig sind.

---

## Inhaltliche Fehler & Inkonsistenzen

### FEHLER 1: Context Window — Widerspruch
- **Cheatsheet Zeile 49**: "200K tokens (Haiku 4.5)"
- **Modul 1.2 Zeile 131**: "Up to 1 million tokens for Claude Opus"
- **Realitaet**: Claude Code nutzt Opus 4.6 mit 1M Context. Haiku hat 200K. Das Cheatsheet nennt nur Haiku, das Modul nur Opus.
- **Fix**: Beide Modelle mit korrekten Werten nennen. Claude Code default = Opus.

### FEHLER 2: Cowork Mode uebertrieben dargestellt
- **Modul 1.1 Zeile 38-43**: "Cowork Mode (IDE Companion)" wird als dritte Interface-Kategorie beschrieben
- **Realitaet**: Das ist die VS Code / JetBrains Extension. Die Beschreibung "watches your code as you write" ist zu stark vereinfacht. Die Extension ist eher ein Sidecar-Chat mit File-Context, kein Live-Watcher.
- **Fix**: Realistischer beschreiben oder als IDE Extension labeln.

### FEHLER 3: Memory-Pfad Inkonsistenz
- **Modul 1.2 Zeile 193**: `~/.claude/projects/*/memory/` (korrekt fuer Auto-Memory)
- **Cheatsheet Zeile 50**: `.agent-memory/` (das ist Agentic-OS Plugin Memory)
- **Fix**: Beide Systeme klar unterscheiden. Auto-Memory ist built-in, .agent-memory ist Plugin-spezifisch.

### FEHLER 4: Claude Code Installation fehlt komplett
- Nirgends steht `npm install -g @anthropic-ai/claude-code` oder die Desktop-App
- **Fix**: Prerequisites-Sektion oder Setup-Guide hinzufuegen.

### FEHLER 5: Modell-Auswahl nicht erklaert
- `claude --model sonnet` bzw. der `/model` Command fehlen
- Teilnehmer wissen nicht, wie sie zwischen Modellen wechseln
- **Fix**: In Block 1.1 oder Cheatsheet ergaenzen.

---

## Fehlende Inhalte

### Cheatsheet — Dringend erweitern

**Fehlende Keyboard Shortcuts:**
| Shortcut | Funktion |
|----------|----------|
| `Tab` | Accept suggestion |
| `Shift+Tab` | Toggle plan mode |
| `Ctrl+C` | Cancel current generation |
| `Ctrl+L` | Clear screen |
| `Up/Down` | Navigate history |

**Fehlende CLI Flags:**
| Flag | Funktion |
|------|----------|
| `claude --model sonnet` | Modell waehlen |
| `claude --print "prompt"` | Non-interactive, stdout only |
| `claude --dangerously-skip-permissions` | Auto-accept all (Vorsicht!) |
| `claude -c` | Continue last session |
| `claude --verbose` | Debug output |
| `claude "task" --output-format json` | JSON output |

**Fehlende /Commands:**
| Command | Funktion |
|---------|----------|
| `/model` | Modell wechseln |
| `/cost` | Kosten der aktuellen Session |
| `/clear` | Konversation zuruecksetzen |
| `/doctor` | Diagnose-Check |
| `/config` | Settings anzeigen/aendern |
| `/permissions` | Permission-Mode aendern |
| `/status` | Aktueller Status |
| `/bug` | Bug Report erstellen |
| `/login` | Authentifizierung |
| `/logout` | Abmelden |
| `/vim` | Vim-Keybindings toggle |
| `/terminal-setup` | Terminal-Optimierung |

**Fehlende Environment Variables:**
| Variable | Zweck |
|----------|-------|
| `ANTHROPIC_API_KEY` | API Key fuer direkte Nutzung |
| `CLAUDE_CODE_USE_BEDROCK` | AWS Bedrock Backend |
| `CLAUDE_CODE_USE_VERTEX` | Google Vertex Backend |
| `CLAUDE_MODEL` | Default-Modell setzen |

**Fehlende Permission Modes:**
| Mode | Verhalten |
|------|-----------|
| Default | Fragt bei jeder Tool-Nutzung |
| `--allowedTools` | Whitelist bestimmter Tools |
| Auto-accept patterns | In settings.json konfigurierbar |

### Module — Fehlende Themen

1. **Claude Code Desktop App & Web App (claude.ai/code)**: Wird nicht erwaehnt. Seit 2025 gibt es neben CLI auch Desktop-Apps fuer Mac/Windows und eine Web-Version.

2. **Cost Management**: Kein Modul erklaert Token-Kosten, `/cost` Command, oder wie man mit guenstigeren Modellen (Haiku/Sonnet) Kosten spart.

3. **Permission System**: Ein fundamentales Sicherheitsfeature — gerade fuer Security-Leute relevant. Wie man Tool-Permissions konfiguriert, allowedTools, etc.

4. **Headless/CI Mode**: `claude --print` fuer Pipeline-Integration fehlt. Gerade fuer Automatisierung in Block 3 relevant.

5. **Max Context Management**: Wie man mit `/compact` arbeitet, wann man Sessions splittet, Token-Budget im Blick behalten.

---

## Zeitplan-Analyse

### Block 1: ~90 Min geplant
| Segment | Geschaetzt | Realistisch |
|---------|-----------|-------------|
| Module 1.1-1.4 Teaching | 40 min | 45 min |
| Demos 1.1-1.4 | 38 min | 45 min |
| Exercises 1.1-1.4 | 40 min | 50 min |
| **Total** | **~118 min** | **~140 min** |

**Problem**: 90 Min reichen nicht. Entweder 120 Min einplanen oder Module kuerzen.

### Block 2: ~90 Min geplant
| Segment | Geschaetzt | Realistisch |
|---------|-----------|-------------|
| Module 2.1-2.5 Teaching | 45 min | 50 min |
| Demos 2.1-2.5 | 35 min | 40 min |
| Exercises 2.1-2.5 | 75-100 min | 90 min |
| **Total** | **~155 min** | **~180 min** |

**Problem**: Block 2 ist massiv ueberladen. 5 Exercises a 15-20 Min = 75-100 Min allein fuer Exercises. Empfehlung: Exercise 2.3 (Plugin Exploration) als optional markieren oder in Homework umwandeln.

### Block 3: ~90 Min geplant
| Segment | Geschaetzt | Realistisch |
|---------|-----------|-------------|
| Module 3.1-3.5 Teaching | 50 min | 55 min |
| Demos 3.1-3.5 | 40 min | 50 min |
| Exercises 3.1-3.5 | 90 min | 100 min |
| **Total** | **~180 min** | **~205 min** |

**Problem**: Aehnlich wie Block 2 — zu viel fuer 90 Min. Exercise 3.5 (Architecture Discussion, 30 Min) ist das Highlight, darf nicht gekuerzt werden.

### Empfehlung: 4-Stunden-Format oder 2-Tages-Workshop
- **Realistischer Zeitbedarf**: ~8-9 Stunden inkl. Pausen
- **Alternative**: Exercises als Hausaufgaben, nur Demos live

---

## Verbesserungsvorschlaege — Priorisiert

### Prio 1 (Vor dem Workshop fixen)

1. **Cheatsheet massiv erweitern** — CLI Flags, alle /Commands, Keyboard Shortcuts, Permission Modes, Environment Variables, Installation
2. **Context Window Widerspruch fixen** — einheitliche, korrekte Angaben
3. **Installation/Prerequisites-Guide erstellen** — Node.js, Claude Code CLI, GitHub CLI, Python 3, Playwright
4. **Zeitplan realistisch machen** — entweder mehr Zeit oder weniger Exercises

### Prio 2 (Stark empfohlen)

5. **Cost Management Sektion** — `/cost`, Modell-Wahl, Token-Awareness
6. **Permission System erklaeren** — gerade fuer Security-Audience essentiell
7. **Claude Code Desktop/Web erwaehnen** — Modul 1.1 aktualisieren
8. **Cowork Mode realistischer beschreiben**

### Prio 3 (Nice to have)

9. **CI/CD Integration** — `claude --print` fuer Pipelines
10. **Troubleshooting erweitern** — haeufige Fehler bei MCP, Authentication
11. **Glossar** — Begriffe wie MCP, RAG, Worktree fuer Nachschlagen
12. **Post-Workshop Resources** — Links, Community, weiterlernen

---

## Staerken — Beibehalten

- Die Security-Analogien sind brillant und perfekt konsistent
- Die Demo-Scripts sind moderator-ready mit Recovery-Notes
- Die Exercises bauen aufeinander auf
- Block 3 ist ambitioniert aber zeigt echten Wow-Faktor
- Der "Contractor Analogy" fuer Prompting ist exzellent
- Die Success Checks in Exercises sind praezise und hilfreich

---

*Analyse erstellt: 2026-04-02 | Analysiert von: Claude Opus 4.6*
