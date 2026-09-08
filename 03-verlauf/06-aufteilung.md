# Runde 6: Aufteilung auf vier Repositories

Datum: 07.09.2026

## Was gefragt war

> „Stop bitte mache das nicht alles in eine repo sondern verteile das
> folgendermaßen in Design, App, Website, Server repos nutze außerdem immer
> das Main Branche und speichere Backups als neuen brach im repo danke richte
> das so ein das ich alles selber testen kann es soll ein fertiger MVP sein"

Vier Punkte: aufteilen, auf `main` arbeiten, Backups als Branch sichern, alles
selbst testbar machen.

## Was gemacht wurde

### Aufgeteilt

| Repository | Was hineingezogen ist |
|---|---|
| `Server` | Fachlogik, Datenhaltung, HTTP-API, Rechteprüfung |
| `Website-` | Screens, Routen, Formulare, Sitzung |
| `App` | Android-Projekt und der Bauweg für die APK |
| `design` | Farben, Schrift, Bausteine, Texte, Vokabular, plus eine Galerie |

Die Schnitte und ihr Preis stehen in `02-technik/AUFTEILUNG.md`.

### Aus dem Prototyp wurde ein MVP

Die Aufteilung war der Anlass, das Naheliegende gleich mitzumachen: Die
Fachlogik zog auf einen **echten Server**.

- **Ohne Fremdabhängigkeiten.** `npm start` genügt, Node 20 reicht.
- **Rechte am Server.** `domain/calls.js` sagt zu jedem Aufruf, wer ihn machen
  darf. Was dort nicht steht, geht nicht, auch nicht mit curl. Das ist die
  Stelle, an der später die RLS-Policies stehen.
- **Anmeldung mit Zugangsmerkmal**, das ein Neuladen übersteht.
- **Datenhaltung in `data/db.json`**, atomar geschrieben.

Die Website läuft weiterhin **auch allein**: ohne `VITE_API` mit der
Fachlogik im Browser, mit `VITE_API` gegen den Server. Die Screens merken den
Unterschied nicht.

### Testbar gemacht

Jedes Repository lässt sich einzeln starten:

| Repository | Befehl | Was man sieht |
|---|---|---|
| `Server` | `npm start`, `npm test` | API auf :4000, 33 Prüfungen |
| `Website-` | `npm run dev` | alle Screens auf :5173 |
| `design` | `npm run dev` | Galerie mit jedem Baustein |
| `App` | `npm run build:apk` | eine installierbare APK |

Dazu `SELBST-TESTEN.md` in diesem Repo: von null zum laufenden MVP, mit fünf
Dingen, die man ausprobieren sollte, darunter zwei Browser mit demselben
Stand und der Nachweis, dass der Server unerlaubte Aufrufe abweist.

### Branches

Alles liegt jetzt auf `main`. Der Stand vor der Aufteilung steht in jedem
Repository als `backup/monolith-2026-09-07`; im Repo `design` liegt dort noch
der vollständige gemeinsame Prototyp. Der Arbeitszweig
`claude/app-website-mvp-3w6arm` bleibt unverändert daneben.

## Was dabei aufgefallen ist

**Die Oberfläche fragte Daten synchron ab.** `api.social.isLiked(…)` mitten im
Zeichnen, über das Netz unmöglich. „gemerkt", „gefällt mir" und der
Folgen-Zustand reisen jetzt an den Daten mit.

**Die Öffnungszeiten sprachen Deutsch.** Mitten in der Fachlogik stand
`t('hours.openUntil')`. Auf einem Server hat das nichts zu suchen, er liefert
jetzt Zustand, den Satz baut die Oberfläche.

**Vier Screens fassten die Daten direkt an**, an jeder Rechteprüfung vorbei.
Im Alleinbetrieb fiel das nicht auf; auf einem Server wäre es ein Loch
gewesen. Sie gehen jetzt über die Aufrufliste.

**„Passwort vergessen" hätte ein Loch gerissen.** Der alte Weg merkte sich das
Konto und ließ danach ein neues Passwort setzen, ohne jeden Nachweis. Ohne
E-Mail-Versand gibt es keinen sauberen Weg, also sagt die Seite das jetzt
offen, statt einen vorzutäuschen.

**Der Routenwächter war zu schnell.** Weil die Sitzung jetzt asynchron
wiederhergestellt wird, hielt er beim Start jeden für abgemeldet und schickte
die App auf die Anmeldeseite. Er wartet jetzt, bis die Sitzung steht.

## Geprüft

| Wo | Was | Ergebnis |
|---|---|---|
| `Server` | 33 Prüfungen: Rechte, ganzer Ablauf, Meldungen, DSGVO | alle grün |
| `Website-` | 55 Routen × 5 Rollen = 275 Seitenaufrufe | keine Auffälligkeiten |
| `Website-` | 27 Ablauftests im Alleinbetrieb | alle grün |
| `Website-` | 7 Prüfungen gegen den Server | alle grün |
| `design` | Übersetzungslücken, Build der Galerie | grün |
| `App` | 8 Prüfungen am gepackten Android-Projekt | alle grün |

Der Serverlauf prüft ausdrücklich, was nur dort gilt: dass die Daten wirklich
von dort kommen, dass die Anmeldung ein Neuladen übersteht, dass ein
einfacher Nutzer keine Videos freigeben kann und dass zwei Browser denselben
Stand sehen.

## Was offen bleibt

Unverändert: echte Videos, echte Karte, GPS, E-Mail und SMS, Bilder,
Rechtstexte, OSM-Import. Siehe `00-produkt/OFFENE-PUNKTE.md`.

Neu dazugekommen: **die Kopien im Website-Repo müssen abgeglichen werden**,
wenn sich `design` oder `Server` ändert (`npm run sync`). Das ist der Preis
der Aufteilung.
