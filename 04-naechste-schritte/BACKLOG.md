# Was danach kommt

Reihenfolge aus dem Konzept (Abschnitt 13), angepasst an den heutigen Stand.

## Schritt 2, Aufräumen
Siehe `SCHRITT-2-DEV-ENTFERNEN.md`.

## Schritt 3. Der Server

Das ist der große Sprung. Die Fassade (`src/lib/store/api.js`) ist genau
dafür gebaut: Sie wird ersetzt, die Screens bleiben.

1. **Supabase-Projekt** anlegen, Umgebungsvariablen setzen.
2. **Migrationen** für alle Tabellen aus `02-technik/DATENMODELL.md`. PostGIS
   aktivieren, räumlichen Index auf `venues.location` nicht vergessen.
3. **RLS-Policies** aus Konzept Abschnitt 7, *vor allem anderen*, sonst
   baut man später unsicher weiter. Die Regeln stehen im Prototyp schon in
   `src/lib/auth.jsx`, aber im Frontend. Sie müssen als SQL-Policies
   nachgezogen werden, nicht nur nachgebaut.
4. **OSM-Import** über die Overpass API. Erst eine Stadt, dann Deutschland.
   Attributionshinweis „© OpenStreetMap-Mitwirkende" ist Pflicht und im
   Prototyp bereits überall vorhanden.
5. **`api.js` umschreiben**, Funktion für Funktion, gegen dieselben
   Signaturen.

## Schritt 4. Was der Prototyp nicht kann

| Thema | Braucht |
|---|---|
| Echte Karte | MapLibre GL + Kacheln (Protomaps oder MapTiler Free) |
| Echte Videos | Capacitor-Kamera, Cloudflare Stream |
| Echtes GPS | Capacitor-Geolocation, Standortfreigabe |
| E-Mail und SMS | Resend/Postmark, ein SMS-Anbieter |
| Bilder | Supabase Storage oder Cloudflare Images |
| Fehlerüberwachung | Sentry Free-Tier |
| Schrift Inter | Selbst ausliefern (woff2 im Projekt, `@font-face`). Nicht von Google laden, Datenschutz und Ladezeit, siehe Kommentar in `index.html` |

## Schritt 5. Vor dem Start

- Rechtstexte prüfen lassen (Konzept 10).
- Produktname festlegen, Domain sichern, Impressum ausfüllen.
- Gastro-Ansprache rechtlich klären (§ 7 UWG).
- Video-Limits scharf schalten: 60 Sek. 100 MB, 10 Uploads pro Tag,
  Transkodierung auf 1080p (Konzept 11).

## Später, Zielbild aus dem Konzept

- `Website-`: Next.js mit serverseitigem Rendern für die SEO der
  Gastro-Seiten. **Das ist der Grund, warum die Gastro-Seiten überhaupt
  öffentlich sind**, ohne Google-Indexierung ist die Karte von Tag 1 leer.
- `App`: React Native + Expo, gemeinsame Logik über ein Monorepo.
- `Server`: Migrationen, Edge Functions, OSM-Import als eigenes Repository.

## Vorschläge, die sich beim Bauen aufgedrängt haben

Nicht beauftragt, nur notiert:

- **Speisekarte in mehreren Sprachen.** Die Struktur ist schon da; für
  Touristengegenden wäre das der offensichtliche nächste Schritt.
- **Tagesangebot.** Der Schalter „heute verfügbar" existiert bereits, eine
  Mittagskarte mit Datum wäre die kleine Erweiterung.
- **Fotos zu Gerichten.** Die Karte hat den Platz vorgesehen, es fehlt nur
  der Speicher.
- **Angebot aus der Speisekarte ableiten.** Wer nur vegane Gerichte
  einträgt, könnte automatisch als vegan markiert werden, als Vorschlag,
  nicht als Zwang.
