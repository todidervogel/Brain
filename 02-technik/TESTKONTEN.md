# Testkonten

> **Es gibt nur noch drei.** Seit Runde 9 sind die erfundenen Konten aus dem
> Ausgangsbestand heraus — mit den Beispieldaten, zu denen sie gehörten.
>
> **Der Verwaltungszugang `topic` mit dem Passwort `admin` ist ein
> Platzhalter, kein Passwort.** Er steht in jeder Wortliste, die es gibt. Der
> Server erinnert bei jedem Start daran, solange er gilt. **Bevor irgendetwas
> davon echte Nutzerdaten sieht, muss er weg** — siehe
> [[../04-naechste-schritte/SCHRITT-2-DEV-ENTFERNEN]].

| Rolle | Anmeldung | Passwort | Landet auf |
|---|---|---|---|
| Verwaltung | `topic` | `admin` | `/admin` |
| Gastro | `test@gastro.de` | `12345aA?` | `/gastro` |
| Nutzer | `test@user.de` | `12345aA?` | `/feed` |

Angemeldet wird mit Benutzernamen **oder** E-Mail — `findByLogin` nimmt beides.
Die Benutzernamen sind `topic`, `test_gastro` und `test_user`.

> Sie hießen zuerst `test-gastro` und `test-user`. Die Regel für
> Benutzernamen lässt keinen Bindestrich zu (`^[a-z0-9._]{3,20}$`); anmelden
> ging, aber beim ersten Speichern des Profils hätte das Formular den eigenen
> Namen zurückgewiesen. Der Rauchtest prüft den Bestand jetzt gegen die
> eigenen Regeln.

Der Gastro-Zugang hängt an einem der importierten Betriebe, damit sich der
Gastro-Bereich überhaupt ausprobieren lässt. **Für die anderen importierten
Betriebe wird kein Konto angelegt** — wer einen davon führt, meldet sich über
„Betrieb übernehmen", und dann steht am Konto auch, dass es geprüft wurde.

**Bestätigungscode bei der Registrierung:** immer `123456` — oder
**„Überspringen"**. Im MVP verschickt niemand SMS und E-Mails; eine Pflicht
zur Bestätigung wäre eine Tür ohne Schlüssel. Dass übersprungen wurde, bleibt
am Konto stehen (`verificationSkipped`).

## Was passiert mit den Daten?

**Mit Server** (`VITE_API` gesetzt oder Adresse in *Einstellungen →
Verbindung*): Alles liegt in der SQLite-Datenbank des Servers und übersteht
jeden Neustart. Passwörter sind mit scrypt gehasht; im Nutzerobjekt gibt es
kein Passwortfeld.

**Ohne Server** (Alleinbetrieb im Browser): Alles liegt im `localStorage` des
Geräts und verlässt es nie. Passwörter stehen dort im Klartext — ein Hash
schützt niemanden vor jemandem, der ohnehin dieselbe Datei lesen kann.

| Schlüssel im `localStorage` | Inhalt |
|---|---|
| `app-db` | Die gesamte Datenhaltung im Alleinbetrieb |
| `app-session` | Wer gerade angemeldet ist |
| `app-ui` | Ziel, Darstellung, Umkreis, Position, reine Kartenansicht, Banner |
| `app-upload-draft` | Der laufende Upload-Entwurf |
| `api-adresse` | Die Serveradresse, falls im Gerät eingestellt |

## Der Feed ist leer — und das stimmt so

Im Ausgangsbestand stehen die 360 Betriebe und diese drei Konten. Keine
Videos, keine Bewertungen, keine Speisekarten. So sieht jede Anwendung am
ersten Tag aus.

Wer volle Screens sehen will: `Website-/tools/pruefbestand.mjs` legt einen
Betrieb „Prüf-Trattoria" mit Speisekarte, Videos und Bewertungen an. Er wird
von den Verhaltenstests und den Bildschirmfotos eingespielt — und ist
nirgends im Programm.

## Schnellwechsel

Das Design-Panel (Schieberegler unten rechts) hat eine Zeile **Rolle** mit
den Schaltern Gast · Nutzer · Gastro · Admin. Damit springt man ohne Formular
in jede Rolle. Darunter setzt **Daten zurücksetzen** alles auf den
Auslieferungsstand zurück.

Mit Server geht dasselbe über `POST /api/reset` — aber nur angemeldet als
Verwaltung. Solange der Server nur auf dem eigenen Rechner lief, war das ein
bequemer Knopf; hinter einem ngrok-Link wäre es ein offener Knopf zum Löschen
aller Daten, den jeder findet, der die Adresse kennt.
