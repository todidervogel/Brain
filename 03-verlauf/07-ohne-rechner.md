# Runde 7 — Alles ohne Rechner

**Gefragt:** „kann ich des auch am Handy ausführen, builden lassen"

## Antwort in einem Satz

Bauen ja, komplett ohne Rechner — GitHub baut, das Handy lädt herunter.
Selbst bauen *auf* dem Handy lohnt nicht.

## Was gemacht wurde

**1. Die Lücke aus der Aufteilung geschlossen.**
Das Repository `Website-` hatte keinen Ablauf zum Veröffentlichen — der lag im
Monolithen und war beim Aufteilen liegen geblieben. Jetzt gibt es
`.github/workflows/pages.yml`: Push auf `main` oder Start von Hand, mit einem
Feld für die Serveradresse. Zwei Feinheiten, die man leicht übersieht:

- Pages liefert nur Dateien aus. Der Ablauf legt `404.html` als Kopie von
  `index.html` an, sonst gäbe jeder direkte Aufruf einer Unterseite einen
  Fehler.
- Der Pfad ist `/Website-/`, nicht `/`. `VITE_BASE` setzt ihn, `main.jsx`
  reicht ihn als `basename` an den Router weiter.

Geprüft: mit `VITE_BASE=/Website-/` gebaut, unter genau diesem Pfad
ausgeliefert, Startseite und `/karte` im Browser geöffnet — beides lädt, keine
Fehler in der Konsole. Der Push hat den Ablauf ausgelöst, Bau und
Veröffentlichung sind grün:

> **<https://todidervogel.github.io/Website-/>**

(Die Seite selbst konnte ich von hier aus nicht abrufen — der Netzzugang
dieser Umgebung lässt github.io nicht durch. Grün ist der Lauf, mit
„Reported success" und genau dieser Adresse im Protokoll.)

**2. Ein echter Fehler in der App gefunden.**
Die Anleitung versprach, die APK könne mit `--api http://192.168.x.x:4000`
gegen den Server im WLAN laufen. Das hätte nicht funktioniert: Seit Android 9
verbietet das System unverschlüsselte Verbindungen, und `targetSdkVersion`
steht auf 34. `allowMixedContent` in der Capacitor-Konfiguration regelt nur
das Verhalten der WebView, nicht die Richtlinie des Systems.

Behoben mit `android/app/src/debug/AndroidManifest.xml`
(`usesCleartextTraffic="true"`) — nur im Debug-Bau, nicht in einer
Veröffentlichung. `tools/pruefen.mjs` prüft die Datei jetzt mit, damit sie
nicht wieder verschwindet.

**3. Der APK-Bau hat nie funktioniert.**
Aufgefallen erst, weil ich den Ablauf zur Kontrolle gestartet habe. Gradle
brach ab, bevor er anfing:

```
Could not read script 'android/capacitor-cordova-android-plugins/
cordova.variables.gradle' as it does not exist.
```

Die Datei liegt bewusst nicht im Repository — `android/.gitignore` schließt
`capacitor-cordova-android-plugins`, `assets/public` und die erzeugten
Konfigurationsdateien aus, weil Capacitor sie erzeugt. Nur hat sie niemand
erzeugt: `tools/build.mjs` kopierte `dist` von Hand ins Android-Projekt und
rief `cap sync` nie auf, und der Ablauf installierte die Abhängigkeiten der
App gar nicht erst.

Jetzt macht es Capacitor selbst. `webDir` zeigt dafür auf `.website/dist`.
Nebeneffekt, der vorher fehlte: `assets/capacitor.config.json` wird
mitgeschrieben — ohne sie gälten in der App die Voreinstellungen, also weder
unser Schema noch `allowMixedContent`. Die Korrektur aus Punkt 2 wäre ohne
diese Datei wirkungslos geblieben.

Geprüft: Lauf 3 ist grün, `tellerrand-apk` liegt als Artefakt bereit
(3,5 MB).

**Die Lehre:** Ein Ablauf, der nie gelaufen ist, ist kein Ablauf. Beim
Aufteilen in Runde 6 wurde der APK-Bau übernommen und für erledigt gehalten,
ohne ihn ein einziges Mal zu starten.

**4. Die Grenze aufgeschrieben, statt sie später zu erleben.**
Eine über Pages ausgelieferte Seite läuft unter https und kann einen
http-Server im WLAN grundsätzlich nicht erreichen. Es gibt also drei
brauchbare Aufbauten, nicht vier:

| Aufbau | Daten |
|---|---|
| Pages-Seite, kein Server | im Browser des Geräts |
| APK, kein Server | im Handy |
| APK plus Server im WLAN | beim Server |

**5. `OHNE-RECHNER.md`** fasst den ganzen Weg über den Browser zusammen:
APK über Actions, Webseite über Pages, Code am Handy ändern — und was in
Termux realistisch ist (der Server ja, er hat keine Abhängigkeiten; die
Website mühsam; das Android-SDK nein).

**6. Zwei Repositories zeigen noch auf den alten Zweig.**
Im `design`-Repo scheitert das Veröffentlichen: Bau grün, Veröffentlichen
abgewiesen, ohne einen einzigen Schritt. Grund ist der **Standardzweig** —
dort steht noch `claude/design-spec-screens-components-omyfb5`, und GitHub
lässt in die Umgebung `github-pages` von Haus aus nur den Standardzweig
hinein. In `Brain` steht ebenso noch `claude/app-website-mvp-3w6arm`.

Das lässt sich von hier aus nicht ändern — die verfügbaren GitHub-Werkzeuge
können Repository-Einstellungen nicht schreiben.

Nebenbei aufgefallen: Im `design`-Repo lag noch der APK-Ablauf aus dem
Monolithen. Ohne `android/` und `tools/build.mjs` wäre er dort sofort
gescheitert — entfernt.

## Offen

- **Standardzweig auf `main` stellen** in `design` und `Brain`:
  Settings → General → Default branch. Erst danach veröffentlicht die
  Galerie. `Website-`, `App` und `Server` stehen schon richtig.
- Ein öffentlich erreichbarer Server unter https fehlt weiterhin. Erst damit
  wird die Pages-Seite mehr als eine Vorschau.
- Schritt 2 (alle Dev-Sachen entfernen) ist weiter unangetastet, siehe
  `04-naechste-schritte/SCHRITT-2-DEV-ENTFERNEN.md`.
