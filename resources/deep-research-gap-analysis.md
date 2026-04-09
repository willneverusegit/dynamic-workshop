# Deep Research Gap-Analyse & Integrationsplan

> Abgleich der Deep-Research-Berichte mit dem Workshop-Inhalt (Stand: 2026-04-05)

---

## 1. Fehlende Slash Commands im Cheatsheet

Das Cheatsheet listet ~25 Commands. Die Deep-Research-Reports dokumentieren **50+ offizielle Commands**. Folgende fehlen komplett:

### Prio A — Workshop-relevant (sofort aufnehmen)

| Command | Zweck | Welche Session |
|---------|-------|----------------|
| `/init` | Projekt-CLAUDE.md generieren/verbessern | S1 (Foundations) |
| `/context` | Context-Verbrauch visualisieren | S1 (Context) |
| `/diff` | Interaktiver Diff-Viewer | S1 (Git) |
| `/effort` | Effort-Level setzen (high/low) | S1 (Prompting) |
| `/memory` | Auto-Memory & CLAUDE.md verwalten | S1/S2 (Memory) |
| `/resume` | Session fortsetzen (benannt) | S1 (Session) |
| `/batch` | Parallele Worktree-Aenderungen (Bundled Skill) | S3 (Agents) |
| `/debug` | Debug-Logging + Analyse (Bundled Skill) | S2/S3 |
| `/loop` | Prompt periodisch ausfuehren (Bundled Skill) | S3 (Automation) |
| `/simplify` | Parallel-Reviews auf changed files (Bundled Skill) | S2 (Skills) |
| `/sandbox` | Sandbox-Mode togglen | S3 (Security) |
| `/security-review` | Security-Diff Review | S3 (Security) |
| `/hooks` | Hook-Konfigurationen ansehen | S2 (Hooks) |
| `/skills` | Installierte Skills listen | S2 (Skills) |
| `/plugin` | Plugins verwalten | S2 (Plugins) |
| `/tasks` / `/bashes` | Background Tasks anzeigen | S3 (Agents) |
| `/export` | Conversation exportieren | S1 (Session) |
| `/rewind` | Checkpoints / zurueckspulen | S1 (Git) |

### Prio B — Nuetzlich, aber optional

| Command | Zweck |
|---------|-------|
| `/btw` | Side-Question ohne Context-Verschmutzung |
| `/branch` / `/fork` | Conversation-Branching |
| `/copy` | Letzten Output kopieren |
| `/insights` | Session-Report (Patterns/Friction) |
| `/chrome` | Chrome-Integration konfigurieren |
| `/desktop` | Session in Desktop-App fortsetzen |
| `/mobile` | QR-Code fuer Mobile App |
| `/remote-control` (`/rc`) | Remote Control Session |
| `/ultraplan` | Browser-Plan + Execute |
| `/voice` | Voice Dictation |
| `/pr-comments` | PR-Kommentare fetchen |
| `/release-notes` | Changelog im Prompt |
| `/reload-plugins` | Plugins live reloaden |
| `/rename` | Session umbenennen |
| `/stats` | Usage/Streaks |
| `/statusline` | Status-Line konfigurieren |
| `/theme` | Theme aendern |
| `/color` | Prompt-Bar Farbe |
| `/usage` | Usage-Limits anzeigen |
| `/upgrade` | Plan upgraden |
| `/passes` | Free Week Share |
| `/extra-usage` | Extra-Usage konfigurieren |
| `/ide` | IDE-Integrationen |
| `/install-github-app` | GitHub Actions App |
| `/install-slack-app` | Slack App |
| `/privacy-settings` | Privacy Settings |
| `/powerup` | Feature-Lessons |
| `/agents` | Subagent-Konfiguration |
| `/keybindings` | Keybinding-Config |
| `/exit` | CLI verlassen |
| `/remote-env` | Remote-Env Default |

---

## 2. Fehlende CLI Flags im Cheatsheet

| Flag | Zweck | Prio |
|------|-------|------|
| `--permission-mode {default\|acceptEdits\|plan\|auto\|dontAsk\|bypassPermissions}` | Feingranulare Permission-Modi | A |
| `--enable-auto-mode` | ML-Klassifikator entscheidet (Team/Enterprise) | B |
| `--mcp-config <file>` | MCP-Server aus JSON laden | A |
| `--strict-mcp-config` | Nur MCP aus Config, keine anderen | B |
| `--plugin-dir <path>` | Lokale Plugin-Dirs laden | A |
| `--json-schema <schema>` | Validated JSON Output (Print-Mode) | B |
| `--worktree` / `-w` | Isolierte Git-Worktrees | A |
| `claude -c` / `--continue` | Letzte Session fortsetzen (im Cheatsheet, aber `-r/--resume` fehlt) | A |
| `claude mcp add/list/get/remove` | MCP CLI-Subcommands | A |
| `claude plugin install/uninstall/enable/disable/update` | Plugin CLI-Subcommands | A |
| `claude auth login/logout/status` | Auth-Subcommands | B |
| `claude update` | CLI updaten | B |
| `claude remote-control` | Remote Control starten | B |

