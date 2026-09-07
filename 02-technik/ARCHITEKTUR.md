# Architektur der Logikschicht

Stand: 07.09.2026 · Repository `design`, Branch `claude/app-website-mvp-3w6arm`

Bis Runde 4 war der Prototyp rein statisch: alle Screens vorhanden, aber
nichts davon tat etwas. Seit Runde 5 hat er eine vollständige Logik — mit
einer Datenhaltung, echter Anmeldung, Formularprüfung und Ladezuständen.

## Der Grundgedanke: eine Fassade, ein Austauschpunkt

Das Konzept (Abschnitt 4) verlangt ausdrücklich, dass externe Dienste hinter
einer eigenen Schicht liegen, damit ein Wechsel — hier auf Supabase — nicht
den ganzen Code anfasst. Genau so ist es gebaut:

```
  Screens (src/routes/…)
        │  kennen nur useQuery(() => api.…)
        ▼
  src/lib/store/api.js         ← die Fassade. HIER wird später Supabase eingesetzt.
        │
        ▼
  src/lib/store/db.js          ← Datenhaltung: ein Baum im localStorage
        │
        ▼
  src/data/seed.js             ← Ausgangsbestand (erfundene Inhalte)
```

Kein einziger Screen greift direkt auf `db.js` oder `seed.js` zu.
Beim Umzug auf einen echten Server wird `api.js` ersetzt — die Screens
merken davon nichts, weil sie schon heute mit Versprechen (`Promise`)
arbeiten und Lade- und Fehlerzustände kennen.

## Die Dateien im Einzelnen

| Datei | Aufgabe |
|---|---|
| `src/data/seed.js` | Ausgangsdaten: 10 Betriebe, Speisekarten, Videos, Bewertungen, Nutzer, Meldungen, Einladungen. Struktur folgt dem Datenmodell aus Konzept Abschnitt 6. |
| `src/lib/store/db.js` | Ein Zustandsbaum, gespeichert im `localStorage` unter `app-db` (versioniert). `insert`, `patch`, `remove`, `update`, `subscribe`, `resetDb`. |
| `src/lib/store/api.js` | Die Fassade. Bereiche: `places`, `menu`, `videos`, `reviews`, `social`, `users`, `notifications`, `reports`, `admin`, `search`, `gastro`. Alle Funktionen geben ein Versprechen zurück, mit kleiner künstlicher Verzögerung. |
| `src/lib/store/geo.js` | Luftlinie (Haversine), deutsche Entfernungsschreibweise, Umrechnung auf die Kartenfläche. |
| `src/lib/store/hours.js` | Öffnungszeiten in Minuten seit Mitternacht; rechnet „jetzt geöffnet" wirklich aus, auch über Mitternacht hinaus. |
| `src/lib/store/index.jsx` | React-Anbindung: `useQuery`, `useMutation`, `useDbValue`, `useDbVersion`. |
| `src/lib/session.jsx` | Anmeldung, Abmeldung, Registrierung mit Bestätigungscode, Passwortwechsel, Rollen. |
| `src/lib/form.js` | `useForm` mit Regelwerk (`rules.required`, `.email`, `.password`, `.matches`, …) und deutschen Fehlermeldungen. |
| `src/lib/upload.jsx` | Der Entwurf des Upload-Assistenten über fünf Schritte hinweg, mit Schrittwächter. |
| `src/lib/auth.jsx` | Routenwächter und die Schranke „dafür brauchst du ein Konto". |
| `src/lib/design-state.jsx` | Oberflächenzustand: Ziel (Website/App), Gerät, Darstellung, Umkreis, Position, reine Kartenansicht. |

## Warum die künstliche Verzögerung?

`api.js` wartet zwischen 130 und 400 ms, bevor es antwortet
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
