# Die fünf Repositories

| Repository | Wofür | Stand (07.09.2026) |
|---|---|---|
| `todidervogel/design` | **Der Prototyp.** Website und App in einer Codebasis (Vite + React, Capacitor für Android). Enthält inzwischen nicht nur den Entwurf, sondern die vollständige MVP-Logik. | Aktiv, alle Arbeit passiert hier |
| `todidervogel/Brain` | **Dieses Repository.** Wissen, Kontext, Entscheidungen, Verlauf. | Aktiv |
| `todidervogel/App` | Für die spätere native App (React Native + Expo, ab MVP 1 laut Konzept). | Leer, nur README |
| `todidervogel/Server` | Für das Backend (Supabase-Migrationen, Edge Functions, OSM-Import). | Leer, nur README |
| `todidervogel/Website-` | Für die spätere Next.js-Website mit serverseitigem Rendern (SEO der Gastro-Seiten). | Leer, nur README |

## Warum liegt alles in `design`?

Weil es für den Test das Richtige ist: eine Codebasis, ein Build, ein APK,
eine Website. Die Trennung in `App` / `Server` / `Website-` folgt dem
Zielbild aus dem Konzept (Abschnitt 4) und wird erst gebraucht, wenn

- die Daten wirklich auf einem Server liegen (dann: `Server`),
- die Gastro-Seiten von Google gefunden werden müssen (dann: `Website-`,
  Next.js mit serverseitigem Rendern),
- die App echte Kamera, GPS und Push braucht (dann: `App`, React Native).

Bis dahin wäre eine Aufteilung dreifache Arbeit am selben Entwurf.

**Arbeitsbranch in allen Repositories:** `claude/app-website-mvp-3w6arm`
