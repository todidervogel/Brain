# Schritt 2. Alles Entwicklerzeug entfernen

Ausdrücklich angekündigt: *„im Schritt 2 entfernen wir dann alle Dev-Dinger,
die jetzt noch da sind."*

**In Runde 8 zum größten Teil erledigt.** Was jetzt noch hier steht, ist das,
was bewusst geblieben ist, und der Grund dafür.

## Zuerst: der einzige harte Punkt

**Der Verwaltungszugang `topic` / `admin` muss weg**, bevor die Anwendung
echte Nutzerdaten sieht. In Runde 8 ausdrücklich so bestellt und vorläufig
gebaut; `admin` ist kein Passwort, sondern ein Platzhalter.

Dasselbe gilt für die Klartext-Passwörter im Ausgangsbestand. Beides löst sich
mit einer echten Anmeldung (Supabase Auth) auf.

## Erledigt in Runde 8

| Was | Wie |
|---|---|
| Design-Panel | Datei und Einbindung entfernt |
| Screen-Übersicht `/uebersicht` | Route, Datei und Routentabelle entfernt. Der Routen-Sweep liest die Adressen jetzt direkt aus dem Router, eine Liste daneben hätte irgendwann nicht mehr gepasst |
| Zustandsschalter `useVariant` | An 21 Stellen aufgelöst, Funktion entfernt |
| Ziel-Umschalter Website ⇄ App | `platform` wird nur noch erkannt, nicht mehr gesetzt |
| Rollen-Schnellwechsel `switchTo` | Ersatzlos gestrichen |
| Testkonten-Hinweise unter der Anmeldung | Entfernt. Die Texte bleiben im `design`-Repo, dort gehören sie hin |
| Künstliche Verzögerung `VITE_LATENCY` | Entfernt |
| Offener Reset-Endpunkt | Jetzt nur für die Verwaltung. Ohne Anmeldung 403, hinter einem ngrok-Link war das vorher ein Löschknopf für jeden, der die Adresse kennt |

## Was geblieben ist, und warum

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
3. Der Routen-Sweep läuft ohne Fehler, mit angepasster Routenliste, weil
   `/uebersicht` weg ist.
4. Die APK startet und zeigt ohne Anmeldung die Anmeldeseite.

## Wichtig: Reihenfolge

Schritt 2 lohnt sich **nach** der internen Abnahme, nicht davor. Solange
Screens beurteilt werden, braucht man den Zustandsschalter und den
Rollenwechsel, sonst muss man sich für jeden Blick auf einen leeren Zustand
erst ein Konto leerräumen.

Die einzige Ausnahme ist Punkt 10: Sobald irgendetwas davon öffentlich
erreichbar wird, dürfen dort keine Passwörter mehr stehen, auch keine
erfundenen. Sonst gewöhnt man sich das Falsche an.
