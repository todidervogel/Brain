# Runde 11: Fehler, Fläche, eigener Kartenstil

## Der Auftrag

1. Die Anmeldung in der App geht nicht.
2. Am Handy braucht es zweimal Zurück, um aus der App zu kommen.
3. Die Server-Fehlermeldung soll ein Satz sein, und sie ist gerade gar nicht da.
4. OpenStreetMap auf den Server, damit wir sie bearbeiten können. Standardstil,
   nicht der deutsche.
5. Google-Maps-Verknüpfung und ein Drei-Punkte-Menü mit Speichern, Teilen,
   Melden.
6. Vor jeder Runde ein Sicherungszweig.
7. Gastros aus ganz Deutschland.

## Die Anmeldung: ein Schlüssel im Formular

Reproduziert, nicht geraten. Gegen einen laufenden Server ging alles. Gegen
einen **toten** Server stand im Anmeldeformular:

    auth.errors.Keine Verbindung

Der Weg dorthin: `request()` wirft bei einem Netzfehler `new Error('Keine
Verbindung')`. Die Anmeldung reichte `error.message` an `errorText()` weiter,
das daraus `auth.errors.Keine Verbindung` baute. Diesen Schlüssel gibt es
nicht, und `t()` gibt bei einem fehlenden Schlüssel den Schlüssel zurück.

Wer das las, konnte sich nicht anmelden und erfuhr nicht, warum.

Zwei Dinge behoben:

- **Netzfehler tragen jetzt kein Schlüsselwort mehr.** `api.js` setzt
  `error.offline`, und die Anmeldung fragt danach, statt auf Text zu prüfen.
- **`errorText` erkennt unbekannte Schlüssel.** Kommt ein Schlüssel zurück,
  den es nicht gibt, steht dort ein Satz und nicht der Schlüssel. Ein
  Schlüssel im Formular ist immer ein Fehler in der Anzeige.

Nebenbei fiel auf, dass `changePassword` den Fehler gar nicht fing: Der Aufruf
flog ungefangen weiter und der Bildschirm blieb hängen.

### Und warum die Meldung fehlte

Die APK auf dem Gerät stammt von vor Runde 10. Das Band gibt es erst seit
Runde 10. Ein neuer APK-Bau, und es ist da.

## Die Zurück-Taste: gemessen statt vermutet

Erste Vermutung: ein doppelter Eintrag beim Start durch die Weiterleitungen.
Nachgemessen: `history.length` ist beim Start gleich, egal ob über `/` oder
direkt. Die Vermutung war falsch.

Dann das Naheliegende gemessen:

    Start 2 -> nach vier Leistenwechseln 6 (also 4 Einträge dazu)

**Jeder Tipp auf die untere Leiste legte einen Verlaufseintrag an.** Wer
zwischen Feed und Karte hin und her tippte, musste ebenso oft Zurück drücken.

Zwei Dinge zusammen beheben es:

1. Die Leistenpunkte wechseln mit `replace`. Ein Bereich ist ein Ort, kein
   Schritt vorwärts. Gemessen: null zusätzliche Einträge. Aufnehmen ist
   ausgenommen, das ist ein Ablauf über mehrere Schritte.
2. `MainActivity.onBackPressed` schließt die App, wenn der WebView auf einem
   Hauptbereich steht, egal was im Verlauf noch liegt. Ohne neue Abhängigkeit:
   Das Android-Projekt gehört uns.

## Das Drei-Punkte-Menü

Der Route-Knopf zeigte bisher einen Hinweis „hier passiert nichts". Er sah aus
wie ein Knopf und war keiner. Jetzt führt er zu Google Maps, mit Koordinaten,
über die offiziellen Verweise, die auf dem Handy die installierte App öffnen.

Dazu ein Menü mit Route, In Google Maps ansehen, Speichern, Teilen, Melden.
Teilen benutzt die Teilen-Funktion des Geräts, wenn es sie gibt, sonst die
Zwischenablage. Vorher meldete der Knopf „Link kopiert", ohne etwas zu
kopieren.

**Beim Ansehen des Bildes fiel der eigentliche Fehler auf:** Das Menü ging
nach unten auf und stand damit außerhalb des Bildschirms, weil die
Aktionsleiste knapp über der unteren Navigationsleiste sitzt. Der Knopf
reagierte, und man sah nichts. `Menu` misst jetzt beim Öffnen den Platz und
klappt nach oben, wenn unten keiner ist.

Wieder ein Fund, den kein Test gemacht hat und ein Bildschirmfoto sofort.

## Ganz Deutschland, ohne die App zu erschlagen

91 Gegenden, die größten Städte und die Urlaubsregionen. Bei 150 Betrieben je
Gegend sind das gut zwölftausend.

