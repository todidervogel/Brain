# Selbst testen

Von null auf laufenden MVP. Gebraucht wird **Node 22.5 oder neuer** (der
Server benutzt `node:sqlite`), sonst nichts.

## Der schnellste Weg: nur die Oberfläche

```bash
git clone https://github.com/todidervogel/Website-
cd Website-
npm install
npm run dev
```

→ <http://localhost:5173>. Die Daten liegen im Browser, jedes Fenster für
sich. Zum Ausprobieren der Oberfläche reicht das.

Anmelden mit `test@user.de` / `12345aA?`, oder unten rechts im Design-Panel
über die Zeile **Rolle** direkt in eine Rolle springen.

> **Der Feed ist leer, und das stimmt so.** Im Ausgangsbestand stehen 360
> echte Betriebe und drei Zugänge, keine Videos, keine Bewertungen, keine
> Speisekarten. Die Beispieldaten sind seit Runde 9 heraus. Wer volle Screens
> sehen will, führt die Prüfungen aus: `npm run bilder` spielt einen
> Prüfbestand ein und legt Bildschirmfotos in `bilder/` ab.

## Der ganze MVP mit Server

```bash
# Fenster 1
git clone https://github.com/todidervogel/Server
cd Server
npm start                        # http://localhost:4000, lädt nichts nach

# Fenster 2
git clone https://github.com/todidervogel/Website-
cd Website-
npm install
VITE_API=http://localhost:4000 npm run dev
```

Jetzt liegen die Daten beim Server, und alle Geräte sehen denselben Stand.
Im Design-Panel steht unter **Betriebsart**, welcher Weg gerade läuft.

## Was man dann ausprobieren sollte

Diese fünf Dinge zeigen, dass es wirklich zusammenhängt:

**1. Zwei Browser, ein Stand.**
In Fenster A als Gastro anmelden (`test@gastro.de` / `12345aA?`), unter
*Speisekarte* eine Kategorie und ein Gericht anlegen. In Fenster B dieselbe
Betriebsseite öffnen, das Gericht ist da. Ohne Server ginge das nicht.
(Welcher Betrieb dem Testkonto gehört, steht im Gastro-Bereich oben.)

**2. Ein Video von Anfang bis Ende.**
Als Nutzer im Design-Panel auf Ziel **App** stellen, dann *Aufnehmen* → fünf
Schritte durchgehen → veröffentlichen. Danach als Verwaltung (`topic` /
`admin`) unter *Video-Freigabe* nachsehen: Es liegt in der Warteschlange.
Freigeben, und es steht auf der Gastro-Seite. Der Nutzer bekommt eine
Benachrichtigung.

**3. Die Rechte hängen wirklich am Server.**
Als einfacher Nutzer anmelden, dann in der Browserkonsole:

```js
await fetch('http://localhost:4000/api/rpc', {
  method: 'POST',
  headers: { 'content-type': 'application/json',
             authorization: `Bearer ${localStorage.getItem('app-token')}` },
  body: JSON.stringify({ method: 'admin.overview', args: [] }),
}).then((r) => r.status)
```

→ `403`. Die Oberfläche bietet den Knopf gar nicht erst an, aber selbst wer
daran vorbeigeht, kommt nicht durch.

**4. Eine Meldung, die etwas bewirkt.**
Auf einer beliebigen Betriebsseite unter *Infos* → *Problem melden* →
„dauerhaft geschlossen" absenden. Dreimal, mit drei verschiedenen Konten.
Danach steht auf der Seite ein Warnhinweis, und die Meldung liegt bei der
Verwaltung unter *Meldungen*.

**6. Der Neustart.**
Etwas anlegen, dann den Server mit Strg-C beenden und wieder starten. Alles
ist noch da, auch die Anmeldung. Genau das prüft `npm test` im Server-Repo
mit einem echten Herunterfahren.

**7. Die Karte.**

```bash
curl -o kachel.png localhost:4000/api/karte/kachel/13/4259/2791.png
curl "localhost:4000/api/karte/betriebe?nord=48.6&sued=48.45&west=8.0&ost=8.2" | head
```

Die Kachel kommt immer, notfalls selbst gezeichnet, wenn der Kachelanbieter
nicht erreichbar ist.

**5. Datenschutz.**
Unter *Einstellungen → Meine Daten herunterladen* kommt eine echte JSON-Datei.
Unter *Konto löschen* verschwindet das Profil, die Bewertungen bleiben
anonymisiert stehen, genau wie es die Datenschutzerklärung beschreiben muss.

## Aufs Handy

Wer gar keinen Rechner benutzen will: **`OHNE-RECHNER.md`** beschreibt den
ganzen Weg über den Browser, APK über GitHub Actions, Webseite über GitHub
Pages, und die eine Falle dabei (eine https-Seite darf keinen http-Server im
WLAN ansprechen).

```bash
git clone https://github.com/todidervogel/App
cd App
npm run build:apk       # braucht zusätzlich Java 17 und das Android-SDK
```

Ohne Android-SDK geht es über GitHub: Repo `App` → **Actions** → *APK bauen* →
**Run workflow**. Einzelheiten in `App/docs/INSTALLIEREN.md`.

Damit die App den Server nutzt: **in der App eintragen**, unter
*Einstellungen → Verbindung*. Das ist seit Runde 9 der bequeme Weg, die APK
wird einmal gebaut, die Adresse kann sich danach ändern.

Wer sie fest einbacken will (nicht `localhost`, das wäre das Handy selbst):

```bash
node tools/build.mjs --api http://192.168.1.20:4000 --apk
```

## Nur das Design ansehen

```bash
git clone https://github.com/todidervogel/design
cd design && npm install && npm run dev
```

Eine Galerie mit jedem Baustein, umschaltbar zwischen hell und dunkel. Ohne
Server, ohne Daten.

## Zugänge

Alles erfunden. Bestätigungscode bei der Registrierung: `123456`, oder
einfach **„Überspringen"** drücken.

| Rolle | Anmeldung | Passwort |
|---|---|---|
| Verwaltung | `topic` | `admin` |
| Gastro | `test@gastro.de` | `12345aA?` |
| Nutzer | `test@user.de` | `12345aA?` |

> `admin` ist ein Platzhalter, kein Passwort. Der Server erinnert bei jedem
> Start daran, solange er gilt.

## Wieder auf Anfang

| Wo | Wie |
|---|---|
| Website allein | Design-Panel → **Daten zurücksetzen** |
| Server | `npm run reset` im Server-Repo (löscht Datenbank und Kacheln). Über die Adresse `POST /api/reset` geht es auch, aber nur angemeldet als Verwaltung. |
| Alles im Browser | Entwicklerwerkzeuge → Anwendung → Lokalen Speicher leeren |

## Wenn etwas nicht geht

| Beobachtung | Grund |
|---|---|
| Website zeigt nichts, Konsole meldet `fetch failed` | Server läuft nicht, oder `VITE_API` zeigt woanders hin |
| Anmeldung geht, aber nach dem Neuladen ist man wieder draußen | Der Browser darf nichts speichern (privates Fenster) |
| App bleibt weiß | Absolute Pfade, `npm run pruefen` im App-Repo sieht genau das nach |
| Handy erreicht den Server nicht | Nicht im selben WLAN, oder `localhost` statt der Rechneradresse |
| Über GitHub Pages geöffnet, Server antwortet nicht | Die Seite läuft unter https, der Server unter http, der Browser blockiert das. Siehe `OHNE-RECHNER.md` |
