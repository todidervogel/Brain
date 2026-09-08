# Stand

> Diese Datei wird bei **jeder** Aktion angefasst. Wer wissen will, wo das
> Projekt gerade steht, liest hier und sonst nirgends.
>
> Letzte Änderung: Runde 9 abgeschlossen.

## In einem Satz

Ein MVP mit richtiger Datenbank, echten Betrieben und einer Karte vom eigenen
Server, der einen Neustart übersteht und sich vollständig über GitHub
bedienen lässt, ohne dass irgendwo ein Rechner steht.

## Ampel

| Bereich | Stand | Wo es steht |
|---|---|---|
| Fachlogik und Rechte | **grün** | [[02-technik/ARCHITEKTUR]] |
| Datenhaltung | **grün**, SQLite, 15 Tabellen, übersteht den Neustart | Runde 9 |
| Passwörter | **grün**, scrypt, eigenes Salz, eigene Tabelle | Runde 9 |
| Anmeldungen | **grün**, in der Datenbank, kein Rauswurf beim Neustart | Runde 9 |
| Aufteilung in fünf Repos | **grün** | [[REPOS]] |
| Webseite veröffentlicht | **grün** | <https://todidervogel.github.io/Website-/> |
| APK-Bau | **grün seit Runde 7** | [[03-verlauf/07-ohne-rechner]] |
| Serveradresse im Gerät einstellbar | **grün**, Einstellungen, Verbindung | Runde 9 |
| Adresse muss niemand abtippen | **grün**, der Server veröffentlicht sie, APK und App holen sie | Runde 10 |
| Man sieht dem Server beim Laufen zu | **grün**, Statuszeile alle 30 Sekunden im Protokoll | Runde 10 |
| App sagt, wenn kein Server da ist | **grün**, Band mit einem Knopf, der die Adresse holt | Runde 10 |
| Anmeldung bei totem Server | **grün**, ein Satz statt eines Schlüssels | Runde 11 |
| Zurück-Taste am Handy | **grün**, einmal drücken genügt | Runde 11 |
| Route und Teilen | **grün**, Drei-Punkte-Menü, Google Maps | Runde 11 |
| Betriebe in Deutschland | **grün**, 91 Gegenden, nur auf dem Server | Runde 11 |
| Kartenstil bearbeitbar | **grün**, Farbe ja, Zeichnung nein | Runde 11, [[../Server/docs/KARTE]] |
| Lauf gegen den echten Server | **grün**, 9 von 9, prüft die Anmeldung wirklich am Server | Runde 11 |
| Kein Aufruf ohne Frist | **grün**, 15 s normal, 8 s beim Nachfragen, 60 s beim Hochladen | Runde 12 |
| Meldung bei schweigendem Server | **grün**, Band und Formular sagen es beide | Runde 12 |
| App durch den ngrok-Tunnel | **grün**, Warnseite übersprungen, Kopfzeile freigegeben | Runde 12 |
| Karte bedienbar | **grün**, schieben, zoomen, lädt den Ausschnitt | Runde 13 |
| Kacheln in der App | **grün**, über fetch mit Kopfzeile, mit Zwischenspeicher | Runde 13 |
| Eigener Standort | **grün**, Berechtigung im Manifest und Plugin dabei | Runde 13 |
| Keine Attrappen im MVP | **grün**, Teilen, QR, Sortieren, Hilfe tun etwas | Runde 13 |
| Herkunft jedes Betriebs | **grün**, osmId wird geprüft | Runde 13 |
| Videos | **rot**, Aufnahme ohne Kamera, Feed ohne Bild | [[04-naechste-schritte/PLAN-VOLLE-APP]] |
| Design Handy | **grün**, in vier Breiten geprüft | Runde 8 |
| Design Desktop | **grün**, Seitenleiste, zwei Spalten, Feed im Rahmen | Runde 8 |
| Echte Betriebe | **grün**, 360 aus drei Gegenden, ohne fremde Bewertungen | Runde 8/9 |
| Karte | **grün**, weltweit, über den eigenen Server, OSM-Standardstil | Runde 9 |
| Titelbilder | **grün**, echte wo vorhanden, sonst gezeichnet | Runde 9 |
| Beispieldaten | **grün**, raus; Prüfdaten liegen im Prüfwerkzeug | Runde 9 |
| Verifizierung überspringbar | **grün** | Runde 9 |
| Server von außen erreichbar | **gelb**, Workflow steht, **Secret fehlt noch** | [[OHNE-RECHNER]] |
| Dev-Sachen draußen | **gelb**, bis auf `topic`/`admin` | [[04-naechste-schritte/SCHRITT-2-DEV-ENTFERNEN]] |

