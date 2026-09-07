# Runde 5 — Von Entwurf zu MVP: alles mit Logik

Datum: 07.09.2026 · Repository `design`, Branch `claude/app-website-mvp-3w6arm`

## Was gefragt war

Zwei Dinge in einer Nachricht:

**A. Ein Prompt aus dem Design-Chat, der nie ausgeführt wurde:**

1. Die Website ist nur weiß — und in der APK gibt es keinen Dunkelmodus, den
   man einschalten könnte.
2. Im Profil fehlt eine Ladeanimation.
3. Wer abgemeldet ist, landet in jedem Fall auf der Anmeldeseite (zumindest
   in der App).
4. „Passwort vergessen" im Login sitzt ein bisschen zu hoch.
5. In der App soll man die Karte auch allein betrachten können, ohne diese
   Leiste.
6. Man soll auf den ersten Blick erkennen, was serviert wird — ob die Gastro
   nur Getränke, vegan kann, Fleisch, Fisch oder Meeresfrüchte hat.
7. Seiten mit Speisekarte hinzufügen, im Stil einer guten reinen Speisekarte.

**B. Der eigentliche Auftrag:** App und Website fertig entwerfen, aber
diesmal *mit Logik und allem drum und dran* für das MVP. Schritt 2 wird
danach: alle Dev-Sachen entfernen.

Und: Fortschritt und Kontext ins `Brain`-Repository schreiben.

## Was gemacht wurde

### Das Fundament: eine Logikschicht

Der Prototyp war statisch. Jetzt gibt es:

- `src/data/seed.js` — Ausgangsdaten nach dem Datenmodell aus dem Konzept.
  Zehn Betriebe, fünf Speisekarten, zehn Videos, acht Bewertungen, acht
  Konten in drei Rollen, Meldungen, Einladungen, Vorschläge, Protokoll.
- `src/lib/store/db.js` — ein Zustandsbaum im `localStorage`, versioniert,
  mit Beobachtern.
- `src/lib/store/api.js` — **die Fassade**, elf Bereiche. Der Austauschpunkt
  für Supabase. Gibt Versprechen zurück, mit kleiner Verzögerung, damit
  Ladezustände sichtbar sind.
- `src/lib/store/geo.js` — Luftlinie, Entfernungsschreibweise, Kartenprojektion.
- `src/lib/store/hours.js` — Öffnungszeiten in Minuten, „jetzt geöffnet"
  wirklich gerechnet, auch über Mitternacht hinaus.
- `src/lib/store/index.jsx` — `useQuery` mit stillem Nachladen, `useMutation`.
- `src/lib/session.jsx` — Anmeldung, Rollen, Registrierung mit Code,
  Passwortwechselzwang beim Gastro-Erstlogin.
- `src/lib/form.js` — `useForm` mit Regelwerk und deutschen Fehlermeldungen.
- `src/lib/upload.jsx` — der Upload-Entwurf über fünf Schritte, mit Wächter.

Der Ordner `src/mock/` ist verschwunden. Kein Screen greift mehr direkt auf
Daten zu.

### Die sieben Punkte aus dem Prompt

**1. Dunkelmodus.** Gilt jetzt für Website *und* App, Standard ist
„Automatisch" (folgt dem Gerät). Der Umschalter steht in der Kopfleiste —
auch auf der Anmeldeseite, denn genau dort hing es: Die Einstellung lag
hinter der Anmeldung, und die App zeigt ohne Anmeldung nur die Anmeldeseite.
Zusätzlich in den Einstellungen und im Design-Panel. Die Farbe der
Android-Statusleiste zieht mit.

**2. Ladeanimation im Profil.** Das Profil lädt jetzt wirklich, also gibt es
auch wirklich etwas zu zeigen: Skelettkacheln plus einen Kreisel darunter.
Zwei neue Bausteine, `Spinner` und `LoadingBlock`, ziehen sich durch alle
Screens, die nachladen.

