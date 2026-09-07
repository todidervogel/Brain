# Stand — die lebende Seite

> Diese Datei wird bei **jeder** Aktion angefasst. Wer wissen will, wo das
> Projekt gerade steht, liest hier und sonst nirgends.
>
> Letzte Änderung: Runde 8, Auftrag aufgenommen und geplant.

## In einem Satz

Ein MVP, der technisch trägt, aber gestalterisch noch nicht vorzeigbar ist —
und der bisher nur vom Rechner aus bedienbar war, obwohl es keinen Rechner
gibt.

## Ampel

| Bereich | Stand | Wo es steht |
|---|---|---|
| Fachlogik, Rechte, Datenhaltung | **grün** | [[02-technik/ARCHITEKTUR]] |
| Aufteilung in fünf Repos | **grün** | [[REPOS]] |
| Webseite veröffentlicht | **grün** | <https://todidervogel.github.io/Website-/> |
| APK-Bau | **grün seit Runde 7** | [[03-verlauf/07-ohne-rechner]] |
| Design Handy | **gelb** — zwei Überläufe behoben, Durchgang läuft | Runde 8, Priorität 2 |
| Design Desktop | **grün** — Seitenleiste, zwei Spalten, Feed im Rahmen | Runde 8, Priorität 1 |
| Echte Kartendaten | **gelb** — Import gebaut, Workflow muss laufen | Runde 8 |
| Server von außen erreichbar | **grün** — Workflow steht, Secret fehlt noch | Runde 8, ngrok |
| Präsentationswebseite | **rot** — gibt es nicht | Runde 8 |
| Dev-Sachen draußen | **rot** | [[04-naechste-schritte/SCHRITT-2-DEV-ENTFERNEN]] |

## Woran ich gerade arbeite

Runde 8, Priorität 2: der Durchgang durch alle Screens in vier Breiten mit
`Website-/tools/bilder.mjs`. Priorität 1 (Desktop) steht.

## Was ich als Nächstes vorhabe

1. ~~Desktop-Layout für die Web-App~~ ✓
2. Überläufe und Überschneidungen beheben, per Bildschirmfoto selbst geprüft
3. Dev-Sachen aus `App`, `Website-`, `Server` raus (nur `design` behält sie)
4. Karte mit echten Kacheln, echte Betriebe aus drei Gegenden
5. Server über ngrok vom Handy startbar
6. Präsentationswebseite davor

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
