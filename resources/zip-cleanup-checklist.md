# Zip Cleanup Checklist

Diese Liste ist bewusst konservativ. Sie markiert zuerst nur Artefakte, die sehr wahrscheinlich lokal, temporaer oder fuer Empfaenger unnoetig sind.

## Sicher aussortierbar vor dem Versand

- `.agent-memory/`
  Lokale Agent-Memory, Status- und Lernartefakte aus der Erstellung.
- `resources/.agent-memory/`
  Weitere Memory-/Session-Artefakte innerhalb von `resources/`; gehoeren nicht zum eigentlichen Kursmaterial.
- `.idea/`
  Lokale IntelliJ-/IDE-Konfiguration.
- `workshop-playground/__pycache__/`
  Python-Bytecode-Cache.
- `bash.exe.stackdump`
  Crash-Artefakt im Projekt-Root.
- `resources/bash.exe.stackdump`
  Zweites Crash-Artefakt innerhalb von `resources/`.
- `Last_session.txt`
  Lokale Sitzungsspur, nicht Teil des Workshops.

## Nur entfernen, wenn du sie nicht bewusst mitliefern willst

- `.claude-plugin/`
  Nur noetig, wenn der Versand das Plugin explizit installierbar enthalten soll.
- `dynamic_workshop.txt`
  Wirkt wie Export-, Sammel- oder Zwischenstandsdatei.
- `files/`
  Enthaelt aeltere oder alternative Workshop-Dokumente; fuer Empfaenger vermutlich nicht noetig, wenn das aktuelle Material in `resources/` und im Root ausreicht.
- `docs/superpowers/`
  Aktuell eher Platzhalter-/Arbeitsstruktur als zentrales Workshop-Material.
- `deep-research-report.md`
  Umfangreiches Hintergrundmaterial fuer Maintainer; fuer Teilnehmer eher optional.
- `Claude Code_ Skills, Use Cases, Einfuehrung.md`
  Einzelne Zusatzreferenz; nur mitliefern, wenn genau dieses Hintergrundpapier Teil des Pakets sein soll.
- `resources/deep-research-gap-analysis.md`
  Interner Abgleich zwischen Recherche und Workshop.
- `resources/workshop-analysis-report.md`
  QA-/Analysepapier, fuer Teilnehmer nicht zwingend.
- `workshop-dashboard.html`
  Zusatzdashboard im Root.
- `resources/workshop-learning-dashboard.html`
  Weiteres Dashboard; nur noetig, wenn die Empfaenger damit wirklich arbeiten sollen.

## Eher behalten

Diese Bestandteile wuerde ich fuer ein normales Workshop-Paket nicht entfernen:

- `README.md`
- `WORKSHOP_EINFUEHRUNG.md`
- `resources/prerequisites.md`
- `resources/session-plan.md`
- `resources/cheatsheet.md`
- `resources/modules/`
- `resources/demos/`
- `resources/exercises/`
- `resources/workshop-guide.md`
- `resources/workshop-specials.md`
- `workshop-playground/` ohne `__pycache__`

## Wenn du auch die Workshop-Specials mitgeben willst

Dann ebenfalls behalten:

- `commands/`
- `skills/`
- `agents/`
- `.claude-plugin/`

Ohne diese Teile bleibt das Material als Dokumentations-/Workshop-Paket nutzbar, aber nicht mehr als zusammenhaengende Claude-Code-Erweiterung.

## Praktische Versandvarianten

Wenn du unsicher bist, denke in zwei Paketen:

- Teilnehmerpaket:
  nur Kernmaterial + Playground
- Maintainer-/Moderatorpaket:
  Kernmaterial + Playground + Specials + optionale Analyseunterlagen

So musst du nicht alles in ein einziges Archiv pressen.