---

## 3. Fehlende Konzepte im Workshop

### 3.1 Nicht oder kaum abgedeckt

| Thema | Was fehlt | Wo integrieren |
|-------|-----------|----------------|
| **Bundled Skills** | `/batch`, `/debug`, `/loop`, `/simplify`, `/claude-api` — keine Erwaehnung | S2 (Skills-Modul) + Cheatsheet |
| **Permission Modes (Detail)** | Nur default + allowedTools + skip-permissions. Es fehlen: `acceptEdits`, `plan`, `auto`, `dontAsk` | S1 (Permissions) + S3 (Security) |
| **Agent Teams** | `TeamCreate`/`SendMessage` fuer Multi-Session-Koordination (experimental) | S3 (Agents) |
| **Sandboxing (OS-Level)** | Seatbelt (macOS), bubblewrap (Linux/WSL2); Sandbox-Modi | S3 (Security) |
| **MCP Transport-Typen** | HTTP (empfohlen), SSE (deprecated), stdio (lokal) — Workshop erwaehnt nur allgemein | S2 (MCP-Modul) |
| **MCP OAuth** | OAuth-Flows fuer Remote-MCP-Server | S2 (MCP-Modul) |
| **MCP Output-Limits** | 10k Warn, 25k Default, 500k Override via `_meta` | S2 (MCP-Modul) |
| **Built-in Tools komplett** | `WebSearch`/`WebFetch`, `LSP`, `Task*`/`Cron*`, `NotebookEdit`, `TeamCreate`/`SendMessage` | S1 (Tools) + Cheatsheet |
| **Data Retention & Privacy** | Consumer vs Commercial; ZDR; Telemetrie opt-out (`DISABLE_TELEMETRY`) | S3 (Security) |
| **Cost Guidance** | ~$6/Dev/Tag; $100-200/Monat; Token-Reduktionsstrategien | S1 (Context) |
| **Plugin Scopes** | `user`/`project`/`local`/`managed` — nur `user` erwaehnt | S2 (Plugins) |
| **Plugin CLI-Befehle** | `claude plugin install/uninstall/enable/disable/update` | S2 (Plugins) + Cheatsheet |
| **Skill Frontmatter Detail** | `disable-model-invocation`, `allowed-tools`, `context: fork`, `agent:` | S2 (Skills) |
| **Circuit Breaker Hooks** | Halluzinations-Loop-Erkennung via Hooks | S3 (Security) |

### 3.2 Im Workshop vorhanden, aber in Deep Research detaillierter

| Thema | Was ergaenzen |
|-------|--------------|
| Hook-Typen | `prompt`-Hooks und `agent`-Hooks neben `command`/`http` |
| CLAUDE.md | Managed Policy Locations (Enterprise); "unter 200 Zeilen"-Empfehlung |
| Subagents | `context: fork` in Skills als Subagent-Isolation |
| Security | CVE-2025-53110 (Path Traversal in MCP), Supply-Chain-Risiken bei Plugins |

---

## 4. Fehlende Use Cases

Die Deep-Research-Reports enthalten **15 Blueprint-Use-Cases**, von denen keiner explizit im Workshop vorkommt. Die relevantesten fuer Physical-Security-Entwickler:

| # | Use Case | Security-Relevanz |
|---|----------|-------------------|
| 1 | Token-Firewall (Hook-basierte Log-Filterung) | Kosten + Context-Kontrolle |
| 2 | Secure Diff Gate (Write/Edit Guardrails) | **Hoch** — direkt anwendbar |
| 8 | HIPAA-Konforme Security Guardrails | **Hoch** — Analogie zu Zutrittskontrolle |
| 9 | Autonomes Pen-Testing ("Shannon") | **Hoch** — Security-Team-relevant |
| 15 | Circuit Breaker gegen Halluzinations-Loops | Kosten-Kontrolle |
| 3 | Repo-Onboarding als Produkt | Team-Standardisierung |
| 7 | Autonomous Refactor mit Worktrees | Grosse Codebasen |
| 14 | Research-to-Patch (CVE-Fix Pipeline) | **Hoch** — Vulnerability Management |

---

## 5. Integrationsplan

### Phase 1: Cheatsheet-Erweiterung (Sofort)

**Neue Sektionen im Cheatsheet:**

1. **Alle Slash Commands** — Prio-A-Liste aus Abschnitt 1 aufnehmen, gruppiert in:
   - Session Management (bestehend + `/init`, `/resume`, `/export`, `/rewind`, `/rename`, `/btw`)
   - Context & Cost (`/context`, `/effort`, `/usage`, `/cost`, `/compact`)
   - Development (`/plan`, `/diff`, `/memory`, `/hooks`, `/skills`, `/plugin`, `/sandbox`)
   - Security (`/security-review`, `/permissions`, `/sandbox`)
   - Agents & Automation (`/tasks`, `/batch`, `/loop`, `/debug`, `/simplify`, `/agents`)
   - Integration (`/mcp`, `/chrome`, `/desktop`, `/mobile`, `/rc`)

