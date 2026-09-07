# Alle Screens

57 Routen. Die Tabelle in `design/src/routes/index.js` ist die einzige Quelle
— sowohl für den Router als auch für die Übersichtsseite unter `/uebersicht`.

## Öffentlich (TEIL C)

| Route | Screen | Logik seit Runde 5 |
|---|---|---|
| `/` | Startseite | Ortssuche verschiebt den Kartenmittelpunkt; Videoreihe und Kartenvorschau aus echten Daten |
| `/karte` | Kartenansicht | Alle Filter filtern wirklich; Marker aus echten Koordinaten; **reine Kartenansicht**; Angebotsfilter |
| `/feed` | Video-Feed | Umkreislogik nach Konzept 8.4, blättern, liken, speichern, folgen, Gesehenes ans Ende |
| `/suche` | Suchergebnisse | Vier Reiter mit echter Suche, Trefferzahlen, Verlauf, Filter |
| `/g/[slug]` | Gastro-Seite | Echte Daten, Angebot ganz oben, Reiter Videos/Speisekarte/Bewertungen/Infos, Speichern, Melden |
| **`/g/[slug]/speisekarte`** | **Speisekarte** | **Neu** — eigene Seite ohne App-Rahmen |
| `/v/[id]` | Videodetail | Echte Daten, Gefällt mir, Speichern, Folgen, Melden |
| `/p/[username]` | Fremdes Profil | Echte Daten, private Profile bleiben verschlossen |
| `/fuer-gastronomen` | Landingpage | — |
| `/gastro/eintragen` | Betrieb eintragen | Sucht echte Betriebe, setzt `claimStatus='pending'`, legt eine Einladung an |

## Konto (TEIL D)

`/registrieren` · `/registrieren/code` · `/anmelden` · `/passwort-vergessen` ·
`/passwort-neu` — alle mit echter Prüfung, echten Fehlermeldungen und
rollenabhängiger Weiterleitung.

## Nutzer (TEIL E)

`/upload` → `/upload/bearbeiten` → `/upload/restaurant` →
`/upload/bewertung` → `/upload/veroeffentlichen` — ein Assistent mit
gemeinsamem Entwurf und Schrittwächter. Am Ende entstehen wirklich ein Video
(Status „in Prüfung") und, wenn ausgefüllt, eine Bewertung.

`/profil` · `/p/[username]` · `/benachrichtigungen` · `/einstellungen` ·
`/einstellungen/profil` · `/einstellungen/daten`

## Gastro (TEIL F)

`/gastro/anmelden` · `/gastro/willkommen` · `/gastro/einrichtung` ·
`/gastro` · `/gastro/videos` · **`/gastro/speisekarte`** ·
`/gastro/bewertungen` · `/gastro/profil` · `/gastro/qr` ·
`/gastro/einstellungen`

Alles hängt am angemeldeten Gastro-Konto. Admins dürfen ebenfalls hinein und
sehen dann den ersten Betrieb — praktisch für die Prüfung.

## Admin (TEIL G)

`/admin` · `/admin/videos` · `/admin/meldungen` · `/admin/betriebe` ·
`/admin/einladungen` · `/admin/nutzer` · `/admin/vorschlaege` ·
`/admin/protokoll`

Alle Aktionen wirken auf die Daten und werden im Protokoll vermerkt.

## Rechtliches (TEIL H) und Fehler (TEIL I)

`/impressum` · `/datenschutz` · `/agb` · `/agb-gastro` · `/richtlinien` ·
`/cookies` · `/404` · `/500` · `/offline` · `/403`

Die Rechtstexte sind Platzhalter mit deutlichem Hinweis. Vor dem
Nutzer-Launch muss dort ein Anwalt für IT-Recht drübersehen (Konzept 10).

## Werkzeug

`/uebersicht` — listet alle Routen mit ihrem Abschnitt in der Spezifikation.
Fällt in Schritt 2 weg.
