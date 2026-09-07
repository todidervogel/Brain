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
Fehler in der Konsole.

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

**3. Die Grenze aufgeschrieben, statt sie später zu erleben.**
Eine über Pages ausgelieferte Seite läuft unter https und kann einen
http-Server im WLAN grundsätzlich nicht erreichen. Es gibt also drei
brauchbare Aufbauten, nicht vier:

| Aufbau | Daten |
|---|---|
| Pages-Seite, kein Server | im Browser des Geräts |
| APK, kein Server | im Handy |
| APK plus Server im WLAN | beim Server |

**4. `OHNE-RECHNER.md`** fasst den ganzen Weg über den Browser zusammen:
APK über Actions, Webseite über Pages, Code am Handy ändern — und was in
Termux realistisch ist (der Server ja, er hat keine Abhängigkeiten; die
Website mühsam; das Android-SDK nein).

## Offen

- Die Pages-Quelle muss einmal von Hand eingestellt werden: Settings → Pages
  → Source: „GitHub Actions". Das kann kein Ablauf für sich selbst tun.
- Ein öffentlich erreichbarer Server unter https fehlt weiterhin. Erst damit
  wird die Pages-Seite mehr als eine Vorschau.
- Schritt 2 (alle Dev-Sachen entfernen) ist weiter unangetastet, siehe
  `04-naechste-schritte/SCHRITT-2-DEV-ENTFERNEN.md`.
