# Runde 8 — Desktop, echtes Design, echte Daten

**Status:** läuft. Diese Datei wächst mit, während gearbeitet wird.

## Der Auftrag, wie ich ihn verstehe

Wörtlich kam sehr viel auf einmal. Sortiert nach der Ansage
*„größte Priorität … und zweitens …"*:

### Priorität 1 — Desktop

Die Web-App hat **kein Desktop-Design**. Was am Rechner erscheint, ist das
Handy-Layout in breit, samt unterer Leiste. Gewünscht ist es „wie bei
Instagram": eine eigene Anordnung für große Bildschirme, Navigation nicht
unten, Inhalt nicht in einer Spalte in der Mitte.

Wichtiger Hinweis aus dem Auftrag: *„du hattest schon ein gutes Desktop-Design,
das hast du aber irgendwie gelöscht."* → Vor dem Neubauen im Backup-Branch
`backup/monolith-2026-09-07` nachsehen, was es gab und warum es verschwand.

### Priorität 2 — Design in Ordnung bringen

*„keine Stellen, wo Texte raustragen, Texte die in andere Felder reinkreuzen."*
Dazu Verhältnisse, die nicht stimmen. Das ist kein Feinschliff, sondern der
Grund, warum es im Moment nicht vorzeigbar ist.

Auflage dabei: **per Bild selbst prüfen.** Nicht aus dem CSS schließen, dass es
passt, sondern hinsehen.

### Alles Weitere

| Was | Anmerkung |
|---|---|
| Dev-Sachen raus aus `App`, `Website-`, `Server` | `design` **behält** seinen Dev-Modus mit Beispielen. Die anderen drei sind die Versionen, die veröffentlicht werden |
| Alles vom Handy bedienbar | Es gibt keinen Laptop. Jeder Ablauf muss über GitHub Actions gehen |
| Server über ngrok | Fester Link liegt vor. Vom Handy startbar |
| Ohne Server keine kaputten Screens | Im Moment: App zeigt Fehler, Webseite geht nicht |
| OpenStreetMap einbauen | Ohne Label, einfach die Karte |
| Echte Betriebe | Alcossebre 25 km, 77836 Rheinmünster 30 km, 77704 Oberkirch 30 km |
| Anmeldeschranke | Liken, Speichern, Bewerten, Hochladen nur angemeldet — auch auf der Webseite |
| Die drei Symbole überarbeiten | Gemeint sind die Aktionssymbole an Videos/Betrieben |
| Präsentationswebseite | Vor der Web-App, im Stil von dropship.io, OpenAI, Claude, GitHub |
| iOS-Fähigkeit | Safari-Eigenheiten, Startbildschirm |
| Konten | Admin über Namen `topic` / `admin`; `test@gastro.de` und `test@user.de`, beide `12345aA?` |
| Dieses Brain | Bei **jeder** Aktion lesen und fortschreiben. Verknüpfungen, Grafiken |

## Was ich vorab klären musste

### Der APK-Bau ist nicht mehr kaputt

Im Auftrag steht *„eine App, die ich im Moment nicht bauen kann, weil der
Workflow einen Fehler hat"*. Das stimmte — bis Runde 7. Der Fehler lag an den
Capacitor-Dateien, die absichtlich nicht im Repository liegen und die niemand
erzeugte; siehe [[07-ohne-rechner]]. Lauf 3 ist grün durchgelaufen, die APK
liegt als Artefakt bereit.

Wahrscheinlich wurde ein älterer, roter Lauf gesehen. **Zu tun:** in der
Anleitung deutlich machen, welcher Lauf der gültige ist.

### Overpass ist von hier aus gesperrt

Die echten Betriebe kommen aus OpenStreetMap. Der Netzzugang dieser Umgebung
lässt `overpass-api.de` nicht durch (Egress-Policy, 403 auf CONNECT). Von
GitHub-Runnern aus geht es.

**Folge:** Der Datenimport wird ein Workflow, der die Daten holt und einträgt.
Das passt ohnehin besser — er ist damit auch vom Handy auslösbar, und
wiederholbar, wenn die Gegenden sich ändern.

### Google Maps geht nicht, OpenStreetMap schon

Gefragt war „alle Restaurants, die du in Google Maps findest". Die Daten von
Google sind lizenzrechtlich nicht übernehmbar und ohne Bezahlkonto auch nicht
abrufbar. OpenStreetMap liefert für diese drei Gegenden dieselben Betriebe,
ist frei nutzbar (ODbL, Namensnennung genügt) — und ist ohnehin die Quelle,
die im Konzept für den Import vorgesehen war. Ich nehme also OSM.

## Zwei Dinge, die ich nicht so mache, wie es dasteht

### Der ngrok-Token kommt nicht ins Repository

Er stand im Klartext im Auftrag. Er kommt als **GitHub Secret** hinein, nicht
als Datei, nicht in eine gitignorierte Datei, nicht in den Workflow.

Und: Er sollte im ngrok-Dashboard **neu erzeugt** werden. Ein Token, der einmal
durch einen Chatverlauf gelaufen ist, ist verbrannt — wer ihn hat, kann Tunnel
auf dieses Konto aufmachen.

### Der Admin-Zugang `topic` / `admin` bleibt vorläufig

So gewünscht, so gebaut. Aber er wandert in [[SCHRITT-2-DEV-ENTFERNEN]] ganz
nach oben, mit einer harten Grenze: **Bevor die Webseite echte Nutzerdaten
sieht, muss dieser Zugang weg.** `admin` als Passwort ist in jeder Wortliste,
die es gibt.

## Reihenfolge

```mermaid
flowchart TD
    A["1 · Desktop-Layout<br/>Web-App"] --> B["2 · Überläufe und<br/>Überschneidungen"]
    B --> C["3 · Dev-Sachen raus<br/>App · Website · Server"]
    A -.prüft.-> P["Bildschirmfotos<br/>ansehen"]
    B -.prüft.-> P
    C --> D["4 · Karte + echte Betriebe"]
    D --> E["5 · Server über ngrok"]
    E --> F["6 · Präsentationswebseite"]
    F --> G["7 · iOS-Feinheiten"]

    style A fill:#fee2e2,stroke:#dc2626
    style B fill:#fee2e2,stroke:#dc2626
    style P fill:#dbeafe,stroke:#2563eb
```

Desktop und Design zuerst, weil beides an derselben Stelle sitzt und ein
zweiter Durchgang durch dieselben Dateien Verschwendung wäre. Die Dev-Sachen
danach, weil sie beim Prüfen noch nützlich sind — sobald sie weg sind, komme
ich nicht mehr in zwei Klicks in eine Gastro-Rolle.

## Verlauf dieser Runde

*(wächst mit)*

- **Aufgenommen.** Auftrag sortiert, zwei Widersprüche geklärt (APK läuft
  wieder; Google Maps → OpenStreetMap), Overpass-Sperre umgangen über einen
  Workflow. Token-Warnung ausgesprochen.
