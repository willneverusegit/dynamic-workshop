# Workshop Specials

## Was mit "Specials" gemeint ist

Mit "Specials" sind in diesem Repository die workshop-spezifischen Erweiterungen gemeint, die ueber das reine Kursmaterial hinausgehen. Sie machen den Workshop in Claude Code interaktiv, sind aber keine allgemeine Pflicht fuer das Verstehen der Module.

Wichtig ist die Trennung:

- Die Workshop-Inhalte liegen in `resources/`.
- Die Specials sind die Mechanik, mit der diese Inhalte innerhalb von Claude Code komfortabel genutzt werden koennen.

## Uebersicht

| Komponente | Typ | Zweck | Wo definiert |
| --- | --- | --- | --- |
| `/workshop` | Custom Command | Einstiegspunkt fuer Uebersicht, Guide- und Learn-Modus | `commands/workshop.md` |
| `workshop` | Custom Skill | Laedt je nach Modus passende Module, Demos oder Exercises | `skills/workshop/SKILL.md` |
| `workshop-mentor` | Custom Agent | Beantwortet gezielte Rueckfragen zu Workshop-Themen | `agents/workshop-mentor.md` |
| `dynamic-workshop` | Plugin-Manifest | Verknuepft Command, Skill und Agent | `.claude-plugin/plugin.json` |

## 1. Der `/workshop` Command

Der Command `/workshop` ist kein eingebauter Claude-Code-Befehl, sondern eine workshop-spezifische Erweiterung.

Er ist gedacht fuer drei Situationen:

- Uebersicht sehen
- einen Modulabschnitt im Moderator-Modus laden
- einen Modulabschnitt im Lernmodus laden

Typische Aufrufe:

```text
/workshop
/workshop guide 1.1
/workshop learn 2.3
```

Bedeutung:

- `/workshop`
  Zeigt die Gesamtstruktur des Workshops.
- `/workshop guide X.X`
  Trainer-/Moderator-Modus. Fokus auf Talking Points, Timing, Demo-Skripte und Uebergaenge.
- `/workshop learn X.X`
  Selbstlern-Modus. Fokus auf Erklaerung, Beispiel, Uebung und Verifikation.

## 2. Das `workshop` Skill

Das Skill in `skills/workshop/SKILL.md` ist die eigentliche Logik hinter dem Command. Der Command reicht nur weiter, was das Skill dann ausfuehrt.

Dieses Skill:

- erkennt den gewuenschten Modus
- ordnet Modulnummern dem passenden Block zu
- laedt die richtigen Dateien aus `resources/modules/`, `resources/demos/` oder `resources/exercises/`
- formatiert die Antwort fuer Moderator oder Selbstlerner passend

Praktisch heisst das:

- Der Command ist der Einstieg.
- Das Skill ist die eigentliche Ablaufsteuerung.

## 3. Der Agent `workshop-mentor`

`workshop-mentor` ist der auffaelligste Spezialfall in diesem Repository. Er ist ein bewusst eng definierter Agent fuer Rueckfragen waehrend oder nach dem Workshop.

Wofuer er gedacht ist:

- kurze Verstaendnisfragen beantworten
- sagen, welches Modul ein Thema abdeckt
- Begriffe knapp und praxisnah erklaeren
- bei Bedarf Security-Analogien verwenden

Wofuer er nicht gedacht ist:

- automatisch bei allgemeinen Fragen anspringen
- das komplette Workshop-Skill ersetzen
- eigenstaendig den Kurs durchfuehren

In seiner Definition steht ausdruecklich:

- nur nutzen, wenn der Nutzer ihn explizit beim Namen anfordert
- bei normalen Workshop-Fragen lieber direkt antworten statt den Agent zu spawnen

Das ist sinnvoll, weil sonst jede Fachfrage unnoetig in einen Agenten delegiert wuerde, obwohl das Hauptsystem sie selbst beantworten kann.

## So verwendest du `workshop-mentor`

Sinnvolle Beispiele:

```text
Frag den workshop-mentor: Was ist der Unterschied zwischen Skills und Commands?
Ask the workshop-mentor which module covers hooks.
```

Weniger sinnvoll:

```text
Erklaer mir den Workshop von Anfang an.
```

Fuer so etwas ist `/workshop` oder das eigentliche Material in `resources/` besser geeignet.

## 4. Das Plugin-Manifest

In `.claude-plugin/plugin.json` wird beschrieben, welche Bausteine zu diesem Workshop-Plugin gehoeren:

- der Command
- der Agent
- das Skill

Das Manifest ist also keine Inhaltsdatei, sondern die Verdrahtung der Workshop-Erweiterung. Ohne diese Datei haetten die einzelnen Teile keinen gemeinsamen Plugin-Rahmen.

## Offizielles Produktfeature vs. Workshop-Spezial

Diese Unterscheidung ist fuer Einsteiger wichtig:

### Offizielle Claude-Code-Konzepte

- Skills als Konzept
- Commands als Konzept
- Agents als Konzept
- Plugin-/Erweiterungsmechanik
- Arbeit mit Dateien, Shell, Kontext und Regeln

### Workshop-spezifische Umsetzung in diesem Repo

- der konkrete Command `/workshop`
- das konkrete `workshop` Skill
- der konkrete Agent `workshop-mentor`
- die konkrete Struktur der Modul-, Demo- und Exercise-Dateien

Anders gesagt:

- Claude Code bringt die Plattform-Idee mit.
- Dieses Repository liefert darauf eine spezifische Workshop-Anwendung.

## Empfohlene Nutzung

Wenn du den Workshop durcharbeitest, nutze die Specials in dieser Reihenfolge:

1. Erst `WORKSHOP_EINFUEHRUNG.md` und `resources/workshop-guide.md` lesen
2. Dann `/workshop` fuer den Gesamtueberblick ausprobieren
3. Danach `/workshop learn X.X` fuer einzelne Module nutzen
4. Den `workshop-mentor` nur fuer punktuelle Rueckfragen einsetzen

Fuer Moderatoren:

- `/workshop guide X.X` ist das wichtigste Special

Fuer Teilnehmer und Selbstlerner:

- `/workshop learn X.X` ist der wichtigste Einstieg

## Kurzfazit

Die Specials sind nicht der Workshop selbst, sondern die Bedienoberflaeche und Lernunterstuetzung fuer den Workshop innerhalb von Claude Code. Besonders `workshop-mentor` ist als gezielte Rueckfragehilfe gedacht, nicht als dauerhafter Hauptmodus.

