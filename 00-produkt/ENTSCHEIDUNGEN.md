# Entscheidungen

Chronologisch, neueste zuletzt. Kurz halten. Auch verworfene Wege notieren, sonst diskutiert man sie in drei Monaten wieder.

---

## E1 · Produktname bleibt offen (Runde 1)

`APP_NAME` steht an genau einer Stelle (`design/src/config.js`), Arbeitstitel
**Tellerrand**. In Texten wird `{{app}}` eingesetzt, nie der Name direkt.
Umbenennen heißt: eine Zeile ändern.

## E2 · Alle Texte in einer Datei (Runde 1)

`design/src/i18n/de.json`. Kein Text steht im JSX. Grund: Das Konzept
verlangt Mehrsprachigkeit ab Tag 1 (Deutschland zuerst, USA danach). Fehlt
ein Schlüssel, erscheint der Schlüssel selbst, dann fällt die Lücke sofort auf.

## E3 · Eine Codebasis für Website und App (Runde 2)

Vite + React, für Android mit Capacitor verpackt. `minSdkVersion 22` deckt
Android 9 auf dem Galaxy S9 ab. **Verworfen:** getrennte Projekte für Web und
App, dreifache Arbeit am selben Entwurf, solange nichts davon final ist.

Das Zielbild aus dem Konzept (Next.js für Web, React Native für Mobil) bleibt
gültig. Der Wechsel lohnt, wenn es einen Server gibt.

## E4 · Kein Gastmodus in der App (Runde 3)

Ausdrücklicher Wunsch. Wer die App installiert, hat sich entschieden. Auf der
Website bleibt der Gastmodus, weil Google-Treffer sonst ins Leere laufen.

## E5 · Beitragen erst nach Anmeldung (Runde 3)

Liken, folgen, speichern, hochladen, melden. Als Gast erscheint ein
Hinweisdialog statt der Aktion.

## E6 · Dunkelmodus jetzt auf beiden Zielen (Runde 5), ändert E-alt

**Vorher:** Dunkelmodus nur in der App, Website immer hell.
**Jetzt:** beides, Standard „Automatisch" (folgt dem Gerät).

Grund: Die Website war dauerhaft weiß, und in der APK war der Dunkelmodus
nicht erreichbar, die Einstellung lag hinter der Anmeldung, und ohne
Anmeldung zeigt die App nur die Anmeldeseite.

Der Umschalter steht deshalb jetzt in der Kopfleiste, sichtbar auch auf der
Anmeldeseite.

## E7 · Neues Datenfeld `serving` (Runde 5)

Was gibt es hier zu essen und zu trinken, als Symbolzeile, nicht als
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

Der Reiter heißt jetzt „Speisekarte" statt „Gerichte", er zeigt eine
gegliederte Karte, keine lose Liste.

## E9 · Echte Logik vor echtem Server (Runde 5)

Die Datenhaltung liegt im Browser (`localStorage`), aber hinter einer
Fassade (`src/lib/store/api.js`), die sich wie ein Server verhält:
Versprechen, Verzögerung, Lade- und Fehlerzustände.

Grund: Alles, was den Ablauf betrifft, Reihenfolge, Prüfungen, Rechte,
Zustände, lässt sich so jetzt festlegen und ausprobieren. Wenn Supabase
kommt, wird eine Datei ersetzt, nicht die Anwendung.

**Verworfen:** Supabase sofort anzubinden. Das kostet ein Projekt, Schlüssel,
Migrationen und Netz, für einen internen Test, bei dem sich das
Datenmodell noch bewegt, zu früh.

## E10 · Aggregate werden gerechnet, nicht gespeichert (Runde 5)

Durchschnittsbewertungen, Videoanzahl, Entfernung, Öffnungsstatus entstehen
bei jeder Abfrage neu. Auf dem Server wird daraus eine Materialized View
(Konzept 6). Vorteil jetzt: keine Werte, die auseinanderlaufen können.

## E11 · Künstliche Verzögerung in der Fassade (Runde 5), ~~gilt nicht mehr~~