2. **Bundled Skills** — Neue Sektion:
   ```
   | Skill | Zweck |
   | `/batch <instruction>` | Parallele Worktree-Aenderungen |
   | `/claude-api` | API/SDK Referenz laden |
   | `/debug [desc]` | Debug-Logging + Analyse |
   | `/loop [interval] <prompt>` | Periodische Ausfuehrung |
   | `/simplify [focus]` | Parallel-Reviews + Fixes |
   ```

3. **Built-in Tools** — Neue Sektion:
   ```
   | Tool | Permission? |
   | Read | nein |
   | Edit / Write / NotebookEdit | ja |
   | Bash | ja |
   | WebSearch / WebFetch | ja |
   | LSP | nein (Setup) |
   | Skill | ja |
   | Agent | nein |
   | TeamCreate / SendMessage | nein (experimental) |
   | Task* / Cron* | nein |
   ```

4. **CLI Subcommands** — Neue Sektion:
   ```
   claude mcp add/list/get/remove
   claude plugin install/uninstall/enable/disable/update
   claude auth login/logout/status
   claude update
   ```

5. **Permission Modes** — Bestehende Sektion erweitern:
   ```
   | Mode | Verhalten |
   | default | Nur Reads erlaubt, alles andere fragt |
   | acceptEdits | Reads + Edits erlaubt |
   | plan | Gesamtplan vorab genehmigt |
   | auto | ML-Klassifikator (Team/Enterprise) |
   | dontAsk | Keine Prompts (CI/CD) |
   | bypassPermissions | YOLO (nur in Isolation!) |
   ```

6. **Fehlende CLI Flags** — Prio-A-Flags aus Abschnitt 2 aufnehmen

7. **Cost Guidance** — Neue Sektion:
   ```
   - Durchschnitt: ~$6/Dev/Tag mit Sonnet 4.6
   - $100-200/Monat pro Dev (stark variabel)
   - Reduktion: Skills statt CLAUDE.md, Subagents, /compact, Sonnet fuer Routine
   ```

### Phase 2: Modul-Erweiterungen

| Modul | Was ergaenzen | Aufwand |
|-------|--------------|---------|
| **1.1 (What is CC)** | Built-in Tools-Tabelle; Installation via curl/irm (nicht nur npm) | Klein |
| **1.2 (Context)** | `/context` Command; Cost Guidance; Token-Firewall Use Case | Klein |
| **1.3 (Prompting)** | `/effort` Command; Plan-Mode als Permission-Mode | Klein |
| **1.4 (Git)** | `/diff`, `/rewind` Commands | Klein |
| **2.1 (Skills)** | Bundled Skills; Skill-Frontmatter Details; `/skills` Command | Mittel |
| **2.2 (Hooks)** | Hook-Typen (prompt/agent neben command/http); Circuit Breaker Pattern | Mittel |
| **2.3 (Plugins)** | Plugin Scopes; Plugin CLI; Plugin-Security (Supply Chain) | Mittel |
| **2.4 (MCP)** | Transport-Typen (HTTP/SSE/stdio); OAuth; Output-Limits; `claude mcp` CLI | Mittel |
| **3.1 (Agents)** | Agent Teams (TeamCreate/SendMessage); `/batch`, `/tasks` | Mittel |
| **3.2 (Security)** | Permission Modes komplett; Sandboxing; Data Retention; CVE-Beispiel | Gross |
| **3.3 (Automation)** | `/loop`, `/schedule`, CI-Locked Agent Blueprint | Mittel |

### Phase 3: Use-Case-Integration (Optional, fuer Exercises/Demos)

| Use Case | Ziel-Session | Format |
|----------|-------------|--------|
| Secure Diff Gate | S2 Demo (Hooks) | Live-Demo: PreToolUse Hook blockiert `.env`-Writes |
| HIPAA/Security Guardrails | S3 Demo (Security) | Security-Analogie: "Zutrittskontrolle fuer Code" |
| Circuit Breaker | S2/S3 Demo (Hooks) | Live-Demo: Hook erkennt 3x gleichen Fehler |
| CVE-Fix Pipeline | S3 Exercise | Hands-on: WebSearch + Plan Mode + PR |
| Token-Firewall | S2 Exercise | Hands-on: Hook filtert Test-Output |

---

## 6. Zusammenfassung

| Kategorie | Im Workshop | In Deep Research | Fehlend |
|-----------|------------|-----------------|---------|
| Slash Commands | ~25 | 50+ | **25+** |
| CLI Flags | 12 | 20+ | **8+** |
| Built-in Tools | 6 | 12 | **6** |
| Permission Modes | 2 | 6 | **4** |
| Bundled Skills | 0 | 5 | **5** |
| Use Case Blueprints | 0 | 15 | **15** |
| MCP Details | Basis | Komplett | Transport, OAuth, Limits |
| Security/Privacy | Basis | Komplett | Sandboxing, ZDR, Retention |

**Groesste Luecke:** Das Cheatsheet hat weniger als die Haelfte der offiziellen Commands. Die Bundled Skills fehlen komplett. Permission Modes sind nur oberflaechlich.
