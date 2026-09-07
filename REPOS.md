# Die fünf Repositories

Alles liegt auf **`main`**. Backups älterer Stände stehen als eigene Branches
daneben (siehe unten).

| Repository | Was drin ist | Selbst starten |
|---|---|---|
| **`Server`** | Fachlogik, Datenhaltung, HTTP-API mit Rechteprüfung | `npm start` → :4000 |
| **`Website-`** | Alle Screens, Routen, Formulare, Sitzung | `npm run dev` → :5173 |
| **`App`** | Android-Hülle (Capacitor), Bauweg für die APK | `npm run build:apk` |
| **`design`** | Design-System und eine Galerie zum Ansehen | `npm run dev` → :5173 |
| **`Brain`** | Dieses Wissen: Konzept, Entscheidungen, Verlauf | zum Lesen |

## Wie sie zusammenhängen

```
   design ──────┐                Server ──────┐
   (Bausteine)  │                (Fachlogik)  │
                ▼                             ▼
            Website-  ◄── holt beides als Kopie (npm run sync)
                │
                ▼
               App  ◄── packt die gebaute Website in ein Android-Projekt
                │
                ▼
        Website- ──HTTP──► Server        (wenn VITE_API gesetzt ist)
```

**Zwei Quellen, eine Anwendung.** Das Design-System und die Fachlogik haben je
ein eigenes Repository; die Website spielt sich beide als Kopie ein und checkt
sie mit ein. Grund: `npm install && npm run dev` soll genügen — ohne zweites
Repository, ohne Netz, ohne Paketregister.

Geändert wird trotzdem nur im Original:

```bash
# im Repo Website-
npm run sync:design    # holt src/design aus dem Repo design
npm run sync:domain    # holt src/domain aus dem Repo Server
npm run sync           # beides

npm run sync:design -- --from ../design    # aus einem lokalen Ordner
```

Wer in `Website-/src/design` oder `Website-/src/domain` hineinschreibt,
verliert es beim nächsten Abgleich. Beide Ordner tragen deshalb einen Hinweis
in der README.

## Den ganzen MVP starten

```bash
# Fenster 1
cd Server && npm start

# Fenster 2
cd Website- && VITE_API=http://localhost:4000 npm run dev
```

Dann zwei Browserfenster öffnen: In einem als Gastro
(`chef@trattoria-bella.de` / `Gastro123`) ein Gericht anlegen, im anderen die
Speisekarte neu laden. Es ist da. Ohne Server geht das nicht — dort trägt
jeder Browser seine eigenen Daten.

## Branches

| Branch | Wo | Was |
|---|---|---|
| `main` | überall | der aktuelle Stand |
| `backup/monolith-2026-09-07` | überall | der Stand vor der Aufteilung |
| `claude/app-website-mvp-3w6arm` | überall | der Arbeitszweig davor, unverändert |

Im Repo `design` steht auf `backup/monolith-2026-09-07` noch der vollständige
gemeinsame Prototyp mit allen Screens, der Fachlogik und dem Android-Projekt.
Falls nach der Aufteilung etwas fehlt, liegt es dort.