130–400 ms, abschaltbar über `VITE_LATENCY=0`. Ohne sie gäbe es keine
Ladezustände zu sehen, und die sollen im Entwurf stimmen.

**Zurückgenommen in Runde 8.** Ein Entwicklerstück in dem, was ausgeliefert
wird, ist genau das, was aus dem MVP heraus sollte. Ladezustände werden jetzt
dort geprüft, wo es sie wirklich gibt: gegen einen Server, mit einer
absichtlich verzögerten Route (`Website-/tools/gegen-server.mjs`).

## E12 · Design-Panel bleibt vorerst (Runde 5)

Es ist kein Produktbestandteil, aber es macht die Abnahme möglich: Ziel,
Rolle, Darstellung, leere und ladende Zustände, Banner, Daten zurücksetzen.
Fällt in **Schritt 2** weg, siehe `04-naechste-schritte/`.

## E13 · Vier Repositories statt einem (Runde 6)

Ausdrücklicher Wunsch. `design` (Bausteine), `Server` (Fachlogik),
`Website-` (Screens), `App` (Android-Hülle), `Brain` (dieses Wissen).

**Verworfen:** ein Monorepo mit Arbeitsbereichen, wie das Konzept
(Abschnitt 4) es eigentlich vorsieht. Es wäre technisch die einfachere
Lösung, die Aufteilung war aber gewünscht.

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
Oberfläche. Vorher stand `t('hours.openUntil')` mitten in der Logik, auf
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
Testserver im WLAN deshalb nicht ansprechen, die App dagegen schon, weil der
Debug-Bau unverschlüsselte Verbindungen ausdrücklich erlaubt
(`android/app/src/debug/AndroidManifest.xml`).

Folge für Schritt 3: Sobald der Server öffentlich unter https steht, fällt die
Einschränkung weg. Bis dahin sind die drei brauchbaren Aufbauten in
`OHNE-RECHNER.md` beschrieben.

## E21 · SQLite statt JSON-Datei (Runde 9)

Gefordert war: Userprofile in eine Datenbank, „wie es professionell Firmen
auch machen", und Daten, die einen Neustart überstehen.

`node:sqlite` steckt seit Node 22.5 in Node selbst. Damit gibt es richtige
Tabellen mit Typen, Bedingungen und Indizes, **ohne** dass `npm install`
etwas nachlädt, was für dieses Projekt seit Runde 6 die Bedingung ist.

