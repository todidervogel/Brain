# Runde 13: Eine Karte, die man benutzen kann, und keine Attrappen mehr

## Der Auftrag

> Was benötigt die App für Berechtigungen. Die Map wird nicht vom Server
> geladen, soll wie bei Maps sein, also nur im Umkreis geladen, die App
> benötigt die Berechtigung. Keine Fake Gastro Seiten. Zu Gastrobetrieben
> gehören Cafés, wo man was vor Ort kaufen kann und es eventuell auch
> mitnehmen kann. Bitte überdenke ALLE Funktionen die im MVP dargestellt sind
> und mache dass sie fertig sind.

Der letzte Satz war der große. Er hat die Runde von „drei Wünsche" zu einer
Durchsicht gemacht, und dabei kam heraus, dass an sechs Stellen etwas stand,
das aussah wie eine Funktion und keine war.

## Die Karte

**Die Kacheln kamen nicht an.** Zwischen App und Server liegt ein
ngrok-Tunnel, und der schiebt Browsern eine Warnseite unter, statt das Bild
durchzulassen. Sie kommt mit Status 200 und HTML. Ein `<img>` kann keine
Kopfzeile mitschicken, also bekam es die Warnseite, konnte sie nicht anzeigen
und blendete sich aus. Auf dem Handy blieb der graue Rasterhintergrund.

Jetzt werden Kacheln über `fetch` geholt, mit der Kopfzeile, die die Warnseite
überspringt, und als Objekt-Adresse an das `<img>` gegeben. Nebenbei entstand
damit ein Zwischenspeicher für 400 Kacheln im Gerät: Beim Schieben kommen
dieselben immer wieder vor.

**Sie ließ sich nicht bedienen.** Jetzt schon: ein Finger schiebt, zwei Finger
zoomen, das Rad zoomt, ein Doppeltipp geht eine Stufe näher, und es gibt
Knöpfe für alle, die lieber tippen. Der Punkt unter dem Finger bleibt dabei
stehen, das ist der ganze Trick daran (`zentrumHalten` in `lib/map.js`).

**Geladen wird, was im Bild liegt.** Nicht mehr der feste Umkreis. Der
Ausschnitt geht als Rechteck in die Abfrage, und `places.list` filtert auf dem
Server grob vor, bevor es Entfernungen und Öffnungszeiten rechnet. Bei
zwölftausend Betrieben ist das der Unterschied zwischen einer trägen und einer
schnellen Karte.

### Zwei Fehler, die nur die Prüfung fand

Die Zoomknöpfe taten nichts, obwohl sie verdrahtet waren. Grund: Die
Gestenzuhörer hingen am ganzen Kartenschirm, und `setPointerCapture` zieht
alle weiteren Berichte zum greifenden Element. Auf dem Knopf kam nie ein Klick
an. Jetzt hängen sie an einer eigenen Ebene zwischen Kacheln und Markern.

Und im Bildschirmfoto: „Seite ist 13px zu breit". Ein Marker am rechten
Bildrand schob die ganze Seite auseinander, seit die Karte lädt, was im
Ausschnitt liegt, und Marker deshalb bis an den Rand reichen. Die Marker
liegen jetzt in einer Ebene, die beschneidet.

## Die Berechtigungen

Im Manifest stehen jetzt Internet und Standort, grob und fein, mit einer
Begründung je Zeile. Dazu `@capacitor/geolocation`, und das ist die
eigentliche Falle: `navigator.geolocation` gibt es im WebView, aber Capacitor
beantwortet die Berechtigungsfrage nur, wenn das Plugin dabei ist. Fehlt es,
schlägt die Abfrage still fehl. Kein Dialog, keine Meldung, die Karte bleibt
auf der Vorgabeposition stehen.

Bewusst nicht im Manifest: Kamera, Speicher, Mitteilungen. Aufgenommen wird
über die Dateiauswahl des Systems, die bringt ihre Rechte selbst mit. Eine
Berechtigung ohne Verwendung wird zu Recht abgelehnt.

## Was Attrappe war

| Stelle | Was sie tat | Was sie jetzt tut |
|---|---|---|
| Teilen (5 Stellen) | meldete „Link kopiert", kopierte nichts | teilt über das Gerät, sonst Zwischenablage |
| QR-Code im Gastro-Bereich | zeigte ein Symbol aus der Icon-Sammlung | erzeugt einen echten Code, druckt, speichert |
| Sortiermenü (2 Stellen) | klappte zu | sortiert nach neu oder nach Bewertung |
| Hilfe im Kopfmenü | schloss das Menü | führt zu den Richtlinien |
| Autor blockieren | nichts | ausgeblendet, kommt mit den Videos |
| Kommentare, Freunde, Bestellen | ausgegraut mit Schloss | ausgeblendet, bis es sie gibt |

Der QR-Code war der gefährlichste davon. Ein Betrieb hätte ihn ausgedruckt
und aufgehängt, und Gäste hätten auf ein Bild gescannt, in dem keine Adresse
steht.

## Cafés, Eisdielen, alles zum Mitnehmen

Der Import holt jetzt auch Konditoreien, Kaffeeröster mit Ausschank und
Foodcourts, und er vergibt endlich die Kategorien „Eisdiele" und „Pub", die es
in den Filtern längst gab. Vorher landeten beide unter „Café" und „Bar", und
die Filter dafür konnten nie etwas finden.

Cafés und Bäckereien waren übrigens schon vorher dabei, 32 und 31 Stück. Was
neu ist, sieht man erst nach dem nächsten Lauf von „Testdaten holen".

## Keine erfundenen Betriebe

Jetzt nachweisbar: `orte-pruefen.mjs` verlangt von jedem Betrieb eine `osmId`
in der Form `node/123`, `way/123` oder `relation/123`. Wer keine hat, kommt
nicht in den Bestand. 359 von 359 haben eine.

## Stand am Ende

Server 46 + 19 + 27, Daten 14 von 14, Website 43 von 43, Kartenrechnung
18 von 18, gegen den echten Server 9 von 9, App 17 von 17, Bildschirmfotos in
zwei Breiten ohne Auffälligkeiten.

## Was mich diese Runde gelehrt hat

- **Ein Knopf, der eine Erfolgsmeldung zeigt, ohne etwas zu tun, ist schlimmer
  als ein Knopf, der nichts tut.** Fünfmal „Link kopiert" ohne Kopie, und
  niemandem wäre es aufgefallen, bis jemand die Zwischenablage einfügt.
- **Zuhörer für Gesten gehören auf eine eigene Ebene.** Am Elternelement
  fangen sie die Knöpfe ab, die darauf liegen.
- **Ein Symbol, das aussieht wie eine Sache, ist nicht die Sache.**

→ zurück zu [[STAND]] · vorherige Runde [[03-verlauf/12-die-stille-app]]
