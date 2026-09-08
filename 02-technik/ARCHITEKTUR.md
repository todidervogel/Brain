# Architektur der Logikschicht

Stand: 08.09.2026 · Repositories `Server` und `Website-`, Branch `main`

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
        ├── ohne Adresse ──► domain/calls.js ──► localStorage
        │
        └── mit  Adresse ──► HTTP ──► Server ──► domain/calls.js ──► SQLite
```

Die Adresse kommt seit Runde 9 aus zwei Quellen, in dieser Reihenfolge:
was im Gerät eingestellt ist (*Einstellungen → Verbindung*), sonst `VITE_API`
vom Bauen. Grund: Die APK wird einmal gebaut, der Server zieht öfter um.

`domain/` ist beide Male dieselbe Fachlogik — synchron, ohne Browser, ohne
Netz, auf einem eingehängten Store. Kein Screen greift direkt darauf zu.

Beim Umzug auf Supabase wird die Datenhaltung unter `domain/` ersetzt und die
Rechte aus `domain/calls.js` werden zu RLS-Policies. Die Screens merken davon
nichts, weil sie schon heute mit Versprechen arbeiten und Lade- und
Fehlerzustände kennen.

## Die Dateien im Einzelnen

| Datei | Aufgabe |
|---|---|
| `Server/src/data/seed.js` | Der Ausgangsbestand: 360 echte Betriebe und drei Zugänge. **Keine Beispielinhalte mehr** — kein Video, keine Bewertung, keine Speisekarte. |
| `Server/src/data/orte.js` | Die importierten Betriebe. Erzeugt von `tools/osm-import.mjs`, nicht von Hand ändern. |
| `Server/src/data/anreicherung.js` | Beschreibungen über die importierten Daten, mit Quelle und Datum. Überlebt jeden neuen Import. |
| `Server/src/domain/store.js` | Der eingehängte Datenzugriff: `get`, `update`, `insert`, `patch`, `remove`, `nextId` — und `pruefePasswort`/`setzePasswort`. Der Server hängt SQLite ein, die Website den Browserspeicher. |
| `Server/src/store/schema.js` | Das Datenbankschema: 15 Tabellen mit Typen, Bedingungen und Indizes. |
| `Server/src/store/sqlite-store.js` | Die Datenbank. Liest beim Start alles ein, schreibt jede Änderung sofort weiter. |
| `Server/src/store/zugaenge.js` | Passwörter: scrypt, eigenes Salz, eigene Tabelle. |
| `Server/src/store/sitzungen.js` | Anmeldungen in der Datenbank — der Neustart wirft niemanden hinaus. |
| `Server/src/http/karte.js` | Kacheln, Zwischenspeicher, Marker im Ausschnitt. |
| `Server/src/http/kachelbild.js` | Zeichnet eine Ersatzkachel, wenn keine zu bekommen ist. Ein PNG von Hand. |
| `Server/src/domain/titelbild.js` | Zeichnet das Kopfbild eines Betriebs. In der Fachlogik, weil die Website es im Alleinbetrieb auch braucht. |
| `Server/src/domain/calls.js` | **Die Aufrufliste.** Was es gibt und wer es darf. Beide Wirte benutzen sie. |
| `Server/src/domain/geo.js` | Luftlinie (Haversine), deutsche Entfernungsschreibweise, Umrechnung auf die Kartenfläche. |
| `Server/src/domain/hours.js` | Öffnungszeiten in Minuten seit Mitternacht; rechnet „jetzt geöffnet" wirklich aus, auch über Mitternacht hinaus. |
| `Website-/src/lib/store/index.jsx` | React-Anbindung: `useQuery`, `useMutation`. |
| `Website-/src/lib/session.jsx` | Anmeldung, Abmeldung, Registrierung mit Bestätigungscode, Passwortwechsel, Rollen. |
| `Website-/src/lib/form.js` | `useForm` mit Regelwerk (`rules.required`, `.email`, `.password`, `.matches`, …) und deutschen Fehlermeldungen. |
| `Website-/src/lib/upload.jsx` | Der Entwurf des Upload-Assistenten über fünf Schritte hinweg, mit Schrittwächter. |
| `Website-/src/lib/auth.jsx` | Routenwächter und die Schranke „dafür brauchst du ein Konto". |
| `Website-/src/lib/design-state.jsx` | Oberflächenzustand: Ziel (Website/App), Gerät, Darstellung, Umkreis, Position, reine Kartenansicht. |

## Wo jede Datei sagt, woran sie hängt

Seit Runde 9 hat **jede** Datei in `Server/src/domain/` und `Server/src/http/`
oben einen Kasten:

```
 ┌─ Wer benutzt diese Datei ────────────────────────────────┐
 │  src/domain/calls.js    social.* — alles nur angemeldet  │
 │  src/domain/derive.js   viewerLiked / viewerSaved        │
 │  src/domain/users.js    räumt beim Löschen eines Kontos auf │
 └──────────────────────────────────────────────────────────┘
