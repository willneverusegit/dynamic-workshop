# Workshop-Einfuehrung

Dieses Repository ist ein praxisnaher Claude-Code-Workshop mit drei aufeinander aufbauenden Sessions. Es richtet sich an Leute, die programmieren koennen, aber noch keine Routine mit Coding Agents wie Claude Code oder Codex haben.

Die wichtigste Einordnung vorab:

- Der Kern des Workshops liegt in `resources/`.
- Das eigentliche Uebungsprojekt liegt in `workshop-playground/`.
- `commands/`, `skills/`, `agents/` und `.claude-plugin/` enthalten die workshop-spezifischen Erweiterungen fuer Claude Code.
- Einige Dateien und Ordner sind Arbeitsartefakte aus der Erstellung und nicht zwingend fuer den Versand gedacht.

## So nutzt du das Repo

Wenn du den Workshop nachvollziehen oder selbst durcharbeiten willst, starte in dieser Reihenfolge:

1. `resources/prerequisites.md`
2. `resources/session-plan.md`
3. `resources/workshop-guide.md`
4. den passenden Block unter `resources/modules/`
5. die zugehoerigen Demos und Exercises

Wenn du verstehen willst, welche workshop-spezifischen Extras es gibt, lies danach `resources/workshop-specials.md`.

## Inhaltsverzeichnis des Projekts

### Kernmaterial

- `README.md`
  Kurzer Projektueberblick fuer Moderator und Maintainer.
- `resources/`
  Zentrale Workshop-Materialien. Hier liegt alles, was inhaltlich den eigentlichen Kurs ausmacht.
- `workshop-playground/`
  Kleines Demo- und Uebungsprojekt fuer Live-Demos, Tests und Security-/Agenten-Beispiele.

### Workshop-Erweiterungen fuer Claude Code

- `commands/`
  Enthaeelt den Custom Command `/workshop`.
- `skills/`
  Enthaeelt das Workshop-Skill, das Guide- und Learn-Logik kapselt.
- `agents/`
  Enthaeelt den Spezial-Agent `workshop-mentor`.
- `.claude-plugin/`
  Plugin-Manifest, das Command, Skill und Agent zusammenbindet.

### Zusatz- und Hintergrundmaterial

- `docs/`
  Aktuell keine tragende Teilnehmer-Doku; eher Ablage fuer zusaetzliche oder noch nicht integrierte Arbeitsbereiche.
- `files/`
  Aeltere oder alternative Workshop-Dokumente, die inhaltlich relevant sein koennen, aber nicht die Hauptstruktur des aktuellen Pakets bilden.
- `deep-research-report.md`
  Umfangreiches Hintergrund- und Recherchematerial.
- `Claude Code_ Skills, Use Cases, Einfuehrung.md`
  Einzelnes Referenzdokument mit zusaetzlichem Hintergrundwissen.
- `workshop-dashboard.html`
  HTML-Dashboard fuer Uebersicht/Tracking ausserhalb des Kernmaterials.

### Lokale und technische Artefakte

- `.agent-memory/`
  Lokale Agent-Memory und Arbeitskontext. Nicht Teil des eigentlichen Workshop-Inhalts.
- `.idea/`
  IDE-spezifische Projektdateien.
- `Last_session.txt`
  Lokale Sitzungsnotiz.
- `dynamic_workshop.txt`
  Vermutlich Export-, Sammel- oder Zwischenstandsdatei.
- `bash.exe.stackdump`
  Crash-/Fehlerartefakt.

## Was sich in `resources/` befindet

`resources/` ist der wichtigste Ordner im Repository:

- `resources/modules/`
  Die eigentlichen Lehrinhalte, gegliedert nach Block 1 bis 3.
- `resources/demos/`
  Demo-Skripte und Vorfuehrablaeufe passend zu den Blocks.
- `resources/exercises/`
  Hands-on-Uebungen fuer Teilnehmer.
- `resources/prerequisites.md`
  Setup- und Vorbereitungsanleitung.
- `resources/session-plan.md`
  Zeitlicher Ablauf und didaktischer Aufbau ueber alle Sessions.
- `resources/cheatsheet.md`
  Schnelle Referenz fuer waehrend oder nach dem Workshop.
- `resources/workshop-guide.md`
  Begleitdokument fuer Selbststudium und Einordnung des Aufbaus.
- `resources/workshop-specials.md`
  Erklaert die workshop-spezifischen Command-/Skill-/Agent-Komponenten.
- `resources/zip-cleanup-checklist.md`
  Konservative Liste fuer das Aussortieren von Nebenartefakten vor dem Versand.

Weitere Dateien in `resources/` wie Analyseberichte, Dashboards oder Deep-Research-Abgleiche sind eher Begleit- oder Maintainer-Material als Pflichtlektuere fuer Teilnehmer.

## Was sich in `workshop-playground/` befindet

Der Playground ist das Uebungsprojekt fuer praktische Teile des Workshops:

- `access_control.py`
  Beispielcode fuer Demos und Uebungen.
- `test_access_control.py`
  Tests fuer den Playground.
- `requirements.txt`
  Python-Abhaengigkeiten.
- `CLAUDE.md`
  Projektinstruktionen fuer Claude Code im Playground-Kontext.

## Empfehlung fuer verschiedene Leser

- Moderator oder Maintainer:
  `README.md`, `resources/session-plan.md`, `resources/workshop-specials.md`
- Teilnehmer:
  `resources/prerequisites.md`, `resources/workshop-guide.md`, `resources/cheatsheet.md`
- Selbstlerner ohne Workshop:
  `WORKSHOP_EINFUEHRUNG.md`, `resources/workshop-guide.md`, dann blockweise `modules/`, `demos/`, `exercises/`