Das Problem daran ist nicht der Server, sondern die Weboberfläche: Sie nimmt
`orte.js` als Modul mit, damit der Alleinbetrieb im Browser funktioniert.
Zwölftausend Betriebe wären dort mehrere Megabyte, die jedes Handy bei jedem
Start herunterlädt und auspackt, um dann die zehn in der Nähe anzuzeigen.

Deshalb getrennt:

| Gruppe | Datei | Wer liest sie |
|---|---|---|
| `kern` | `src/data/orte.js` (Modul) | Server **und** Weboberfläche |
| `deutschland` | `src/data/deutschland.json` | nur der Server |

Das ist die richtige Aufteilung: Die Fläche ist genau das, wofür es einen
Server gibt. Ohne Server sieht man die drei Kern-Gegenden, mit Server das
ganze Land.

Dazu drei Kleinigkeiten, die dazugehören:

- **Leere Felder werden nicht geschrieben.** Gut die Hälfte der Betriebe hat
  keine Adresse, kein Telefon, keine Zeiten. `orte-laden.js` ergänzt sie beim
  Laden. Sonst wären es Megabyte für Felder, die niemand liest.
- **`/api/places` hat eine Obergrenze** von 500. Ohne Umkreis lieferte die
  Adresse sonst jeden Betrieb, den der Server kennt.
- **Doppelte fliegen raus.** Die Gegenden überlappen sich, Oberkirch liegt im
  Umkreis von Karlsruhe. Es zählt die Kennung aus OpenStreetMap, und die
  Kern-Gegend gewinnt, weil sie angereichert ist.

### Ein Fehler im Workflow, der noch nie zugeschlagen hatte

Der Eincheckschritt zählte die Betriebe so:

    JSON.parse(readFileSync('src/data/orte.js'))

`orte.js` ist seit Runde 8 ein Modul und kein JSON. Der Schritt wäre
abgebrochen, sobald sich wirklich etwas geändert hätte. Aufgefallen ist es
nur, weil ich den Workflow für die zweite Gruppe umgebaut habe.

## Der Kartenstil gehört jetzt uns

Gewünscht: OpenStreetMap auf den Server, damit wir sie bearbeiten können.
Standardstil, nicht der deutsche.

Der Standardstil war schon eingestellt. Neu ist, dass wir ihn **verändern**
können. Der Server kann PNG jetzt nicht nur schreiben, sondern auch lesen
(`kachelbild.js`), und `kartenstil.js` färbt jede Kachel um, bevor sie
ausgeliefert wird:

    KARTE_STIL=ruhig  node src/index.js    heller, entsättigt
    KARTE_STIL=dunkel node src/index.js    für den Dunkelmodus

Ein Stil sind sechs Zahlen in einer Datei. Voreingestellt ist `roh`, also
unverändert.

Was damit **nicht** geht: Straßen, Beschriftungen, Symbole. Dafür bräuchte es
die Rohdaten statt der Bilder. Der Weg dorthin steht in `Server/docs/KARTE.md`,
mit beiden Möglichkeiten (eigene Rasterkacheln über osm2pgsql und renderd,
oder Vektorkacheln über Planetiler und MapLibre) und einer Einschätzung, was
sie kosten. Solange der Server in einem Workflow läuft, der nach fünfeinhalb
Stunden endet, wäre beides ein Vorhaben ohne Ort.

## Was die Prüfungen gefunden haben

- `tools/vollstaendig.mjs` meldete `src/http/zaehler.js` als nicht eingecheckt,
  in derselben Minute, in der die Datei entstand.
- Der i18n-Prüfer wurde rot, weil ich in einem **Kommentar** erklärt hatte,
  welcher Schlüssel früher falsch war. Er überliest Kommentare jetzt.
- Die Kachelprüfung wurde rot, nachdem der Zwischenspeicher je Stil einen
  eigenen Ordner bekam. Genau dafür ist sie da.
- Der Lauf gegen den echten Server brach ab. `tools/gegen-server.mjs` suchte
  die Speisekarte von `trattoria-bella`, einem erfundenen Betrieb aus der Zeit
  vor dem MVP-Bestand. Seit Runde 9 gibt es ihn nicht mehr, und am Gastro-Konto
  hängt ein echter Betrieb aus OpenStreetMap, dessen Name sich mit jedem Import
  ändern kann. Das Skript liest die Adresse jetzt dort ab, wo die Seite sie
  selbst hinschreibt, und legt die Kategorie an, die ein frisch importierter
  Betrieb noch nicht hat.

  Das ist die eigentliche Lehre dieser Runde: Der Alleinbetrieb war die ganze
  Zeit grün. Nur der Lauf gegen den Server merkt, wenn eine Prüfung von Daten
  ausgeht, die es nicht mehr gibt. Er steht jetzt in der Prüfliste in
  `CLAUDE.md`, in allen fünf Repositories.

→ zurück zu [[STAND]] · vorherige Runde [[03-verlauf/10-sichtbar-und-selbsttaetig]]
