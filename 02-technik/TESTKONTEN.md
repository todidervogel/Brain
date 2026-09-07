# Testkonten

> **Neu in Runde 8, ausdrücklich so bestellt.** Der Verwaltungszugang `topic`
> mit dem Passwort `admin` ist ein Platzhalter, kein Passwort. Er steht in
> jeder Wortliste, die es gibt. **Bevor irgendetwas davon echte Nutzerdaten
> sieht, muss dieser Zugang weg** — siehe [[../04-naechste-schritte/SCHRITT-2-DEV-ENTFERNEN]].

| Rolle | Anmeldung | Passwort | Landet auf |
|---|---|---|---|
| Verwaltung | `topic` | `admin` | `/admin` |
| Gastro | `test@gastro.de` | `12345aA?` | `/gastro` |
| Nutzer | `test@user.de` | `12345aA?` | `/feed` |

Angemeldet wird mit Benutzernamen **oder** E-Mail — `findByLogin` nimmt beides.
Der Gastro-Zugang hängt an einem eigenen Betrieb (`p3`, Dong Xuan Imbiss) und
wird bei fremden Betrieben vom Server abgewiesen; geprüft.


**Alle Konten sind erfunden.** Sie existieren nur im Browser des Prototyps.
Niemals echte Zugangsdaten hier eintragen.

| Rolle | E-Mail | Passwort | Landet nach der Anmeldung auf |
|---|---|---|---|
| Nutzer | `max@beispiel.de` | `Passwort123` | `/feed` |
| Nutzer | `lisa@beispiel.de` | `Passwort123` | `/feed` |
| Nutzer (privates Profil) | `jonas@beispiel.de` | `Passwort123` | `/feed` |
| Gastro (eingerichtet) | `chef@trattoria-bella.de` | `Gastro123` | `/gastro` |
| Gastro (erstes Login) | `hallo@morgenrot-cafe.de` | `Start1234` | `/gastro/willkommen` — muss zuerst ein Passwort setzen |
| Admin | `ana@intern` | `Admin1234` | `/admin` |

Statt Benutzername geht auch die E-Mail und umgekehrt.

**Bestätigungscode bei der Registrierung:** immer `123456`.

## Schnellwechsel

Das Design-Panel (Schieberegler unten rechts) hat eine Zeile **Rolle** mit
den Schaltern Gast · Nutzer · Gastro · Admin. Damit springt man ohne
Formular in jede Rolle. Darunter setzt **Daten zurücksetzen** alles auf den
Auslieferungsstand zurück — praktisch, wenn man beim Ausprobieren die
Speisekarte zerlegt hat.

## Was wo gespeichert wird

| Schlüssel im `localStorage` | Inhalt |
|---|---|
| `app-db` | Die gesamte Datenhaltung (Betriebe, Videos, Bewertungen, …) |
| `app-session` | Wer gerade angemeldet ist |
| `app-ui` | Ziel, Darstellung, Umkreis, Position, reine Kartenansicht, Banner |
| `app-upload-draft` | Der laufende Upload-Entwurf |

Alles löschen: Entwicklerwerkzeuge → Anwendung → Lokaler Speicher leeren,
oder einfach „Daten zurücksetzen" im Design-Panel (setzt nur `app-db`).
