# Stand — die lebende Seite

> Diese Datei wird bei **jeder** Aktion angefasst. Wer wissen will, wo das
> Projekt gerade steht, liest hier und sonst nirgends.
>
> Letzte Änderung: Runde 8 abgeschlossen.

## In einem Satz

Ein MVP, der technisch trägt, am Rechner wie am Handy ein eigenes Gesicht hat
und sich vollständig über GitHub bedienen lässt — ohne dass irgendwo ein
Rechner steht.

## Ampel

| Bereich | Stand | Wo es steht |
|---|---|---|
| Fachlogik, Rechte, Datenhaltung | **grün** | [[02-technik/ARCHITEKTUR]] |
| Aufteilung in fünf Repos | **grün** | [[REPOS]] |
| Webseite veröffentlicht | **grün** | <https://todidervogel.github.io/Website-/> |
| APK-Bau | **grün seit Runde 7** | [[03-verlauf/07-ohne-rechner]] |
| Design Handy | **grün** — Überläufe behoben, in vier Breiten geprüft | Runde 8, Priorität 2 |
| Design Desktop | **grün** — Seitenleiste, zwei Spalten, Feed im Rahmen | Runde 8, Priorität 1 |
| Echte Kartendaten | **gelb** — Import gebaut, Workflow muss laufen | Runde 8 |
| Server von außen erreichbar | **grün** — Workflow steht, Secret fehlt noch | Runde 8, ngrok |
| Präsentationswebseite | **grün** — Startseite ist jetzt eine | Runde 8 |
| Dev-Sachen draußen | **grün** — bis auf `topic`/`admin` | [[04-naechste-schritte/SCHRITT-2-DEV-ENTFERNEN]] |

## Woran ich gerade arbeite

Nichts — Runde 8 ist abgeschlossen. Der nächste Schritt hängt an zwei Dingen,
die nur von Hand gehen: das ngrok-Secret hinterlegen und den Standardzweig in
`design` und `Brain` auf `main` stellen.

## Was ich als Nächstes vorhabe

Runde 8 ist durch. Was als Nächstes ansteht:

1. **Kartenbedienung** — Verschieben und Zoomen mit Maus und Finger
2. **Leerzustände**, die wissen, warum sie leer sind
3. **Eigener Kachelserver** vor einer Veröffentlichung (OSM erlaubt keine
   Massenabrufe)
4. **Echte Anmeldung** statt Klartext-Passwörtern — damit fällt auch
   `topic`/`admin` weg
5. **Videos und Bilder**, die es wirklich gibt

## Was mich beschäftigt

- **Die Webseite hat zwei Betriebsarten, und das wird langsam ein Problem.**
  Allein im Browser ist bequem zum Ansehen, aber es verdeckt, dass ohne Server
  nichts geteilt wird. Sobald ngrok steht, sollte der Alleinbetrieb nur noch
  eine Rückfallebene sein — mit einem ehrlichen Hinweis, nicht als
  gleichwertige Wahl.
- **„Professionell und clean" heißt weniger, nicht mehr.** Die aktuelle
  Oberfläche zeigt zu viel gleichzeitig. Das ist der eigentliche Grund für die
  Überschneidungen, nicht ein fehlendes `overflow: hidden`.
- **Ein Design, das ich nie gesehen habe, kann ich nicht beurteilen.** Ab
  Runde 8 mache ich Bildschirmfotos und sehe sie mir an, statt aus dem CSS zu
  schließen, dass es passt. Das hat sich sofort ausgezahlt: die fehlenden
  Zahlen am Profil, die Sackgasse im Feed und der überlaufende Knopf standen
  in keinem Test — im Bild sah man alle drei auf einen Blick.
- **Tests prüfen, ob etwas da ist. Nicht, ob man wieder wegkommt.** Der Feed
  hatte am Rechner keine Navigation. Jeder Verhaltenstest war grün.

## Verknüpfungen

- Auftrag und Denkstand dieser Runde → [[03-verlauf/08-desktop-und-echte-daten]]
- Warum etwas so ist → [[00-produkt/ENTSCHEIDUNGEN]]
- Was noch fehlt → [[00-produkt/OFFENE-PUNKTE]]
- Selbst ausprobieren → [[SELBST-TESTEN]] · ohne Rechner [[OHNE-RECHNER]]
