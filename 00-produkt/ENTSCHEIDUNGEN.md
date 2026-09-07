# Entscheidungen

Chronologisch, neueste zuletzt. Kurz halten. Auch verworfene Wege notieren —
sonst diskutiert man sie in drei Monaten wieder.

---

## E1 · Produktname bleibt offen (Runde 1)

`APP_NAME` steht an genau einer Stelle (`design/src/config.js`), Arbeitstitel
**Tellerrand**. In Texten wird `{{app}}` eingesetzt, nie der Name direkt.
Umbenennen heißt: eine Zeile ändern.

## E2 · Alle Texte in einer Datei (Runde 1)

`design/src/i18n/de.json`. Kein Text steht im JSX. Grund: Das Konzept
verlangt Mehrsprachigkeit ab Tag 1 (Deutschland zuerst, USA danach). Fehlt
ein Schlüssel, erscheint der Schlüssel selbst — dann fällt die Lücke sofort auf.

## E3 · Eine Codebasis für Website und App (Runde 2)

Vite + React, für Android mit Capacitor verpackt. `minSdkVersion 22` deckt
Android 9 auf dem Galaxy S9 ab. **Verworfen:** getrennte Projekte für Web und
App — dreifache Arbeit am selben Entwurf, solange nichts davon final ist.

Das Zielbild aus dem Konzept (Next.js für Web, React Native für Mobil) bleibt
gültig. Der Wechsel lohnt, wenn es einen Server gibt.

## E4 · Kein Gastmodus in der App (Runde 3)

Ausdrücklicher Wunsch. Wer die App installiert, hat sich entschieden. Auf der
Website bleibt der Gastmodus, weil Google-Treffer sonst ins Leere laufen.

## E5 · Beitragen erst nach Anmeldung (Runde 3)

Liken, folgen, speichern, hochladen, melden. Als Gast erscheint ein
Hinweisdialog statt der Aktion.

## E6 · Dunkelmodus jetzt auf beiden Zielen (Runde 5) — ändert E-alt

**Vorher:** Dunkelmodus nur in der App, Website immer hell.
**Jetzt:** beides, Standard „Automatisch" (folgt dem Gerät).

Grund: Die Website war dauerhaft weiß, und in der APK war der Dunkelmodus
nicht erreichbar — die Einstellung lag hinter der Anmeldung, und ohne
Anmeldung zeigt die App nur die Anmeldeseite.

Der Umschalter steht deshalb jetzt in der Kopfleiste, sichtbar auch auf der
Anmeldeseite.

## E7 · Neues Datenfeld `serving` (Runde 5)

Was gibt es hier zu essen und zu trinken — als Symbolzeile, nicht als
Fließtext. Zehn Werte von `getraenke` bis `glutenfrei`. Ein Betrieb mit
ausschließlich Getränken bekommt das Label „Nur Getränke".

**Verworfen:** die vorhandenen `features` dafür zu benutzen. Die mischen
Umstände (WLAN, Kartenzahlung) mit Essen (vegetarisch) und taugen deshalb
nicht als schneller Blick.

## E8 · Speisekarte als eigene Seite (Runde 5)

`/g/[slug]/speisekarte`, ohne den Rahmen der App. Auf der Gastro-Seite bleibt
ein Reiter mit Vorschau. Grund: Wer im Lokal den QR-Code scannt, will die
Karte lesen und sonst nichts. Vorbild sind die digitalen Karten, die
inzwischen in vielen Lokalen hängen.

Der Reiter heißt jetzt „Speisekarte" statt „Gerichte" — er zeigt eine
gegliederte Karte, keine lose Liste.

## E9 · Echte Logik vor echtem Server (Runde 5)

Die Datenhaltung liegt im Browser (`localStorage`), aber hinter einer
Fassade (`src/lib/store/api.js`), die sich wie ein Server verhält:
Versprechen, Verzögerung, Lade- und Fehlerzustände.

Grund: Alles, was den Ablauf betrifft — Reihenfolge, Prüfungen, Rechte,
Zustände — lässt sich so jetzt festlegen und ausprobieren. Wenn Supabase
kommt, wird eine Datei ersetzt, nicht die Anwendung.

**Verworfen:** Supabase sofort anzubinden. Das kostet ein Projekt, Schlüssel,
Migrationen und Netz — für einen internen Test, bei dem sich das
Datenmodell noch bewegt, zu früh.

## E10 · Aggregate werden gerechnet, nicht gespeichert (Runde 5)

