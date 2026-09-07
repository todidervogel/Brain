# Was Website und App unterscheidet

Eine Codebasis, zwei Ziele. `platform` wird über
`Capacitor.isNativePlatform()` erkannt und lässt sich im Design-Panel
umschalten, um beides am Rechner anzusehen.

| | Website | App |
|---|---|---|
| Gastmodus | ja, aber ohne Beiträge | **nein** — ohne Anmeldung nur die Anmeldeseite |
| Untere Leiste (mobil) | Feed · Karte · Suche · Profil | zusätzlich **Aufnehmen** in der Mitte |
| Untere Leiste (Rechner) | keine, stattdessen Kopfleiste | — |
| Fußzeile | ja | nein — Rechtstexte stehen in den Einstellungen |
| Dunkelmodus | ja (seit Runde 5) | ja |
| Reine Kartenansicht | ja (mobil) | ja (mobil) |

## Warum kein Gastmodus in der App?

Ausdrücklicher Wunsch. Wer die App installiert, hat sich entschieden — dann
lohnt das Konto. Auf der Website ist das anders: Wer über eine Google-Suche
auf eine Gastro-Seite kommt, soll lesen dürfen, ohne sich anzumelden. Das ist
auch die Grundlage für die SEO-Strategie aus dem Konzept.

Praktische Folge: **Wer sich abmeldet, landet in der App zwangsläufig wieder
auf der Anmeldeseite.** Das ist so gewollt und wird vom Routenwächter
(`src/lib/auth.jsx`) durchgesetzt — jede Abmelde-Schaltfläche leitet
zusätzlich selbst dorthin.

## Was Gäste auf der Website nicht dürfen

Liken, folgen, speichern, hochladen, melden. Jede dieser Aktionen läuft durch
`useRequireLogin()` und öffnet stattdessen den Hinweis „Dafür brauchst du ein
Konto" mit den beiden Wegen Anmelden und Registrieren.

Lesen dürfen Gäste alles: Karte, Gastro-Seiten, Speisekarten, Feed,
Videodetails, fremde öffentliche Profile.

## Die reine Kartenansicht

Auf schmalen Bildschirmen liegt unten rechts auf der Karte ein Schalter.
Eingeschaltet verschwinden Kopfleiste, Banner, untere Navigation und das
Ergebnisblatt — es bleibt die Karte, ein Zurück-Knopf oben links und die
Pflichtangabe „Kartendaten © OpenStreetMap-Mitwirkende". Tippt man einen
Marker an, erscheint nur eine kleine Vorschaukarte.

Der Zustand wird gemerkt (`app-ui`), damit man nicht bei jedem Aufruf neu
umschalten muss.