## Woran ich gerade arbeite

Nichts, Runde 13 ist abgeschlossen. Was als Nächstes ansteht, steht in
[[04-naechste-schritte/PLAN-VOLLE-APP]].

## Arbeitsregeln

Seit Runde 10 steht in jedem Repo eine `CLAUDE.md`. Sie gilt für jede Sitzung,
auch für spätere:

1. **Abhängigkeiten prüfen.** Wer hängt an dem, was ich geändert habe? Mit
   einer Tabelle, welche Änderung welches Repo mitzieht.
2. **Auf Fehler prüfen.** Nicht „sieht richtig aus", sondern laufen lassen.
   Mit den Befehlen, die dazugehören.
3. **Kommentare mitziehen.** Der Kasten oben in jeder Datei sagt, wer sie
   benutzt. Ändert sich das, ändert er sich mit.
4. **Die .md-Dateien mitziehen**, allen voran diese hier.

## Was nur von Hand geht

Drei Dinge, die ich nicht selbst erledigen kann:

1. **Den ngrok-Token neu erzeugen** (der alte stand im Chat) und als Secret
   `NGROK_AUTHTOKEN` im Repo `Server` hinterlegen. Danach läuft
   *Actions → „Server über ngrok"*.
2. **Den Standardzweig** in `design` und `Brain` auf `main` stellen.
3. **Das Passwort von `topic` ändern**, bevor der Server öffentlich läuft.
   Der Server erinnert bei jedem Start daran.

## Was mich diese Runde gelehrt hat

- **Ein Knopf mit Erfolgsmeldung, der nichts tut, ist schlimmer als ein toter
  Knopf.** Fünfmal „Link kopiert" ohne Kopie.
- **Ein Symbol, das aussieht wie eine Sache, ist nicht die Sache.** Der
  QR-Code war ein Icon.
- **Zuhörer für Gesten gehören auf eine eigene Ebene**, sonst fangen sie die
  Knöpfe ab, die darauf liegen.

Aus Runde 12, weiter gültig:

- **Alles, was auf ein Netz wartet, braucht eine eigene Uhr.** Das
  Verbindungsband sollte sagen „der Server antwortet nicht" und hing dabei
  selbst an einer Antwort des Servers. Ohne Frist wartet `fetch` unbegrenzt.
- **„Falscher Text" und „gar kein Text" sind zwei verschiedene Fehler.** In
  Runde 11 habe ich den ersten behoben und für den ganzen gehalten. Die
  Rückmeldung hatte beides sauber getrennt, ich hatte es zusammengezogen.
- **Ein Formular darf nie stumm bleiben.** Ein `try` ohne `catch` um das
  Absenden ist eine Zusage, dass nichts wirft. Solche Zusagen halten nicht.

Aus Runde 11, weiter gültig:

- **Reproduzieren, nicht raten.** Bei der Zurück-Taste war meine erste
  Vermutung falsch. Die Messung hat die richtige Ursache in einer Minute
  gezeigt.
- **Ein Schlüssel im Formular ist immer ein Anzeigefehler.** Jetzt fängt
  `errorText` das ab, statt sich auf vollständige Übersetzungen zu verlassen.
- **Ein Menü, das man nicht sieht, sieht aus wie ein kaputter Knopf.**

## Was ich als Nächstes vorhabe

Ausführlich in [[04-naechste-schritte/PLAN-VOLLE-APP]]. In Kürze:

1. **Videos wirklich machen.** Aufnahme über die Dateiauswahl, ablegen,
   ausliefern, abspielen, in der Moderation ansehen. Der Kern des Produkts.
2. **Kommentare und Blockieren**, direkt danach, weil beides erst mit echten
   Inhalten Sinn ergibt.
3. **Ein Server mit fester Adresse**, statt eines Tunnels mit Ablaufdatum.
4. **E-Mail**, damit Registrierung und Passwort-vergessen echt werden.
5. **Marker zusammenfassen und „hier suchen"**, die zwei Griffe, die auf einer
   Karte mit vielen Punkten fehlen.