**3. Abgemeldet → Anmeldeseite.** Bleibt so und ist jetzt lückenlos: Der
Routenwächter leitet in der App jede geschützte Route um, und jede
Abmelde-Schaltfläche (Kopfleiste, Einstellungen, Gastro-Seitenleiste,
Admin-Seitenleiste) navigiert zusätzlich selbst dorthin. Vorher setzte sie
nur einen Schalter um.

**4. „Passwort vergessen".** Saß über einem negativen Rand direkt am
Passwortfeld. Steht jetzt in einer eigenen Zeile mit ordentlichem Abstand
(`.login-forgot`), gleich auf der Gastro-Anmeldung.

**5. Reine Kartenansicht.** Schalter unten rechts auf der Karte (mobil).
Eingeschaltet verschwinden Kopfleiste, Banner, untere Navigation und
Ergebnisblatt. Es bleiben die Karte, ein Zurück-Knopf und die
OSM-Pflichtangabe. Der Zustand wird gemerkt.

**6. Angebot auf einen Blick.** Neues Feld `serving` mit zehn Werten, als
Symbolzeile (`ServingRow`). Steht auf der Gastro-Seite ganz oben, in jeder
Trefferliste, in der Kartenvorschau, in der Restaurantleiste des Feeds, in
der Betriebswahl beim Hochladen und auf der Speisekarte. Ist Filter auf der
Karte und in der Suche. Wird vom Gastro selbst gepflegt. „Nur Getränke" ist
ein eigener, deutlich hervorgehobener Fall.

**7. Speisekarte.** Eigene Seite `/g/[slug]/speisekarte` ohne App-Rahmen:
Sprungleiste, die dem Scrollen folgt, Suche, Gerichte mit Preis,
Beschreibung, Diät-Kennzeichnung, Schärfe, Gästebewertung pro Gericht,
Allergene als hochgestellte Zahlen mit Legende, Druckansicht. Dazu ein
Reiter mit Vorschau auf der Gastro-Seite und ein vollständiger Editor unter
`/gastro/speisekarte`. Der QR-Code kann jetzt wahlweise direkt auf die Karte
zeigen. Details in `01-design/SPEISEKARTE.md`.

### Alles andere mit Logik

- **Konto:** Registrierung mit Formatprüfung, Doppelvergabe, Altersgrenze 16;
  Bestätigungscode mit Einfügen aus der Zwischenablage; Anmeldung mit
  rollenabhängiger Weiterleitung; Passwort zurücksetzen.
- **Karte:** alle Filter filtern wirklich (Umkreis, geöffnet, Kategorie,
  Bewertung, Preis, nur mit Videos, Angebot), Marker aus echten Koordinaten,
  Vorschaukarte beim Antippen.
- **Feed:** Umkreislogik nach Konzept 8.4 — Bonus für ausgefüllte Bewertung
  und Ortsprüfung, Zufallskomponente, Gesehenes ans Ende, Hinweis bei zu
  wenig Inhalt. Blättern per Wischen, Mausrad, Pfeiltasten.
- **Suche:** vier Reiter mit Trefferzahlen, echtem Verlauf, Filtern. Orte
  verschieben den Kartenmittelpunkt — die Reiseplanung aus Konzept 8.8.
- **Upload:** fünf Schritte, gemeinsamer Entwurf, Ortsprüfung unter 150 m,
  Bewertungsdialog mit Gerichten aus der echten Speisekarte, am Ende ein
  Video in der Freigabewarteschlange.
- **Profil und Einstellungen:** echtes Bearbeiten, echter Datenexport als
  JSON-Datei, echte Kontolöschung mit Anonymisierung der Bewertungen.
- **Gastro:** Dashboard mit gerechneten Zahlen, Aufgabenliste, die sich aus
  den Daten selbst beantwortet; Videos hochladen, verbergen, löschen;
  Speisekarte pflegen; auf Bewertungen antworten und sie beanstanden; Profil,
  Angebot und Öffnungszeiten pflegen; Schließung melden.
