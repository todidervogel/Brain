# Architektur der Logikschicht

Stand: 07.09.2026 · Repositories `Server` und `Website-`, Branch `main`

> **Seit Runde 6 ist der Code aufgeteilt.** Wie die vier Repositories
> zusammenhängen, steht in [`AUFTEILUNG.md`](AUFTEILUNG.md). Diese Seite
> beschreibt die Logikschicht selbst — sie liegt jetzt im Repo `Server` und
> wird von der Website als Kopie eingespielt.

Bis Runde 4 war der Prototyp rein statisch: alle Screens vorhanden, aber
nichts davon tat etwas. Seit Runde 5 hat er eine vollständige Logik — mit
einer Datenhaltung, echter Anmeldung, Formularprüfung und Ladezuständen.

## Der Grundgedanke: eine Fachlogik, zwei Wirte

Das Konzept (Abschnitt 4) verlangt, dass externe Dienste hinter einer eigenen
Schicht liegen, damit ein Wechsel — hier auf Supabase — nicht den ganzen Code
anfasst. Der Schnitt liegt inzwischen noch tiefer:

```
  Screens (Website-/src/routes/…)
        │  kennen nur useQuery(() => api.…)
        ▼
  Website-/src/lib/store/api.js      ← wählt den Weg
        │
        ├── ohne VITE_API ──► domain/calls.js ──► localStorage
        │
        └── mit  VITE_API ──► HTTP ──► Server ──► domain/calls.js ──► data/db.json
```

`domain/` ist beide Male dieselbe Fachlogik — synchron, ohne Browser, ohne
Netz, auf einem eingehängten Store. Kein Screen greift direkt darauf zu.

Beim Umzug auf Supabase wird die Datenhaltung unter `domain/` ersetzt und die
Rechte aus `domain/calls.js` werden zu RLS-Policies. Die Screens merken davon
nichts, weil sie schon heute mit Versprechen arbeiten und Lade- und
Fehlerzustände kennen.

## Die Dateien im Einzelnen

| Datei | Aufgabe |
|---|---|
| `Server/src/data/seed.js` | Ausgangsdaten: 10 Betriebe, Speisekarten, Videos, Bewertungen, Nutzer, Meldungen, Einladungen. Struktur folgt dem Datenmodell aus Konzept Abschnitt 6. |
| `Server/src/domain/store.js` | Der eingehängte Datenzugriff: `get`, `update`, `insert`, `patch`, `remove`, `nextId`. Der Server hängt eine Datei ein, die Website den Browserspeicher. |
| `Server/src/domain/calls.js` | **Die Aufrufliste.** Was es gibt und wer es darf. Beide Wirte benutzen sie. |
| `Server/src/domain/geo.js` | Luftlinie (Haversine), deutsche Entfernungsschreibweise, Umrechnung auf die Kartenfläche. |
| `Server/src/domain/hours.js` | Öffnungszeiten in Minuten seit Mitternacht; rechnet „jetzt geöffnet" wirklich aus, auch über Mitternacht hinaus. |
| `Website-/src/lib/store/index.jsx` | React-Anbindung: `useQuery`, `useMutation`. |
| `Website-/src/lib/session.jsx` | Anmeldung, Abmeldung, Registrierung mit Bestätigungscode, Passwortwechsel, Rollen. |
| `Website-/src/lib/form.js` | `useForm` mit Regelwerk (`rules.required`, `.email`, `.password`, `.matches`, …) und deutschen Fehlermeldungen. |
| `Website-/src/lib/upload.jsx` | Der Entwurf des Upload-Assistenten über fünf Schritte hinweg, mit Schrittwächter. |
| `Website-/src/lib/auth.jsx` | Routenwächter und die Schranke „dafür brauchst du ein Konto". |
| `Website-/src/lib/design-state.jsx` | Oberflächenzustand: Ziel (Website/App), Gerät, Darstellung, Umkreis, Position, reine Kartenansicht. |

## Warum die künstliche Verzögerung?

Im Alleinbetrieb wartet `api.js` zwischen 130 und 400 ms, bevor es antwortet
(`VITE_LATENCY`, in Tests auf `0`). Ohne diese Wartezeit gäbe es keine
Ladezustände zu sehen — und genau die sollen im Entwurf stimmen. Ein
Skelett, das nie erscheint, ist kein Entwurf, sondern eine Behauptung.

## `useQuery` — der wichtigste Baustein

```js
const { data, loading, refreshing, reload } = useQuery(
  () => api.places.list({ radiusKm, serving }),
  [radiusKm, serving],
  { initial: [] },
)
```

