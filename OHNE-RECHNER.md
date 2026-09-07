# Ohne Rechner — alles vom Handy aus

Kurz: **Bauen geht komplett ohne Rechner.** GitHub baut, das Handy lädt
herunter. Was am Handy _selbst_ läuft, hat dagegen Grenzen — die stehen hier
auch, damit niemand eine halbe Stunde in einer Sackgasse verbringt.

## Was ohne Rechner geht

| Vorhaben | Ohne Rechner? | Wie |
|---|---|---|
| APK bauen und installieren | **ja** | GitHub Actions, unten Schritt für Schritt |
| Webseite als Adresse aufrufen | **ja** | GitHub Pages, unten |
| Design-Galerie ansehen | fast | ein Klick fehlt noch, siehe unten |
| Code ändern | ja | im Browser über github.com bearbeiten, Push löst den Bau aus |
| Server dauerhaft betreiben | nein | dafür braucht es einen Rechner oder einen Hoster |
| APK **auf** dem Handy bauen | praktisch nein | Gradle + Android-SDK in Termux, siehe unten |

## APK bauen — nur mit dem Browser

1. Im Repository `App` auf **Actions**.
2. Links **APK bauen**, rechts **Run workflow**.
3. Feld *Adresse des Servers* leer lassen (dann trägt die App ihre Daten
   selbst) oder die Adresse eines erreichbaren Servers eintragen.
4. Warten, bis der Lauf grün ist — einige Minuten.
5. Den Lauf öffnen, unten unter **Artifacts** `tellerrand-apk` antippen.

Geprüft: Der Ablauf läuft durch, die APK ist rund 3,5 MB groß und liegt
90 Tage lang bereit.

Das Ergebnis ist eine ZIP-Datei. Android-Dateimanager können sie öffnen; darin
liegt `app-debug.apk`. Wer sich das sparen will, setzt einen Tag — dann hängt
die APK an einer Vorab-Version und lässt sich direkt herunterladen. Tags gehen
am Handy über **Releases → Draft a new release → Choose a tag → v0.1 →
Publish**; das startet denselben Ablauf.

Bei der Installation fragt Android nach der Erlaubnis für „unbekannte
Quellen". Das ist normal für eine APK, die nicht aus dem Play Store kommt.

## Webseite als Adresse — GitHub Pages

Läuft schon:

> **<https://todidervogel.github.io/Website-/>**

Jeder Push auf `main` baut sie neu; von Hand geht es über **Actions →
„Webseite veröffentlichen" → Run workflow**. (Falls Pages in einem anderen
Repository erst noch eingeschaltet werden muss: **Settings → Pages → Source:
„GitHub Actions"**.)

Damit lässt sich der ganze MVP am Handy im Browser ansehen, ohne irgendetwas
zu installieren.

**Aber:** So veröffentlicht läuft die Seite im **Alleinbetrieb** — die Daten
liegen im Browser des Besuchers, jedes Gerät für sich. Das reicht zum
Anschauen und Durchklicken, nicht für „zwei Geräte sehen denselben Stand".

### Was bei der Galerie noch fehlt

Im Repository `design` scheitert die Veröffentlichung, weil dort noch der
alte Entwurfszweig als **Standardzweig** eingetragen ist. GitHub lässt in die
Umgebung `github-pages` von Haus aus nur den Standardzweig hinein — der Bau
läuft durch, das Veröffentlichen wird abgewiesen.

Zu beheben mit einem Klick: **Settings → General → Default branch → `main`**.
Dasselbe gilt im Repository `Brain`, dort steht noch
`claude/app-website-mvp-3w6arm`. Danach den Ablauf einmal von Hand starten.

## Die Falle: https-Seite und http-Server

Eine über Pages ausgelieferte Seite läuft unter **https**. Ein Server im WLAN
läuft unter **http**. Browser verbieten diese Mischung — die Anfragen werden
stillschweigend blockiert. Eine Pages-Seite kann den Testserver im WLAN also
grundsätzlich nicht erreichen, egal was in `VITE_API` steht.

Drei Wege, die wirklich funktionieren:

| Aufbau | Daten | Wofür |
|---|---|---|
| Pages-Seite, kein Server | im Browser des Geräts | ansehen, herumklicken |
| APK, kein Server | im Handy | die App im Alltag ausprobieren |
| APK **plus** Server im WLAN | beim Server | mehrere Geräte, ein Stand |

Der dritte Weg braucht einen laufenden Rechner im selben WLAN. Die App darf
http benutzen, weil der Debug-Bau das ausdrücklich erlaubt
(`android/app/src/debug/AndroidManifest.xml`) — die Pages-Seite darf es nicht.

Sobald der Server einmal öffentlich unter https steht, fällt die Einschränkung
weg, und die Pages-Seite kann ihn ganz normal nutzen.

## Server auf dem Handy (Termux)

Möglich, aber Bastelei — und hier nicht ausprobiert.

Der Server hat **keine einzige Abhängigkeit** und benutzt nur, was in Node
schon eingebaut ist (`node:http`, `node:fs`, `node:path`, `node:crypto`). Ein
`npm install` entfällt also komplett:

```bash
pkg install nodejs-lts git
git clone https://github.com/todidervogel/Server
cd Server && npm start          # http://localhost:4000
```

Weil er auf allen Netzwerkkarten hört, ist er dann auch für andere Geräte im
WLAN erreichbar. Die Website daneben am Handy laufen zu lassen ist deutlich
mühsamer: Dafür braucht es `npm install` mit rund siebzig Paketen samt der
nativen Teile von esbuild und Rollup. Auf arm64 gibt es die zwar, aber wenn
etwas klemmt, klemmt es genau dort.

Die APK **auf** dem Handy zu bauen lohnt nicht: Dafür müssten Gradle, ein JDK
und das Android-SDK in Termux stehen. Der Umweg über GitHub Actions dauert
Minuten statt Stunden und ist der empfohlene Weg.

## Code am Handy ändern

Auf github.com jede Datei über das Stiftsymbol bearbeiten und auf `main`
committen. Das startet die Abläufe von selbst — die Webseite wird neu
veröffentlicht, die APK baut man danach von Hand.

Für mehr als kleine Korrekturen ist die GitHub-Mobil-App oder ein
Tablet mit Tastatur angenehmer als das Telefon.
