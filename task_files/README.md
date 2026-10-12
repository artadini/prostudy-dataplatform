English
---

# Prostudy – Analytics Engineer Application Task

This repository contains the task and all materials for the technical assignment in the application process.

**→ You can find the task description in [`AUFGABE.md`](AUFGABE.md).**

## Contents

```
.
├── AUFGABE.md                      # Task description – start here
├── api/
│   ├── server.py                   # local test API (CRM + LXP), Python standard library only
│   ├── README.md                   # API documentation: auth, pagination, filters, data model
│   ├── Dockerfile                  # optional: run the API in a container
│   └── data/                       # raw data served by the API (please do not read directly*)
└── data/
    └── coach_betreuungsliste.xlsx  # manually maintained coach list
```

\* The CSV files under `api/data/` are the server’s underlying data source. Please use the **API** in your pipeline, not the files – working with the API is part of the task.

## Quick Start

```bash
python api/server.py
# → http://127.0.0.1:8000/docs
```

Login: `prostudy` / `learn-data-2026`

## Submission

Fork or copy this repository with your solution (link or ZIP) – see `AUFGABE.md`, section 6, for details.

=====

German
---

# Prostudy – Bewerbungsaufgabe Analytics Engineer

Dieses Repository enthält die Aufgabe und alle Materialien für die technische Aufgabe im Bewerbungsprozess.

**→ Die Aufgabenstellung findest du in [`AUFGABE.md`](AUFGABE.md).**

## Inhalt

```
.
├── AUFGABE.md                      # Aufgabenstellung – hier anfangen
├── api/
│   ├── server.py                   # lokale Test-API (CRM + LXP), nur Python-Standardbibliothek
│   ├── README.md                   # API-Dokumentation: Auth, Pagination, Filter, Datenmodell
│   ├── Dockerfile                  # optional: API im Container
│   └── data/                       # Rohdaten, die die API ausliefert (bitte nicht direkt einlesen*)
└── data/
    └── coach_betreuungsliste.xlsx  # manuell gepflegte Coach-Liste
```

\* Die CSV-Dateien unter `api/data/` sind die Datenbasis des Servers. Bitte nutze in deiner Pipeline die **API**, nicht die Dateien – der Umgang mit der API ist Teil der Aufgabe.

## Schnellstart

```bash
python api/server.py
# → http://127.0.0.1:8000/docs
```

Login: `prostudy` / `learn-data-2026`

## Abgabe

Fork oder Kopie dieses Repos mit deiner Lösung (Link oder ZIP) – Details in `AUFGABE.md`, Abschnitt 6.
