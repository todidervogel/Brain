# Warum vier Repositories, und wie sie zusammenhängen

Bis zum 07.09.2026 lag alles in `design`. Auf Wunsch ist es jetzt aufgeteilt.
Diese Seite beschreibt die Schnitte und die Stellen, an denen es weh tut.

## Die Schnitte

| Repository | Enthält | Kennt **nicht** |
|---|---|---|
| `design` | Farben, Schrift, Bausteine, alle Texte, Vokabular | Daten, Anmeldung, Netz, Routen |
| `Server` | Fachlogik, Datenhaltung, HTTP, Rechte | React, Browser, Gestaltung |
| `Website-` | Screens, Routen, Formulare, Sitzung |, führt beides zusammen |
| `App` | Android-Projekt, Bauweg | Oberflächencode |

### Warum kennt `design` keine Daten?

Weil ein Baustein sonst nur im Zusammenhang der ganzen Anwendung zu betrachten
ist. `PlaceRow` griff früher selbst auf Sitzung und Fassade zu; jetzt kommt
alles als Eigenschaft herein:

```jsx
<PlaceRow place={betrieb} openText="Jetzt geöffnet · bis 22:00"
          saved={gespeichert} onToggleSave={…} />
```

Die Verdrahtung liegt in der Website (`components/PlaceRowConnected.jsx`).
Dadurch lässt sich die Galerie ohne Server starten, und man sieht jeden
Baustein einzeln.

### Warum kennt `Server` kein React?

Die Fachlogik ist synchron, ohne Netz, ohne Browser. Sie arbeitet auf einem
eingehängten „Store", der Server hängt eine Datei ein, die Website den
Browserspeicher. Dieselben Funktionen, zwei Wirte.

Das ist der Grund, warum die Website auch ohne Server läuft und sich dabei
**genauso** verhält.

## Der wunde Punkt: Kopien

`Website-/src/design` und `Website-/src/domain` sind **Kopien**, eingecheckt.

Das ist bewusst und hat einen Preis:

| Dafür | Dagegen |
|---|---|
| `npm install && npm run dev` genügt | Kopien laufen auseinander, wenn niemand abgleicht |
| Kein Paketregister, kein Netz, keine Submodule | Wer in die Kopie schreibt, verliert es |
| Die App baut ohne Zusatzschritte | Drei Repos müssen zusammenpassen |

Der Abgleich ist ein Befehl:

```bash
cd Website- && npm run sync          # holt beides frisch
npm run sync:design -- --from ../design   # oder aus einem lokalen Ordner
```

**Wann abgleichen?** Immer nach einer Änderung in `design` oder `Server`.
Am besten sofort, sonst merkt man es erst, wenn etwas fehlt.

Die Alternative wäre ein Monorepo mit Arbeitsbereichen gewesen, wie im
Konzept (Abschnitt 4) eigentlich vorgesehen. Die Aufteilung auf eigene
Repositories war der ausdrückliche Wunsch; der Abgleich per Skript ist die
schmerzärmste Art, sie umzusetzen.

## Die Aufrufliste

`domain/calls.js` ist die wichtigste Datei der ganzen Aufteilung. Sie sagt,
welche Aufrufe es gibt und wer sie machen darf:

```js
'menu.addDish': {
  who: 'gastro',
  guard: (ctx, [placeId]) => ownsPlace(ctx, placeId),
  call: (ctx, [placeId, categoryId, dish]) => menu.addDish(placeId, categoryId, dish),
},
```

Beide Wirte benutzen sie: der Server hinter der Anmeldung mit Token, die
Website im Alleinbetrieb mit der lokalen Sitzung. Dadurch

- verhalten sich beide Betriebsarten gleich,
- stehen die Rechte an genau einer Stelle,
- und was hier nicht steht, ist nicht möglich, auch nicht mit curl.

Hier stehen später die RLS-Policies von Supabase (Konzept Abschnitt 7).

## Zwei Betriebsarten in der Website

```
ohne VITE_API           mit VITE_API
─────────────           ────────────
Screens                 Screens
   │                       │
   ▼                       ▼
api.places.list()       api.places.list()
   │                       │
   ▼                       ▼
domain/calls.js         HTTP → Server → domain/calls.js
   │                                       │
   ▼                                       ▼
localStorage                            SQLite (data/tellerrand.db)
```

Die Screens merken den Unterschied nicht: `api.places.list({…})` gibt beide
Male ein Versprechen zurück.

Welcher Weg gilt, entscheidet die Serveradresse: was im Gerät eingestellt ist
(*Einstellungen → Verbindung*), sonst `VITE_API` vom Bauen, sonst keiner.

## Was sich beim Aufteilen sonst geändert hat

**Öffnungszeiten liefern Zustand statt Sätze.** Die Fachlogik kennt keine
Sprache mehr; aus `{ open: false, nextDay: 'tomorrow', nextAt: '08:00' }` baut
die Website „Geschlossen · öffnet morgen 08:00" (`lib/hours-text.js`). Sonst
hätte der Server Deutsch gesprochen.

**Der Zustand der Betrachterin reist mit.** „gemerkt", „gefällt mir" und der
Folgen-Zustand hängen jetzt an den Daten (`viewerSaved`, `viewerLiked`,
`viewerFollow`), statt einzeln abgefragt zu werden. Über das Netz ginge das
ohnehin nicht synchron, und es spart pro Liste ein Dutzend Nachfragen.

**Vier Screens fassten die Daten direkt an.** Sie gehen jetzt über die
Aufrufliste und damit durch die Rechteprüfung.

**„Passwort vergessen" sagt die Wahrheit.** Es gibt keinen E-Mail-Versand,
also gibt es auch kein Zurücksetzen ohne Nachweis. Die Seite sagt das offen,
statt so zu tun, als käme gleich eine Mail.