## Was mich beschäftigt

- **Ein Ausgangsbestand ist Code.** Die bestellten Konten hießen `test-user`
  und `test-gastro`, mit Bindestrich, den die eigene Regel für
  Benutzernamen nicht zulässt. Anmelden ging; beim ersten Speichern des
  Profils hätte das Formular den eigenen Namen zurückgewiesen. Prüfungen
  laufen jetzt gegen die echten Konten, und der Rauchtest prüft den Bestand
  gegen die eigenen Regeln.
- **Eine Regel, die nur der Browser kennt, ist keine Regel.** Dieselbe Sache
  von der anderen Seite: Wer den Aufruf direkt schickt, kam an der
  Benutzernamensregel vorbei. Sie steht jetzt in der Fachlogik.
- **Beispieldaten sind bequem und gefährlich.** Sie zeigen, wie es aussieht,
  wenn die Anwendung läuft, und verdecken, wie es aussieht, wenn sie neu ist.
  Getrennt: leer im Programm, voll im Prüfwerkzeug.
- **Bildschirmfotos finden, was Tests nicht finden.** Diese Runde: der
  Platzhalter „Stand {date}", der wörtlich auf jeder angereicherten
  Betriebsseite stand. Kein Test prüft Platzhalter.
- **`Number(null)` ist `0`.** Zum zweiten Mal dieselbe Falle, diesmal bei der
  Kartenabfrage. Zweimal heißt: keine Unachtsamkeit, sondern etwas, wogegen
  man einmal eine Hilfsfunktion schreibt.
- **„Website vernachlässigen" ging nicht wörtlich.** Die App *ist* die
  gebaute Website. Was in der App zu sehen ist, habe ich angefasst; die
  Präsentationsseite und der Desktop-Feinschliff ruhen.
- **Ein Symbol an der falschen Stelle macht alles kaputt.** Die
  Kartenmarker und die Betriebszeilen trugen beide das Symbol für „Bild
  fehlt". Beides sah nach Fehler aus, obwohl nichts kaputt war. Und die
  Navigationsleiste stand in Großbuchstaben, weil eine Klasse für
  Tabellenköpfe benutzt wurde. Drei Kleinigkeiten, ein Eindruck: unfertig.
- **Bilder von Google gehen nicht, und das ist kein Aufwandsproblem.** Die
  Fotos gehören den Menschen, die sie gemacht haben, nicht Google und
  nicht uns. Was geht: Wikimedia Commons, gezeichnete Titelbilder, und
  irgendwann die Betriebe selbst.
- **Zwei Bausteine dürfen nicht denselben Klassennamen tragen.**
  `.menu-item` war im Aufklappmenü ein Eintrag und auf der Speisekarte ein
  Gericht. Das Gericht hat eine Trennlinie, seine Regel stand weiter unten,
  also gewann sie. Keine Änderung an der Menü-Regel half, und der Grund war
  nirgends zu sehen.
- **Ein `.gitignore`-Muster ohne Schrägstrich vorn trifft jeden Ordner.**
  `data/` hat auch `src/data/` erwischt. Der Server startete lokal und auf
  dem Runner nicht. Gefragt werden muss git, nicht das Dateisystem, und das
  tut jetzt `tools/vollstaendig.mjs`.
- **Ein Skript, das 220 Dateien anfasst, braucht danach eine Prüfung.**
  Beim Entfernen der Gedankenstriche hat eine zu weite Regel `(?, ?, ?)` in
  SQL zu `(? ? ?)` gemacht und Kommentarzeilen zusammengezogen. Beides fiel
  sofort auf, weil hinterher die Tests liefen.

## Verknüpfungen

- Auftrag und Denkstand dieser Runde → [[03-verlauf/09-datenbank-und-karte]]
- Runde davor → [[03-verlauf/08-desktop-und-echte-daten]]
- Warum etwas so ist → [[00-produkt/ENTSCHEIDUNGEN]]
- Was noch fehlt → [[00-produkt/OFFENE-PUNKTE]]
- Selbst ausprobieren → [[SELBST-TESTEN]] · ohne Rechner [[OHNE-RECHNER]]
- Zugänge → [[02-technik/TESTKONTEN]]
