# Der Weg zur vollen App

Stand: Runde 13. Diese Datei beantwortet drei Fragen: Woraus besteht die App,
was kann sie heute wirklich, und was fehlt, damit „voll funktionsfähig"
stimmt. Sie ersetzt kein Rundenlog, sie ist die Landkarte darüber.

## 1. Die Bausteine

| Baustein | Was darin liegt |
|---|---|
| `design` | 11 Bausteine (Knopf, Feld, Menü, Bewertung, Videokachel …), Farben, Schrift, alle Texte an einer Stelle |
| `Website-` | 49 Bildschirme, Karte, Formulare, Sitzung. Läuft als Webseite **und** als Oberfläche der App |
| `Server` | 18 Fachlogikmodule, 68 Aufrufe mit Rechteprüfung, SQLite mit 15 Tabellen, HTTP, Kartenkacheln |
| `App` | Android-Hülle (Capacitor), APK-Bau, Berechtigungen |
| `Brain` | Entscheidungen, Rundenlog, Architektur, diese Datei |

Der tragende Gedanke: Die Fachlogik unter `domain/` läuft **zweimal**, auf dem
Server und im Browser. Deshalb funktioniert die App auch ohne Server, und
deshalb stehen die Rechte an einer Stelle statt an fünfzig.

## 2. Was heute wirklich funktioniert

- 359 Betriebe aus OpenStreetMap, mit Adresse, Öffnungszeiten, Angebot,
  Herkunftsnachweis. Keine erfundenen Seiten.
- Karte weltweit, schieben und zoomen, geladen wird der sichtbare Ausschnitt.
- Standort vom Gerät, mit Berechtigungsabfrage und sauberem Rückfall.
- Konten mit gehashten Passwörtern, Sitzungen überleben den Neustart.
- Speisekarten anlegen, öffentlich zeigen, QR-Code drucken.
- Bewertungen auf drei Achsen, sortierbar, mit Antwort des Betriebs.
- Meldungen und Moderation, Admin-Bereich, Protokoll.
- Dunkelmodus, Desktop-Layout, Fehlermeldungen mit Fristen.

## 3. Was fehlt

### Sofort spürbar

1. **Videos sind eine Attrappe.** Die Aufnahme ist eine Stoppuhr ohne Kamera,
   im Feed liegt eine leere Fläche. Das ist der Kern des Produkts.
   Nötig: Datei über Kamera oder Galerie, Ablage (Gerät im Alleinbetrieb,
   Server im Serverbetrieb), Auslieferung mit Bereichsabfragen, Abspielen im
   Feed, Vorschaubild, Ansehen in der Moderation.
2. **Kommentare.** Ausgeblendet, weil es sie nicht gibt.
3. **Blockieren.** Gehört zu den Videos: Liste je Konto, Filter im Feed,
   Rücknahme in den Einstellungen. Ohne das ist keine App mit fremden Inhalten
   veröffentlichungsreif.
4. **Bilder zu Bewertungen.** Wer ein Gericht bewertet, will es zeigen.

### Betrieb

5. **Ein Server mit fester Adresse.** Heute läuft er in einem Workflow hinter
   einem ngrok-Tunnel, höchstens fünfeinhalb Stunden am Stück, mit einer
   Datenbank, die als Artefakt von Lauf zu Lauf gereicht wird. Das ist ein
   kluger Behelf und kein Betrieb.
6. **E-Mail.** Registrierung bestätigen, Passwort zurücksetzen, Benachrichtigen.
   Solange es das nicht gibt, bleibt der Überspringen-Knopf, und `topic/admin`
   bleibt ein Loch.
7. **Sicherung, die nicht am Zufall hängt.** Heute hängt alles an einem
   Artefakt mit Ablaufdatum.

### Vor einer Veröffentlichung

8. Impressum, Datenschutzerklärung, AGB mit echtem Inhalt statt Platzhaltern.
9. Signiertes Paket, Play-Store-Eintrag, Alterskennzeichnung.
10. Meldeweg und Löschfrist für gemeldete Inhalte, nachvollziehbar.

### Qualität

11. **Marker zusammenfassen.** Bei 200 Markern auf kleinem Zoom klumpen sie.
    Google Maps fasst sie zu Zahlen zusammen.
12. **„Hier suchen".** Wer die Karte verschiebt, will einen Knopf, der die
    Suche auf den neuen Ausschnitt anwendet, statt sofort neu zu laden.
13. **Offline.** Was man einmal gesehen hat, sollte man ohne Netz wiedersehen.
14. **Eine zweite Sprache.** Alle Texte liegen an einer Stelle, das ist die
    halbe Miete, aber es gibt nur Deutsch.

## 4. Der Vergleich

| Gegenüber | Was die haben, wir nicht | Was wir anders machen |
|---|---|---|
| TikTok, Instagram | echte Videos, Kommentare, Blockieren, Vorschläge, Offline-Puffer | Videos hängen an einem Ort, nicht an einem Kanal |
| Google Maps | Marker-Cluster, „hier suchen", Navigation, Gästefotos | keine Navigation, dafür ein Verweis dorthin |
| Yelp, Tripadvisor | Fotos an Bewertungen, große Betriebsdatenbank | drei Achsen statt einer Sternzahl, keine übernommenen Bewertungen |
| Lieferando | Bestellung, Bezahlung | bewusst außerhalb des MVP |

Was wir besser können, wenn es fertig ist: Das Angebot steht auf einen Blick
da (Fleisch, Fisch, vegan, süß, Getränke), die Bewertung trennt Essen, Service
und Preis, und es gibt keine übernommenen Fremdbewertungen, die niemand
zuordnen kann.

## 5. Reihenfolge

**Phase A, abgeschlossen in Runde 13.** Alles, was im MVP zu sehen ist, tut
auch etwas.

**Phase B, die Videos.** Aufnahme über die Dateiauswahl, Ablage, Auslieferung,
Abspielen, Vorschaubild, Moderation mit Ansicht. Danach Kommentare und
Blockieren, weil beides erst mit echten Inhalten Sinn ergibt.

**Phase C, der Betrieb.** Fester Server, E-Mail, Sicherung. Ab hier kann man
die App jemandem geben, den man nicht kennt.

**Phase D, der Feinschliff.** Cluster, „hier suchen", Offline, zweite Sprache.

**Phase E, die Veröffentlichung.** Recht, Signatur, Store.

→ zurück zu [[STAND]]