Durchschnittsbewertungen, Videoanzahl, Entfernung, Öffnungsstatus entstehen
bei jeder Abfrage neu. Auf dem Server wird daraus eine Materialized View
(Konzept 6). Vorteil jetzt: keine Werte, die auseinanderlaufen können.

## E11 · Künstliche Verzögerung in der Fassade (Runde 5)

130–400 ms, abschaltbar über `VITE_LATENCY=0`. Ohne sie gäbe es keine
Ladezustände zu sehen — und die sollen im Entwurf stimmen.

## E12 · Design-Panel bleibt vorerst (Runde 5)

Es ist kein Produktbestandteil, aber es macht die Abnahme möglich: Ziel,
Rolle, Darstellung, leere und ladende Zustände, Banner, Daten zurücksetzen.
Fällt in **Schritt 2** weg, siehe `04-naechste-schritte/`.

## E13 · Vier Repositories statt einem (Runde 6)

Ausdrücklicher Wunsch. `design` (Bausteine), `Server` (Fachlogik),
`Website-` (Screens), `App` (Android-Hülle), `Brain` (dieses Wissen).

**Verworfen:** ein Monorepo mit Arbeitsbereichen, wie das Konzept
(Abschnitt 4) es eigentlich vorsieht. Es wäre technisch die einfachere
Lösung — die Aufteilung war aber gewünscht.

**Preis:** `Website-/src/design` und `Website-/src/domain` sind eingecheckte
Kopien. Dafür genügt `npm install && npm run dev`; dagegen laufen sie
auseinander, wenn niemand `npm run sync` ausführt.

## E14 · Fachlogik zieht auf den Server (Runde 6)

Die Fachlogik ist synchron, kennt weder Browser noch Netz und arbeitet auf
einem eingehängten Store. Der Server hängt eine Datei ein, die Website den
Browserspeicher. Dieselben Funktionen, zwei Wirte.

Folge: Die Website läuft weiterhin ohne Server und verhält sich dabei genauso.

## E15 · Rechte stehen in einer Aufrufliste (Runde 6)

`domain/calls.js` sagt zu jedem Aufruf, wer ihn machen darf. Beide Wirte
benutzen dieselbe Datei. Was dort nicht steht, ist nicht möglich.

**Verworfen:** frei zugängliche Endpunkte mit Prüfung im Frontend. Das Konzept
(Abschnitt 7) verlangt ausdrücklich Regeln, die nicht am Frontend hängen.

## E16 · Öffnungszeiten ohne Sprache (Runde 6)

Die Fachlogik liefert `{ open, until, nextDay, nextAt }`, den Satz baut die
Oberfläche. Vorher stand `t('hours.openUntil')` mitten in der Logik — auf
einem Server hat das nichts zu suchen.

## E17 · Kein Zurücksetzen des Passworts ohne Nachweis (Runde 6)

Ohne E-Mail-Versand gibt es keinen sauberen Weg. Die Seite sagt das offen,
statt einen vorzutäuschen. Ändern lässt sich das Passwort im angemeldeten
Zustand.

## E18 · Alles auf `main`, Backups als Branch (Runde 6)

Ausdrücklicher Wunsch. Der Stand vor der Aufteilung liegt in jedem Repository
als `backup/monolith-2026-09-07`.

## E19 · Die Webseite wird über GitHub Pages veröffentlicht (Runde 7)

Damit sie am Handy einfach als Adresse aufrufbar ist, ohne irgendetwas zu
installieren. Bewusst in Kauf genommen: So veröffentlicht läuft sie im
Alleinbetrieb, mit den Beispieldaten im Browser des Besuchers.

Verworfen: die Seite gleich gegen einen öffentlichen Server zu stellen. Dafür
gibt es noch keinen Hoster, und ohne https wäre es ohnehin nicht möglich
(siehe E20).

## E20 · Eine https-Seite erreicht keinen http-Server (Runde 7)

Keine Entscheidung, sondern eine Grenze, die man kennen muss: Browser
blockieren gemischte Inhalte. Eine über Pages ausgelieferte Seite kann den
Testserver im WLAN deshalb nicht ansprechen — die App dagegen schon, weil der
Debug-Bau unverschlüsselte Verbindungen ausdrücklich erlaubt
(`android/app/src/debug/AndroidManifest.xml`).

Folge für Schritt 3: Sobald der Server öffentlich unter https steht, fällt die
Einschränkung weg. Bis dahin sind die drei brauchbaren Aufbauten in
`OHNE-RECHNER.md` beschrieben.