```

Nicht Zierde: Wer eine Datei ändert, sieht ohne Suche, was daran hängt. Der
zweite Absatz darunter sagt jeweils, **warum** etwas so ist — nicht, was der
Code tut. Das steht im Code.

## Die künstliche Verzögerung ist weg

Im Alleinbetrieb wartete `api.js` früher zwischen 130 und 400 ms, damit man
Ladezustände sieht (`VITE_LATENCY`). Ein Entwicklerstück in dem, was
ausgeliefert wird — seit Runde 8 heraus. Die Ladezustände werden jetzt dort
geprüft, wo es sie wirklich gibt: gegen einen Server, mit einer absichtlich
verzögerten Route (`Website-/tools/gegen-server.mjs`).

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
- **Die Karte lässt sich nicht bedienen.** Kacheln kommen seit Runde 9 vom
  eigenen Server, und die Marker sitzen richtig — aber Verschieben und Zoomen
  mit Maus und Finger fehlt noch.
- **Kein echtes GPS.** Die Position steht auf Oberkirch und lässt sich über
  die Ortssuche verschieben.
- **Keine E-Mails, keine SMS.** Der Bestätigungscode lautet immer `123456` —
  oder man drückt „Überspringen". Eine Pflicht zur Bestätigung ohne Absender
  wäre eine Tür ohne Schlüssel.
- **Passwörter:** Mit Server gehasht (scrypt, eigenes Salz, eigene Tabelle).
  Im Alleinbetrieb im Browser stehen sie im Klartext im `localStorage` — dort
  schützt ein Hash niemanden, der ohnehin dieselbe Datei lesen kann. Trotzdem
  gilt: hier gehören ausschließlich erfundene Konten hinein.

## Geprüft wird mit drei Skripten

Unter `Website-/tools/`:

| Skript | Was es prüft | Dauer |
|---|---|---|
| `i18n-check.mjs` | jeden `t('…')`-Aufruf gegen `de.json` | Sekunden, kein Browser |
| `routen-sweep.mjs` | alle Routen in fünf Rollen auf Fehler und Platzhalter | wenige Minuten |
| `verhalten.mjs` | 30 Abläufe: Anmeldung, Rollen, Upload, Speisekarte, Admin | wenige Minuten |
| `bilder.mjs` | Bildschirmfotos in vier Breiten, plus Überlaufprüfung | wenige Minuten |
| `karte-pruefen.mjs` | die Kartenrechnung gegen bekannte Koordinaten | Sekunden |
| `verbindung.mjs` | wie sich die Anwendung ohne Server verhält | wenige Minuten |

`verhalten.mjs` und `bilder.mjs` spielen ihre Daten selbst ein
(`tools/pruefbestand.mjs`) — der Ausgangsbestand ist leer, seit die
Beispieldaten heraus sind.

Im Repo `Server` dazu: `npm test` mit 78 Prüfungen in drei Dateien —
Rauchtest, Datenbank über einen echten Neustart, und die Karte.
