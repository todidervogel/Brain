# Runde 10: Sichtbar und selbsttätig

Eine kurze Runde mit vier Aufträgen, von denen drei dasselbe Thema haben:
**Man soll sehen, was los ist, und nichts abtippen müssen.**

## Der Auftrag

1. Die Workflows sollen zeigen, wie der Server läuft.
2. Erklären, was das Kästchen im Server-Workflow tut und was „Testdaten holen"
   eigentlich macht.
3. Der APK-Bau soll die ngrok-Adresse selbst nehmen.
4. Die App soll es melden, wenn der Server nicht erreichbar ist.
5. Und ab jetzt bei jedem Schritt: Abhängigkeiten prüfen, auf Fehler prüfen,
   Kommentare und Dokumentation mitziehen.

## Man sieht dem Server beim Laufen zu

Vorher war ein laufender Server eine Blackbox. Man startete ihn, bekam eine
Adresse, und danach stand im Protokoll eine halbe Stunde lang nichts. Ob
überhaupt jemand etwas abruft, sah man nicht.

`src/http/zaehler.js` zählt jetzt mit, an genau einer Stelle:

```js
res.once('finish', () => zaehlen(url.pathname, res.statusCode))
```

Am Ende der Antwort, nicht am Anfang. Nur so steht der Status wirklich fest,
und ein einziger Haken fängt jeden Weg durch den Server, auch die vorzeitigen
Rückgaben.

Der Workflow zeigt es alle 30 Sekunden:

```
  Zeit  | Anfragen (wohin)                                    | Sitzungen | zuletzt
  ------+-----------------------------------------------------+-----------+--------
    3 min |    47  Karte 38, RPC 6, Bilder 2, Anmeldung 1, Fehler 0 |         1 | vor 4s
```

Dazu kommt, was der Server selbst gemeldet hat, seit dem letzten Blick.

Die Zähler sind absichtlich **nicht** dauerhaft. Beim Neustart fangen sie bei
null an, und das ist richtig: Sie beschreiben diesen Lauf, nicht die
Geschichte des Projekts. Was dauerhaft ist, steht in der Datenbank.

## Die Adresse muss niemand mehr abtippen

Über ngrok bekommt der Server bei jedem Start eine neue Adresse, wenn keine
feste hinterlegt ist. Sie auf einem Handy jedes Mal abzutippen ist eine
Zumutung, und für jede neue Adresse eine neue APK zu bauen dauert Minuten, die
man nicht hat.

Jetzt schreibt der Lauf sie in `adresse.json` in das Repo `Server`:

```json
{
  "adresse": "https://sneeze-till-paternity.ngrok-free.dev",
  "seit": "2026-09-08T07:12:00Z",
  "laeuftBis": "2026-09-08T08:12:00Z",
  "beendet": false,
  "lauf": "https://github.com/todidervogel/Server/actions/runs/123"
}
```

Alle fünf Repositories sind öffentlich, also liegt die Datei unter
`raw.githubusercontent.com` und braucht keine Zugangsdaten. Zwei Stellen lesen
sie: der APK-Bau, wenn das Eingabefeld leer bleibt, und die App über
*Einstellungen, Verbindung, Aktuelle Adresse holen*.

Geschrieben wird über die Contents-API, nicht mit `git push`: Der Runner klont
nur den letzten Commit, und ein Rebase darauf ist unzuverlässig. Ein PUT auf
eine einzelne Datei kann außerdem nichts überfahren, was jemand anderes gerade
geschoben hat.

Am Ende des Laufs wird `beendet: true` gesetzt. Wer die Adresse danach holt,
bekommt nicht eine tote, sondern die Auskunft, dass gerade keiner läuft. Der
APK-Bau backt dann bewusst **keine** Adresse ein: eine tote Adresse fest in
der App wäre schlechter als gar keine.

### Was dort nicht steht

Kein Token, kein Passwort. Nur eine Adresse, die ohnehin jeder kennt, der die
App benutzt. Wer sie hat, kommt an dieselbe Anmeldung wie alle anderen und
nicht daran vorbei.

## Die App sagt es, wenn nichts da ist

Zwei Fälle, zwei Meldungen, beide oben auf jedem Bildschirm:

- **Der Server antwortet nicht.** Gab es schon seit Runde 8, aber erst
  nachdem ein Bildschirm vergeblich Daten geholt hatte. Jetzt fasst das Band
  beim Start selbst einmal nach, damit bis dahin keine falsche Leermeldung
  dasteht.
- **Es ist gar keiner eingestellt.** Das funktioniert, ist aber etwas anderes,
  als die meisten erwarten: Nichts wird geteilt, niemand sonst sieht es. In
  der App sagen wir das. Auf der Webseite nicht, dort ist der Alleinbetrieb
  der Normalfall für Besucher ohne Konto.

Beide Male derselbe Ausweg mit einem Tipp: *Aktuelle Adresse holen*. Geprüft
wird vor dem Übernehmen, denn eine Adresse, die im Verzeichnis steht, muss
noch nicht antworten.

## Die Arbeitsregeln stehen jetzt fest

`CLAUDE.md` in jedem der fünf Repositories. Sie gilt für jede Sitzung, auch
für spätere, und sie steht fünfmal, weil eine Sitzung immer nur ein Repo offen
hat.

Darin die Tabelle, die am meisten wert ist: **wer an was hängt.** Sie steht
dort, weil jeder Fehler dieser und der letzten Runde aus einer übersehenen
Abhängigkeit kam:

- `.gitignore` mit `data/` traf auch `src/data/`
- `.menu-item` gab es zweimal, in zwei Bausteinen
- `lib/map.js` bekam einen Import und ließ sich nicht mehr unter Node prüfen
- `seed.js` bekam eine Nachbardatei, und `tools/sync.mjs` kannte sie nicht

## Was die Prüfung diesmal gefunden hat

`tools/vollstaendig.mjs`, aus der letzten Runde, hat sofort angeschlagen:
`src/http/zaehler.js` war neu und noch nicht in git. Genau der Fehler, der die
Runde davor den Workflow lahmgelegt hat, diesmal in derselben Minute
gefunden, in der er entstand.

→ zurück zu [[STAND]] · vorherige Runde [[03-verlauf/09-datenbank-und-karte]]
