# Runde 12: Die stille App

## Der Auftrag

Zwei Sätze vom Handy, und beide zeigten auf dieselbe Stelle:

> Wenn ich in der App auf anmelden drücke bei der Anmeldung passiert nichts.

> Und wenn ich offline bin also der Server nicht erreichbar sein kann kommt
> auch die Fehlermeldung in der App nicht.

In Runde 11 hatte ich die Anmeldung schon einmal repariert. Damals stand im
Formular ein Übersetzungsschlüssel statt eines Satzes. Das war ein echter
Fehler, und er ist auch weg. Er war nur nicht der, den das Handy meinte.

## Nachgestellt, nicht geraten

Vier Lagen, alle im Browser mit der gebauten Oberfläche, Plattform „app":

| Lage | vorher |
|---|---|
| gar kein Server eingestellt | Anmeldung läuft lokal, weiter zum Feed |
| toter ngrok-Tunnel, 404 mit HTML | Meldung kommt, nach 62 ms |
| Verbindung verweigert | Meldung kommt, nach 65 ms |
| **Verbindung hängt** | **nichts. Der Knopf dreht sich weiter, für immer** |

Die vierte Zeile ist der Fehlerbericht. `fetch` hat ohne Zutun **keine**
Frist. Nimmt die Gegenstelle die Verbindung an und antwortet dann nie, wartet
der Aufruf unbegrenzt. Genau das macht ein abgelaufener ngrok-Tunnel, ein WLAN
mit Anmeldeseite, ein Funkloch mitten im Verbindungsaufbau.

Und damit erklärt sich auch der zweite Satz: Das Verbindungsband fragt beim
Start `/api/health`. Dieser Aufruf hing genauso. Das Band, das die Meldung
zeigen soll, erschien deshalb nie. Ein Fehler, zwei Beschwerden.

## Was jetzt gilt

Jeder Aufruf hat eine Frist (`src/lib/store/api.js`):

    15 Sekunden   normal
     8 Sekunden   Nachfragen, die nur „lebst du?" bedeuten
    60 Sekunden   wenn wirklich Daten hochgehen, ab 100 kB Rumpf
     8 Sekunden   wenn die Störung schon feststeht

Die Uhr läuft von Hand über `AbortController` und `setTimeout`, nicht über
`AbortSignal.timeout`. Das gibt es erst in neueren Browsern, und die App läuft
auch auf Geräten mit älterem WebView. Ein fehlendes `AbortSignal.timeout` wäre
dort ein Fehler mitten im Aufruf gewesen, also genau die Stille, die wir
gerade abstellen.

Dazu zwei Dinge, die unabhängig davon Stille erzeugt hätten:

**Formulare bleiben nicht mehr stumm.** `useForm` fing bisher nur den Fall ab,
dass `onSubmit` ordentlich `{ ok: false }` zurückgibt. Wirft es stattdessen,
lief der Fehler an der Anzeige vorbei: Der Knopf hörte auf zu drehen, sonst
passierte nichts. Jetzt gibt es einen Satz, am Netz gescheitert einen eigenen.

**Das Gerät wird gefragt.** Flugmodus und WLAN weg meldet der Browser selbst,
sofort. `src/lib/store/connection.js` hört jetzt darauf, statt auf den ersten
gescheiterten Aufruf zu warten. Nur der Weg nach „weg" wird so gegangen:
`navigator.onLine` heißt „es gibt eine Verbindung", nicht „unser Server
antwortet".

## Der Fehler, den niemand gemeldet hat

Beim Prüfen der Abhängigkeiten fiel eine zweite Sache auf, die zugeschlagen
hätte, sobald der Server wieder läuft.

Ein kostenloser ngrok-Tunnel schiebt Browsern eine Warnseite dazwischen,
statt die Anfrage durchzulassen. Sie kommt mit Status 200 und HTML, sieht für
den Code also aus wie eine Antwort. `res.json()` scheitert daran, und die
Anmeldung bekäme ein leeres Ergebnis statt eines Zugangsmerkmals.

Übersprungen wird die Seite mit der Kopfzeile `ngrok-skip-browser-warning`.
Die schickte bisher nur die Adressprüfung, nicht die normalen Aufrufe. Und sie
hätte selbst dort nicht funktioniert: Eine Kopfzeile, die der Server in
`access-control-allow-headers` nicht freigibt, lässt der Browser gar nicht
erst los. Die Voranfrage scheitert, und in der App sieht das aus, als sei der
Server kaputt.

Beides ist jetzt zusammengeführt: Die App schickt die Kopfzeile bei jedem
Aufruf, der Server gibt sie frei, und der Rauchtest prüft die Voranfrage aus
der Herkunft `https://localhost`, unter der die APK läuft.

## Was die Prüfungen jetzt abdecken

Drei neue Fälle in `Website-/tools/verhalten.mjs`, mit zwei Aushilfsservern,
die der Test selbst startet: einer, der annimmt und schweigt, einer, der wie
ein abgelaufener Tunnel mit 404 antwortet.

- Schweigt der Server, sagt die Anmeldung trotzdem Bescheid
- Schweigt der Server, erscheint das Verbindungsband
- Ein abgelaufener Tunnel ist kein Anmeldefehler

Vier neue im Rauchtest des Servers, für die Voranfrage und die Kopfzeilen.

Stand am Ende: Server 46 + 19 + 27, Daten 12 von 12, Website 38 von 38,
Kartenrechnung 18 von 18, gegen den echten Server 9 von 9, App 12 von 12,
Bildschirmfotos ohne Auffälligkeiten.

## Was mich das gelehrt hat

Eine Meldung, die von einer Antwort abhängt, gibt es nicht, wenn keine Antwort
kommt. Das Verbindungsband war dafür gebaut, zu sagen „der Server antwortet
nicht", und hing dabei selbst an einer Antwort des Servers. Alles, was auf ein
Netz wartet, braucht eine eigene Uhr.

Und: Runde 11 hat einen echten Fehler an derselben Stelle behoben und ihn für
den ganzen gehalten. Der Unterschied zwischen „falscher Text" und „gar kein
Text" steht in der Rückmeldung, wenn man sie wörtlich nimmt.

→ zurück zu [[STAND]] · vorherige Runde [[03-verlauf/11-fehler-und-flaeche]]
