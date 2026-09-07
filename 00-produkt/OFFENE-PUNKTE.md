# Offene Punkte

## Entscheidungen, die anstehen

| Punkt | Warum es drängt | Vorschlag |
|---|---|---|
| **Produkt- und Firmenname** | Ohne Namen keine Domain, kein Impressum, keine Gastro-Ansprache. Arbeitstitel ist „Tellerrand". | Vor der ersten Gastro-Ansprache festlegen |
| **Rechtstexte** | AGB, Datenschutzerklärung, Impressum sind Platzhalter. Bei Nutzervideos, Standortdaten und Bewertungen ist das Haftungsrisiko real. | Vor MVP 1 einmal von einer Anwältin oder einem Anwalt für IT-Recht prüfen lassen. Konzept 10 nennt das die eine Ausgabe, an der man nicht sparen sollte. |
| **Gastro-Ansprache** | Kaltakquise per E-Mail an Unternehmen ist in Deutschland nach § 7 UWG grundsätzlich unzulässig. Der Admin-Bereich warnt an der Stelle bereits. | Postkarte, Telefon oder Ansprache über die Gäste per QR-Code. Vor dem ersten Versand klären. |
| **Video-Hosting** | Supabase Free hat 1 GB Speicher. Bei Video ist das schnell weg. | Ab MVP 1 Cloudflare Stream einplanen (Konzept 11) |
| **Gastro-Verifizierung** | Der Ablauf ist im Prototyp nur ein Knopf. | Nach MVP 1 ausarbeiten: Gewerbeanmeldung hochladen + Telefonanruf, Postbrief nur bei Zweifeln |

## Technische Lücken im Prototyp

Bewusst so — es fehlt der Server, nicht die Idee.

- **Kein echtes Video.** Aufnahme, Zuschnitt und Wiedergabe sind angedeutet.
  Braucht Capacitor-Plugins und Cloudflare Stream.
- **Keine echte Karte.** Marker stehen an den richtigen Stellen (aus echten
  Koordinaten gerechnet), aber ohne Kartenkacheln darunter. MapLibre kommt
  mit dem Server.
- **Kein GPS.** Position steht auf Prenzlauer Berg, verschiebbar über die
  Ortssuche.
- **Keine E-Mails, keine SMS.** Bestätigungscode ist immer `123456`.
- **Keine Bilder.** Titelbilder, Gerichtsfotos und Avatare sind Platzhalter.
- **Keine echten OSM-Daten.** Zehn erfundene Berliner Betriebe. Der
  Overpass-Import ist Schritt 4 der Umsetzungsreihenfolge im Konzept.
- **Passwörter im Klartext** im Browser. Für einen Prototyp mit erfundenen
  Konten in Ordnung, für alles andere nicht.

## Einstellungen, die nur von Hand gehen

- **Standardzweig auf `main`** in `design` und `Brain` (Settings → General →
  Default branch). Solange dort der alte Entwurfszweig steht, weist GitHub
  jede Veröffentlichung von `main` aus ab — die Umgebung `github-pages`
  lässt von Haus aus nur den Standardzweig hinein. `Website-`, `App` und
  `Server` stehen richtig.
- **Kein öffentlicher Server.** Die veröffentlichte Webseite läuft deshalb im
  Alleinbetrieb. Und solange der Server nur unter http erreichbar wäre, kann
  eine https-Seite ihn ohnehin nicht ansprechen (E20).

## Aus früheren Runden noch offen

- **Icons und Animationen verfeinern** (Runde 3). Es wurde nach einer
  konkreteren Richtung gefragt, statt ins Blaue zu ändern. Steht weiter offen.
- **Kommentare** sind überall angelegt, aber ausgegraut (`MVP_STAGE < 2`).
- **Bestellung und Bezahlung** erst MVP 2, Architektur ist offengehalten.
