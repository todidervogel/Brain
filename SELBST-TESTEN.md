# Selbst testen

Von null auf laufenden MVP. Gebraucht wird **Node 20 oder neuer**, sonst
nichts.

## Der schnellste Weg — nur die Oberfläche

```bash
git clone https://github.com/todidervogel/Website-
cd Website-
npm install
npm run dev
```

→ <http://localhost:5173>. Die Daten liegen im Browser, jedes Fenster für
sich. Zum Ausprobieren der Oberfläche reicht das.

Anmelden mit `max@beispiel.de` / `Passwort123`, oder unten rechts im
Design-Panel über die Zeile **Rolle** direkt in eine Rolle springen.

## Der ganze MVP — mit Server

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
In Fenster A als Gastro anmelden (`chef@trattoria-bella.de` / `Gastro123`),
unter *Speisekarte* ein Gericht anlegen. In Fenster B
<http://localhost:5173/g/trattoria-bella/speisekarte> öffnen — das Gericht ist
da. Ohne Server ginge das nicht.

**2. Ein Video von Anfang bis Ende.**
Als Nutzer im Design-Panel auf Ziel **App** stellen, dann *Aufnehmen* → fünf
Schritte durchgehen → veröffentlichen. Danach als Admin (`ana@intern` /
`Admin1234`) unter *Video-Freigabe* nachsehen: Es liegt in der Warteschlange.
Freigeben — und es steht auf der Gastro-Seite. Der Nutzer bekommt eine
Benachrichtigung.

**3. Die Rechte hängen wirklich am Server.**
Als einfacher Nutzer anmelden, dann in der Browserkonsole:

```js
await fetch('http://localhost:4000/api/rpc', {
  method: 'POST',
  headers: { 'content-type': 'application/json',
             authorization: `Bearer ${localStorage.getItem('app-token')}` },
  body: JSON.stringify({ method: 'videos.moderate', args: ['v4', 'published'] }),
}).then((r) => r.status)
```

→ `403`. Die Oberfläche bietet den Knopf gar nicht erst an — aber selbst wer
daran vorbeigeht, kommt nicht durch.

**4. Eine Meldung, die etwas bewirkt.**
Auf <http://localhost:5173/g/baeckerei-sommer> unter *Infos* → *Problem
melden* → „dauerhaft geschlossen" absenden. Dreimal, mit drei verschiedenen
Konten. Danach steht auf der Seite ein Warnhinweis, und die Meldung liegt bei
der Verwaltung unter *Meldungen*.

**5. Datenschutz.**
Unter *Einstellungen → Meine Daten herunterladen* kommt eine echte JSON-Datei.
Unter *Konto löschen* verschwindet das Profil, die Bewertungen bleiben
anonymisiert stehen — genau wie es die Datenschutzerklärung beschreiben muss.

## Aufs Handy

```bash
git clone https://github.com/todidervogel/App
cd App
npm run build:apk       # braucht zusätzlich Java 17 und das Android-SDK
```

Ohne Android-SDK geht es über GitHub: Repo `App` → **Actions** → *APK bauen* →
**Run workflow**. Einzelheiten in `App/docs/INSTALLIEREN.md`.

Damit die App den Server nutzt, beim Bauen die Adresse des Rechners im WLAN
mitgeben (nicht `localhost` — das wäre das Handy selbst):

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

Alles erfunden. Bestätigungscode bei der Registrierung: `123456`.

| Rolle | E-Mail | Passwort |
|---|---|---|
| Nutzer | `max@beispiel.de` | `Passwort123` |
| Nutzer | `lisa@beispiel.de` | `Passwort123` |
| Nutzer (privates Profil) | `jonas@beispiel.de` | `Passwort123` |
| Gastro | `chef@trattoria-bella.de` | `Gastro123` |
| Gastro (erstes Login) | `hallo@morgenrot-cafe.de` | `Start1234` |
| Admin | `ana@intern` | `Admin1234` |

## Wieder auf Anfang

| Wo | Wie |
|---|---|
| Website allein | Design-Panel → **Daten zurücksetzen** |
| Server | `npm run reset` im Server-Repo, oder `curl -X POST localhost:4000/api/reset` |
| Alles im Browser | Entwicklerwerkzeuge → Anwendung → Lokalen Speicher leeren |

## Wenn etwas nicht geht

| Beobachtung | Grund |
|---|---|
| Website zeigt nichts, Konsole meldet `fetch failed` | Server läuft nicht, oder `VITE_API` zeigt woanders hin |
| Anmeldung geht, aber nach dem Neuladen ist man wieder draußen | Der Browser darf nichts speichern (privates Fenster) |
| App bleibt weiß | Absolute Pfade — `npm run pruefen` im App-Repo sieht genau das nach |
| Handy erreicht den Server nicht | Nicht im selben WLAN, oder `localhost` statt der Rechneradresse |
