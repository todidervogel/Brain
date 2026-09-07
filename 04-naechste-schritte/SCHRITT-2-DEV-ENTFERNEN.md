# Schritt 2 — Alles Entwicklerzeug entfernen

Ausdrücklich angekündigt: *„im Schritt 2 entfernen wir dann alle Dev-Dinger,
die jetzt noch da sind."*

Diese Liste ist die Vorbereitung dafür. **Noch nichts davon ist gemacht** —
die Sachen werden für die interne Abnahme gebraucht.

## Streichliste

| # | Was | Wo | Anmerkung |
|---|---|---|---|
| 1 | **Design-Panel** | `src/components/layout/DevPanel.jsx`, eingebunden in `src/App.jsx` | Datei löschen, Einbindung entfernen, CSS `.dev-panel` / `.dev-toggle` aufräumen |
| 2 | **Screen-Übersicht** | `src/routes/ScreenIndex.jsx`, Route `/uebersicht` | Route und Datei entfernen. `src/routes/index.js` wird dann nur noch vom Router gebraucht |
| 3 | **Zustandsschalter „leer / ladend"** | `useVariant` und `useListQuery` in `src/lib/design-state.jsx`, `state`/`setState` | Etwa 30 Aufrufstellen: `useVariant(useQuery(…))` wird zu `useQuery(…)` |
| 4 | **Ziel-Umschalter Website ⇄ App** | `setPlatform` in `src/lib/design-state.jsx` | `platform` bleibt, wird aber nur noch erkannt, nicht mehr gesetzt |
| 5 | **Rollen-Schnellwechsel** | `switchTo` in `src/lib/session.jsx` | Nur fürs Panel da. Ersatzlos streichen |
| 6 | **„Daten zurücksetzen"** | `resetDb` in `src/lib/store/db.js` | Export entfernen, sobald die Daten vom Server kommen |
| 7 | **Testkonten-Hinweise** | `auth.demoHint`, `auth.codeHint`, `dev.passwords` in `src/i18n/de.json` | Die Zeilen unter Anmeldung, Gastro-Anmeldung und Code-Eingabe |
| 8 | **Fester Bestätigungscode `123456`** | `src/lib/session.jsx`, `confirmRegistration` | Ersetzen durch echten SMS-Versand |
| 9 | **Künstliche Verzögerung** | `LATENCY` in `src/lib/store/api.js` | Fällt mit dem Umstieg auf den Server ohnehin weg |
| 10 | **Klartext-Passwörter** | `password` in `src/data/seed.js` und `users` | Löst sich mit Supabase Auth auf. **Vorher darf nichts davon nach draußen** |
| 11 | **Aufbau-Banner** | `src/components/layout/Banners.jsx` | Bleibt, solange „App befindet sich im Aufbau" stimmt (MVP 0). Danach weg |
| 12 | **Prüfskripte** | `tools/pruefung/` | Können bleiben, gehören aber nicht ins Auslieferungspaket |

## Prüfung nach dem Aufräumen

1. `grep -rn "DevPanel\|useVariant\|switchTo\|resetDb\|demoHint\|codeHint" src/`
   liefert nichts mehr.
2. `npx vite build` läuft durch.
3. Der Routen-Sweep läuft ohne Fehler — mit angepasster Routenliste, weil
   `/uebersicht` weg ist.
4. Die APK startet und zeigt ohne Anmeldung die Anmeldeseite.

## Wichtig: Reihenfolge

Schritt 2 lohnt sich **nach** der internen Abnahme, nicht davor. Solange
Screens beurteilt werden, braucht man den Zustandsschalter und den
Rollenwechsel — sonst muss man sich für jeden Blick auf einen leeren Zustand
erst ein Konto leerräumen.

Die einzige Ausnahme ist Punkt 10: Sobald irgendetwas davon öffentlich
erreichbar wird, dürfen dort keine Passwörter mehr stehen, auch keine
erfundenen. Sonst gewöhnt man sich das Falsche an.