- **Admin:** Freigabe mit Tastenkürzeln, Ablehnung mit Grund und
  Benachrichtigung, Meldungen bearbeiten, Betriebe verifizieren und
  archivieren, Einladungen, Nutzer verwarnen und sperren, Vorschläge, echtes
  Protokoll.

### Design-Panel

Neu gebaut: Ziel, Rolle (Gast/Nutzer/Gastro/Admin), Darstellung
(Auto/Hell/Dunkel), Screen-Variante, reine Kartenansicht, Banner und
**Daten zurücksetzen**.

## Was geprüft wurde

- `vite build` läuft durch.
- Alle 55 Routen automatisiert aufgerufen, in fünf Kombinationen
  (Website·Gast·hell, Website·Nutzer·dunkel, App·Nutzer·dunkel,
  App·Gastro·hell, Website·Admin·hell) — auf JS-Fehler, Konsolenfehler,
  fehlende Übersetzungen und sichtbare Platzhalter.
- 27 gezielte Verhaltenstests für die neuen Abläufe — alle grün.
- Prüfskripte liegen unter `design/tools/pruefung/` (siehe README dort).

## Was die Prüfung gefunden hat

Sieben Dinge, die ohne die Tests nicht aufgefallen wären. Alle behoben.

1. **Google Fonts blockierten den Seitenaufbau.** `index.html` lud Inter von
   Googles Servern. Das ist gleich dreifach ungünstig: Es blockiert das erste
   Bild, in der verpackten App ist die Datei offline gar nicht erreichbar, und
   rechtlich ist die Einbindung ohne Einwilligung nach dem Urteil des
   LG München I (20.01.2022, 3 O 17493/20) angreifbar — die IP-Adresse der
   Besucherin wandert zu Google. Jetzt wird nichts mehr nachgeladen: Inter,
   wenn es vorhanden ist, sonst die Systemschrift. Vor dem Start sollte Inter
   selbst ausgeliefert werden.
2. **Der Feed schob das Video weg, das man gerade ansah.** Ein Video wurde
   beim Anzeigen als „gesehen" vermerkt; bei der nächsten Aktualisierung —
   etwa nach einem Klick auf „Gefällt mir" — rutschte es damit ans Ende der
   Liste und ein anderes erschien. Jetzt wird erst beim Weiterblättern
   vermerkt.
3. **Die Erfolgsmeldung nach dem Hochladen war nie zu sehen.** Der Entwurf
   wurde sofort nach dem Absenden geleert, woraufhin der Schrittwächter auf
   Schritt 1 zurücksprang. Der Entwurf wird jetzt erst beim Verlassen geleert.
4. **Der Gericht-Dialog ließ sich nicht speichern.** Er übernahm seine
   Startwerte, während die Speisekarte noch lud — die Kategorie blieb leer und
   die Pflichtprüfung schlug zu. Die Dialoge werden jetzt erst beim Öffnen
   eingehängt.
5. **Die schwebenden Kartenknöpfe waren auf dem Handy nicht zu treffen.** Sie
   lagen unter dem Ergebnisblatt und der unteren Leiste. Ausgerechnet der
   Schalter für die reine Kartenansicht.
6. **Fehlender Text.** Auf der Gastro-Seite stand „öffnet hours.today 11:30".
   Deshalb gibt es jetzt eine Prüfung, die alle `t()`-Aufrufe gegen `de.json`
   hält — ohne Browser, in einer Sekunde.
7. **Die Trefferliste stand zweimal im Dokument.** Auf dem Handy wurde die
   Seitenspalte zwar ausgeblendet, aber trotzdem erzeugt.

## Was offen blieb

Siehe `00-produkt/OFFENE-PUNKTE.md`. Der nächste Schritt steht in
`04-naechste-schritte/SCHRITT-2-DEV-ENTFERNEN.md`.