Verworfen: PostgreSQL (braucht einen laufenden Dienst, den es auf einem
GitHub-Runner nicht gibt) und Supabase (braucht ein Konto und Netz, beides
Dinge, die dem Ziel „vom Handy aus bedienbar" im Weg stehen).

Gelesen wird aus dem Arbeitsspeicher, geschrieben sofort in die Datenbank.
Das reicht genau so lange, wie **ein** Serverprozess auf die Datei zeigt.

## E22 · Passwörter und Sitzungen gehören nicht der Fachlogik (Runde 9)

Passwörter liegen in einer eigenen Tabelle, gehasht mit scrypt und eigenem
Salz je Konto. Die Fachlogik fragt den Store „stimmt das?" und bekommt ja
oder nein, sie sieht nie ein Passwort, auch kein gehashtes.

Der Grund ist nicht nur Kryptografie: Vorher stand das Passwort im Klartext
neben dem Konto, und an drei Stellen im Code musste daran gedacht werden, es
wieder herauszunehmen (`const { password, ...rest }`). Eine davon zu
vergessen hätte gereicht. **Ein Feld, das es im Objekt nicht gibt, kann nicht
in einer Antwort landen.**

Dass der Store das Verfahren bestimmt, hat einen zweiten Grund: Dieselbe
Fachlogik läuft im Browser, wo der ganze Bestand ohnehin offen im Gerät
liegt. Dort schützt ein Hash niemanden.

## E23 · Keine übernommenen Bewertungen (Runde 9)

Aus den Kartendaten wird alles übernommen, Name, Lage, Adresse, Zeiten,
Küche, Kontakt, Ausstattung. **Bewertungen nicht.**

Ausdrücklich so bestellt, und es ist die einzige haltbare Antwort: Eine
fremde Sternezahl sagt nichts darüber, *was* bewertet wurde, lässt sich nicht
nachvollziehen und wäre eine Behauptung über einen Betrieb, die wir nicht
belegen können. Bewertungen entstehen in dieser Anwendung, mit Video, mit
drei getrennten Achsen, oder gar nicht.

`Server/tools/orte-pruefen.mjs` prüft, dass keine hereinkommt.

## E24 · Kartenkacheln über den eigenen Server (Runde 9)

Vier Gründe, alle praktisch: **ein Ausgang** (die App spricht mit einer
einzigen Adresse, wo der Netzzugang eng ist, muss nur eine Verbindung
erlaubt sein), **ein Zwischenspeicher** (jede Kachel wird einmal geholt),
**Höflichkeit** gegenüber den freien Kachelservern, die von Spenden leben,
und **ein Stilwechsel bleibt eine Zeile**.

Der Stil ist CARTO Voyager. Gewünscht war „so wie Google Maps"; Googles
eigene Kacheln dürfen nur über deren SDK benutzt werden und brauchen ein
Bezahlkonto. Voyager kommt demselben Bild am nächsten und benutzt dieselben
Daten wie alles hier: OpenStreetMap.

## E25 · Titelbilder werden gezeichnet, nicht gesucht (Runde 9)

Zu jeder Betriebsseite gehört ein Bild. Fotos gibt es dafür nicht: Die bei
Google Maps gehören denen, die sie gemacht haben; OpenStreetMap führt bei
unter fünf Prozent eines; ein Stockfoto wäre eine Behauptung über einen
Betrieb, den niemand fotografiert hat.

Also ein ruhiger Verlauf mit dem Anfangsbuchstaben, aus dem Kürzel gerechnet, für denselben Betrieb immer derselbe. Die Seite sieht vollständig aus, ohne
etwas vorzugeben. Sobald ein Betrieb sein Konto übernimmt, lädt er ein echtes
Foto hoch.

## E26 · Beispieldaten raus, Prüfdaten ins Prüfwerkzeug (Runde 9)

Beispieldaten sind bequem und gefährlich: Sie zeigen, wie es aussieht, wenn
die Anwendung läuft, und verdecken, wie es aussieht, wenn sie neu ist.
Schlimmer noch: Niemand hätte von außen erkennen können, was echt ist und was
Kulisse.

Der Ausgangsbestand ist deshalb leer bis auf die echten Betriebe und drei
Zugänge. Was zum Prüfen gebraucht wird, steht in
`Website-/tools/pruefbestand.mjs`, sichtbar als das, was es ist, und nirgends
im Programm.

## E27 · Die Verifizierung darf übersprungen werden (Runde 9)

Im MVP verschickt niemand SMS und E-Mails. Eine Pflicht zur Bestätigung wäre
damit eine Tür ohne Schlüssel: Niemand bekäme je einen Code, und niemand käme
je zu einem Konto.

Der Knopf steht **unter** dem Bestätigen-Knopf, nicht daneben: Bestätigen
bleibt der Normalfall, sobald es einen Absender gibt. Dass übersprungen
wurde, bleibt am Konto stehen (`verificationSkipped`), damit später gezielt
nachgefragt werden kann, statt es zu vergessen.

## E28 · Die Serveradresse steht im Gerät, nicht in der APK (Runde 9)

Die APK wird einmal gebaut, der Server zieht öfter um, über ngrok bei jedem
Start, wenn keine feste Adresse hinterlegt ist. Für jede neue Adresse eine
neue APK zu bauen dauert Minuten, die man mit einem Handy allein nicht hat.

Die Adresse wird geprüft, bevor sie übernommen wird, und danach lädt die Seite
neu: `SERVER` entscheidet beim Laden, ob die Fachlogik im Browser läuft oder
über das Netz. Das mitten im Betrieb umzustellen hieße, jeden laufenden
Zustand mitzunehmen.

`--api` beim Bauen bleibt für den Fall, dass die Adresse feststeht.