- **Erster Aufruf:** `loading = true`, der Screen zeigt Skelette.
- **Änderung an den Daten** (jemand liket, speichert, gibt frei): die Abfrage
  läuft still nach, `refreshing = true`, der Inhalt bleibt stehen. Sonst
  würde die Seite bei jedem Klick blinken.
- **Änderungen** über `api.…` benachrichtigen alle Abfragen automatisch —
  ein Klick auf „Gefällt mir" im Feed ändert sofort auch die Zahl im Profil.

## Ableitungen statt gespeicherter Doppelwerte

Durchschnittsbewertungen, Videoanzahl, Entfernung und Öffnungsstatus stehen
**nicht** in den Daten, sondern werden bei jeder Abfrage berechnet
(`decoratePlace`, `ratingOf`, `dishRatingOf`). Das entspricht der
Materialized View aus Konzept Abschnitt 6 — auf dem Server wird daraus eine
Sicht, hier ist es eine Funktion.

Folge: Wer eine Bewertung abgibt, sieht den neuen Durchschnitt sofort auf der
Gastro-Seite, in der Suche und auf der Karte. Ohne Nachrechnen von Hand.

## Was die Logik heute wirklich tut

- **Anmeldung** prüft gegen die Kontenliste, kennt Rollen (`user`, `gastro`,
  `admin`) und erzwingt beim Gastro-Erstlogin einen Passwortwechsel.
- **Registrierung** prüft Format, Doppelvergabe und die Altersgrenze 16
  (Art. 8 DSGVO), verlangt einen Bestätigungscode und legt dann ein Konto an.
- **Upload** führt über fünf Schritte, hält den Entwurf fest, markiert das
  Video bei unter 150 m Entfernung als „vor Ort geprüft" und legt es mit
  Status `pending_review` an — wo es sofort in der Admin-Warteschlange steht.
- **Admin** gibt frei oder lehnt ab, benachrichtigt die Autorin oder den
  Autor, schreibt ins Protokoll, bearbeitet Meldungen, sperrt Konten,
  verifiziert Betriebe, legt Einladungen an.
- **Gastro** pflegt Profil, Angebot, Öffnungszeiten und Speisekarte, lädt
  Videos hoch, antwortet auf Bewertungen und beanstandet sie.
- **Meldungen** landen wirklich in der Warteschlange. Ab drei unabhängigen
  Meldungen „dauerhaft geschlossen" bekommt ein Betrieb den Status
  `closed_reported` und einen Warnhinweis auf der Seite (Konzept 8.7).
- **Datenexport** lädt eine echte JSON-Datei herunter (Art. 15/20).
- **Kontolöschung** entfernt Profil und Videos, anonymisiert Bewertungen —
  wie in Konzept Abschnitt 10 beschrieben.

## Was die Logik bewusst nicht tut

- **Keine echten Videos.** Statt einer Kamera läuft eine Uhr, statt eines
  Videos steht eine dunkle Fläche. Das braucht Capacitor-Plugins und gehört
  in die native App.
- **Keine echte Karte.** Die Marker werden aus echten Koordinaten gerechnet
  und richtig platziert, aber unter ihnen liegen keine Kartenkacheln.
  MapLibre kommt, wenn die Daten vom Server kommen.
- **Kein echtes GPS.** Die Position steht auf Prenzlauer Berg und lässt sich
  über die Ortssuche verschieben.
- **Keine E-Mails, keine SMS.** Der Bestätigungscode lautet immer `123456`.
- **Keine Verschlüsselung.** Passwörter stehen im Klartext im Browser. Das
  ist für einen Prototyp in Ordnung und für alles andere nicht — deshalb
  gehören hier ausschließlich erfundene Konten hinein.

## Geprüft wird mit drei Skripten

Unter `Website-/tools/`:

| Skript | Was es prüft | Dauer |
|---|---|---|
| `i18n-check.mjs` | jeden `t('…')`-Aufruf gegen `de.json` | Sekunden, kein Browser |
| `routen-sweep.mjs` | alle 55 Routen in fünf Rollen auf Fehler und Platzhalter | wenige Minuten |
| `verhalten.mjs` | 27 Abläufe: Anmeldung, Rollen, Upload, Speisekarte, Admin | wenige Minuten |

Die Ladezustände sind nur zu sehen, wenn die Fassade auch wartet — für den
Verhaltenslauf lohnt `npm run build:test` (setzt `VITE_LATENCY=400`).
