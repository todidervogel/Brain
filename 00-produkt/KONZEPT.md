# Projektkonzept, Food-Discovery-App (Arbeitstitel: "FoodFeed")

> **Hinweis an Claude Code:** Dieses Dokument ist die Produktspezifikation. Der endgültige Produkt-/Firmenname steht noch nicht fest. Verwende überall die Konstante `APP_NAME` aus einer zentralen Config-Datei, damit der Name später an einer Stelle geändert werden kann.

---

## 1. Vision

Eine App, die Restaurants, Bars und Cafés über einen **Kurzvideo-Feed im Hochformat** entdeckbar macht, kombiniert mit einer **Karte**, **strukturierten Bewertungen pro Gericht** und **vollständigen Gastro-Profilseiten** (Speisekarte, Öffnungszeiten, Kontakt, später Bestellung).

**Kernproblem:** Wer essen gehen will, muss heute zwischen Google Maps (Infos, aber keine echten Eindrücke), TikTok/Instagram (Eindrücke, aber unstrukturiert und nicht lokal filterbar) und Lieferdiensten (nur Lieferung) hin- und herspringen.

**Lösung:** Alles an einem Ort, man *sieht*, wie das Essen aussieht, sieht wo es ist, sieht was Freunde davon halten, und kann direkt bestellen.

**Startmarkt:** Deutschland (Sprache DE). Danach USA (EN). Architektur muss von Anfang an mehrsprachig und mehrwährungsfähig sein (i18n ab Tag 1, auch wenn nur DE befüllt).

---

## 2. Wettbewerbsanalyse (Stand 09/2026)

| Wettbewerber | Was sie machen | Wo unsere Lücke ist |
|---|---|---|
| **TikTok „In der Nähe"-Feed** | Lokaler Kurzvideo-Feed, in DE/FR/IT/UK ausgerollt | Keine strukturierten Gastro-Daten, keine Speisekarte, keine Bestellung, kein Bewertungssystem |
| **Uber Eats Video-Feed** | Kurzvideos von Restaurants im Bestellprozess | Nur Lieferung, nur Partner-Restaurants, kein User-Content |
| **Beli** (USA) | Gamifiziertes Restaurant-Ranking mit Freundes-Feed | Fotobasiert, kein Video-Feed, keine Bestellung, nicht in DE |
| **CYZL** | Restaurants/Bars mit Echtzeit-Fotos/Videos + Freundesprofile | Klein, USA-fokussiert, keine Gastro-Seiten |
| **Google Maps** | Vollständige Daten, Fotos, Bewertungen | Keine Video-Discovery, kein Feed-Erlebnis |
| **SwipeEat** | Standortbasierte Restaurantsuche mit Swipe | Sehr klein, nur Fotos |

**Unser Alleinstellungsmerkmal:**
1. Video-Feed **+** vollständige Gastro-Seite **+** Bestellung in *einer* App, unabhängig von Lieferdiensten
2. **Bewertung pro Gericht** (nicht pro Restaurant), wesentlich nützlicher als „4,2 Sterne"
3. **Automatisch generierte Gastro-Seiten** aus offenen Daten, die Karte ist von Tag 1 voll, auch ohne dass Gastros mitmachen

**Realistische Risiken (bitte im Hinterkopf behalten):**
- Henne-Ei-Problem: Ohne Videos kein Nutzen, ohne Nutzer keine Videos → deshalb die Gastro-Pilot-Phase (MVP 0)
- TikTok kann diese Funktion jederzeit besser bauen → wir müssen über die *Tiefe* der Gastro-Daten gewinnen, nicht über den Feed
- Video-Hosting ist der größte Kostenblock → strikte Limits von Anfang an

---

## 3. Phasenplan

### MVP 0, Gastro-Pilot (Web-App only)
**Ziel:** Inhalt aufbauen, bevor Nutzer kommen.

- Karte mit Restaurants aus OpenStreetMap-Daten (Deutschland)
- Automatisch generierte Gastro-Seiten (Name, Adresse, Öffnungszeiten, Kategorie)
- **Gastro-Login** (Zugangsdaten werden von uns per E-Mail vergeben)
- Gastros können: Videos hochladen, Profil bearbeiten, Beschreibung/Bilder ergänzen
- **Normale Besucher** können ohne Account: Karte ansehen, Gastro-Seiten ansehen, Videos ansehen
- Hinweisbanner: „App befindet sich im Aufbau"
- **QR-Code-Funktion:** Gastros können QR-Codes ausdrucken/aufhängen → Gäste landen auf der Gastro-Seite und können sich als Test-Nutzer registrieren (mit Video-Upload + Bewertung)
- Admin-Oberfläche: Gastro-Accounts anlegen, Videos moderieren, Meldungen bearbeiten

### MVP 1, Nutzer-Launch (Web-App + Android)
- Nutzer-Registrierung (E-Mail + Handynummer)
- Video-Upload durch Nutzer mit **Pflicht-Bewertungsdialog**
- Vertikaler Video-Feed mit Umkreis-Logik (Radius einstellbar)
- Nutzerprofile (**standardmäßig privat**, umstellbar auf öffentlich)
- Suche: nach Gericht, Restaurantname, Adresse/Ort, Nutzerprofil
- Meldefunktion + Moderationsprozess
- Vollständige DSGVO-Funktionen (Export, Löschung, Einwilligungen)

### MVP 2, Social & Bestellung
- Freunde/Follower (Kontaktabgleich, Username-Suche)
- Freundes-Feed („was essen meine Freunde")
- Bestellsystem (Abholung; Lieferung wenn von Gastro freigeschaltet)
- Zahlung via Stripe Connect, inkl. Trinkgeld-Auswahl und Plattformgebühr
- iOS-App

### Phase 3+ (nicht spezifizieren, nur Architektur offenhalten)
- Personalisierter Algorithmus (Vorlieben statt reiner Umkreis)
- KI-Moderation der Videos
- Musik, Filter, Text-Overlays im Editor
- Offline-Download von Videos + Gastro-Seiten
- Gastro-Verifizierung per Post/Gewerbenachweis
- Modus für Nicht-Gastro-Geschäfte
- Werbeplätze im Feed
- Öffentliche API für Agenten
- Anbindung an bestehende Lieferdienste

---

## 4. Tech-Stack (verbindlich)

| Bereich | Technologie | Begründung |
|---|---|---|
| Frontend Web | **Next.js 15** (App Router, TypeScript) | Serverseitiges Rendering für SEO der Gastro-Seiten, kritisch, damit Google die Seiten findet |
| Frontend Mobile | **React Native + Expo** (ab MVP 1) | Eine Codebasis für Android + iOS, teilt Logik mit Web |
| Shared Code | Monorepo (pnpm workspaces oder Turborepo) mit `packages/shared` für Typen, API-Client, Validierung | Kein doppelter Code |
| Backend / DB | **Supabase** (PostgreSQL + PostGIS, Auth, Storage, Row Level Security, Edge Functions) | Login, DB, Dateispeicher und Rechteverwaltung fertig, als Einzelentwickler nicht selbst zu bauen |
| Geodaten | **PostGIS** in Supabase für Umkreissuche | Umkreissuche direkt in der DB, kein externer Dienst |
| Karte | **MapLibre GL** + OpenStreetMap-Tiles (Anbieter: Protomaps self-hosted oder MapTiler Free-Tier) | Kostenlos, keine Lizenzprobleme |
| Restaurant-Daten | **OpenStreetMap** via Overpass API, einmalig importiert | Siehe Abschnitt 5, rechtlich sauber, im Gegensatz zu Google |
| Video-Hosting | **Cloudflare Stream** (ab MVP 1), im MVP 0 Supabase Storage | Transkodierung + adaptives Streaming inklusive |
| Zahlung (MVP 2) | **Stripe Connect** (Express-Accounts) | Kann Plattformgebühr + Trinkgeld automatisch aufteilen |
| E-Mail | Resend oder Postmark | Für Gastro-Einladungen |
| Hosting | Vercel (Frontend) + Supabase Cloud | Startet im Gratis-/Hobby-Tarif |
| Monitoring | Sentry (Free-Tier) | Fehler sehen, bevor Nutzer sie melden |

**Wichtig:** Alle externen Dienste hinter einer eigenen Abstraktionsschicht (`packages/shared/services/`) kapseln, damit ein Wechsel (z.B. Supabase → eigener Server) später ohne Umbau des ganzen Codes möglich ist. Das war eine ausdrückliche Anforderung.

---

## 5. Datenquelle Restaurants, WICHTIG

**Google Places darf NICHT als Datenbasis verwendet werden.** Die Nutzungsbedingungen verbieten das dauerhafte Speichern von Ortsdaten (außer der Place-ID) und die Anzeige auf Nicht-Google-Karten. Ein Verstoß ist ein Sperrgrund und ein rechtliches Risiko.

**Stattdessen:**
1. **Basis:** OpenStreetMap-Daten via Overpass API importieren.
   - Filter: `amenity=restaurant|cafe|fast_food|bar|pub|ice_cream`, `shop=bakery|deli`
   - Felder: Name, Koordinaten, Adresse, `opening_hours`, `cuisine`, `phone`, `website`, `wheelchair`
   - Lizenz: ODbL, **Attributionshinweis „© OpenStreetMap-Mitwirkende" ist auf jeder Karte Pflicht**
2. **Aktualisierung:** wöchentlicher Cron-Job, der Änderungen nachzieht
3. **Ergänzung:** Gastros können ihre eigenen Daten nach Übernahme des Profils korrigieren, diese Korrekturen werden in einer eigenen Tabelle gespeichert und überschreiben die OSM-Daten in der Anzeige (OSM-Daten bleiben unverändert erhalten)

---

## 6. Datenmodell (PostgreSQL / Supabase)

```
-- Nutzer
profiles
  id                uuid PK (= auth.users.id)
  username          text UNIQUE
  display_name      text
  avatar_url        text
  bio               text
  is_private        boolean DEFAULT true      -- Standard: privat
  role              enum('user','gastro','admin')
  created_at        timestamptz
  deleted_at        timestamptz               -- Soft Delete

-- Gastronomiebetriebe
venues
  id                uuid PK
  osm_id            text UNIQUE               -- Herkunft OSM
  name              text
  slug              text UNIQUE               -- für SEO-URLs
  location          geography(Point,4326)     -- PostGIS
  address_street    text
  address_city      text
  address_zip       text
  address_country   text DEFAULT 'DE'
  category          text[]                    -- z.B. {italian, pizza}
  opening_hours     jsonb
  phone             text
  website           text
  description       text
  cover_image_url   text
  claim_status      enum('unclaimed','pending','verified')
  claimed_by        uuid FK -> profiles
  status            enum('active','closed_reported','closing','archived')
  closing_since     timestamptz               -- Start der 1-Jahres-Frist
  created_at        timestamptz

-- Gerichte (für Bewertung pro Gericht)
dishes
  id                uuid PK
  venue_id          uuid FK -> venues
  name              text
  price_cents       integer
  description       text
  is_verified       boolean                   -- von der Gastro bestätigt
  created_by        uuid FK -> profiles

-- Videos
videos
  id                uuid PK
  author_id         uuid FK -> profiles
  venue_id          uuid FK -> venues NOT NULL   -- MVP: Pflicht
  author_type       enum('user','gastro')
  stream_uid        text                      -- Cloudflare Stream ID
  thumbnail_url     text
  duration_seconds  integer                   -- max 60 im MVP
  caption           text
  is_location_verified boolean DEFAULT false  -- GPS beim Upload am Ort?
  visibility        enum('public','friends','private')
  status            enum('processing','pending_review','published','rejected','hidden')
  view_count        integer DEFAULT 0
  created_at        timestamptz
  deleted_at        timestamptz

-- Bewertungen (an ein Video gekoppelt, alle Felder optional)
reviews
  id                uuid PK
  video_id          uuid FK -> videos UNIQUE
  author_id         uuid FK -> profiles
  venue_id          uuid FK -> venues
  rating_food       smallint NULL CHECK (1..5)
  rating_service    smallint NULL CHECK (1..5)
  rating_price      smallint NULL CHECK (1..5)
  was_hot           boolean NULL              -- "Essen war heiß serviert"
  group_size        smallint NULL
  text              text NULL
  created_at        timestamptz

-- Einzelne Gerichte innerhalb einer Bewertung
review_dishes
  id                uuid PK
  review_id         uuid FK -> reviews
  dish_id           uuid FK -> dishes NULL
  dish_name_raw     text                      -- falls Gericht nicht in DB
  rating            smallint NULL CHECK (1..5)

-- Soziales (ab MVP 2, Tabellen aber früh anlegen)
follows
  follower_id       uuid FK -> profiles
  following_id      uuid FK -> profiles
  status            enum('pending','accepted')   -- pending bei privaten Profilen
  created_at        timestamptz
  PRIMARY KEY (follower_id, following_id)

likes
  user_id           uuid FK -> profiles
  video_id          uuid FK -> videos
  PRIMARY KEY (user_id, video_id)

-- Moderation
reports
  id                uuid PK
  reporter_id       uuid FK -> profiles
  target_type       enum('video','review','profile','venue')
  target_id         uuid
  reason            enum('spam','offensive','fake','wrong_info','venue_closed','other')
  note              text
  status            enum('open','in_review','resolved','dismissed')
  handled_by        uuid FK -> profiles
  created_at        timestamptz

-- Gastro-Einladungen (MVP 0)
venue_invites
  id                uuid PK
  venue_id          uuid FK -> venues
  email             text
  token             text UNIQUE
  status            enum('sent','opened','activated','bounced','declined')
  sent_at           timestamptz
  activated_at      timestamptz
```

**Aggregat-Werte** (Durchschnittsbewertung pro Venue/Dish) über eine **Materialized View** oder Trigger berechnen, nicht bei jedem Seitenaufruf neu.

---

## 7. Row Level Security (Supabase), Grundregeln

Diese Regeln sind sicherheitskritisch und müssen als SQL-Policies umgesetzt werden, nicht nur im Frontend:

- `profiles`: jeder darf öffentliche Profile lesen; private Profile nur der Besitzer und akzeptierte Follower
- `videos`: `visibility='public'` + `status='published'` für alle lesbar; `visibility='friends'` nur für akzeptierte Follower; eigene Videos immer
- `videos` INSERT: nur eingeloggte Nutzer, `author_id` muss `auth.uid()` sein
- `venues` UPDATE: nur `claimed_by` bei `claim_status='verified'`, oder `role='admin'`
- `reviews`: lesbar wenn das zugehörige Video lesbar ist
- `reports`: einfügbar von allen eingeloggten Nutzern, lesbar nur von Admins
- Admin-Rolle über eine Custom Claim im JWT, nicht über eine Spalte allein prüfbar

---

## 8. Feature-Spezifikationen

### 8.1 Karte (MVP 0)
- Vollbildkarte, Marker für alle Venues im Viewport
- Marker-Cluster ab hoher Dichte
- Marker zeigen Kategorie-Icon; Venues **mit Videos** werden hervorgehoben (z.B. farbiger Ring)
- Tap auf Marker → Vorschaukarte unten (Name, Kategorie, Ø-Bewertung, Anzahl Videos, Thumbnail)
- Standortermittlung per GPS, mit manueller Ortseingabe als Alternative
- Pflicht: OSM-Attributionshinweis sichtbar

### 8.2 Gastro-Seite (MVP 0)
Öffentlich erreichbar unter `/g/[slug]`, serverseitig gerendert für Google-Indexierung.
- Kopfbereich: Name, Kategorie, Ø-Bewertung (Essen/Service/Preis getrennt), Adresse, Öffnungszeiten (mit „jetzt geöffnet/geschlossen"), Telefon, Website
- Verifizierungs-Badge falls `claim_status='verified'`
- Video-Raster: alle Videos zu diesem Venue
- Gerichte-Liste mit Einzelbewertungen (sobald vorhanden)
- Buttons: Route (öffnet Karten-App), Anrufen, Teilen
- Melde-Button („Infos falsch" / „dauerhaft geschlossen")
- Deutlicher Hinweis, wenn Daten automatisch generiert sind und noch nicht von der Gastro bestätigt wurden

### 8.3 Gastro-Bereich (MVP 0)
Login mit von uns vergebenen Zugangsdaten, Passwortwechsel beim ersten Login erzwingen.
- Dashboard: eigene Videos, Aufrufe, eingegangene Bewertungen
- Video-Upload (Drag & Drop, max. 60 Sek.)
- Profil bearbeiten: Beschreibung, Titelbild, Öffnungszeiten korrigieren, Kategorien, Gerichte anlegen
- QR-Code-Generator: erzeugt PDF zum Ausdrucken, führt auf die Gastro-Seite mit `?src=qr`
- Account-Schließung beantragen (startet die 1-Jahres-Frist, siehe 8.7)

### 8.4 Video-Feed (MVP 1)
- Vertikaler Vollbild-Feed, Wischen nach oben für nächstes Video
- Overlay: Autor (Avatar + Username), Venue-Name (tappbar → Gastro-Seite), Entfernung („1,2 km"), Sternebewertung, Caption
- Aktionen: Like, Kommentar (ab MVP 2), Teilen, Speichern
- **Feed-Logik MVP 1 (rein umkreisbasiert):**
  1. Alle veröffentlichten Videos im eingestellten Radius (Standard 5 km, einstellbar 1/5/10/25/50 km)
  2. Sortierung: gemischt aus Aktualität und Bewertungsvollständigkeit
     - Videos **mit** ausgefüllter Bewertung: Bonus-Gewichtung
     - Videos mit `is_location_verified=true`: Bonus-Gewichtung
     - Leichte Zufallskomponente, damit nicht immer dieselben oben stehen
  3. Bereits gesehene Videos ans Ende
- **Kaltstart:** Wenn im Radius weniger als 5 Videos → Radius automatisch schrittweise erweitern, plus Hinweis „Wenig Inhalte in deiner Nähe" mit Link zur Karte

### 8.5 Video-Upload + Bewertungsdialog (MVP 1)
Ablauf:
1. Video aufnehmen (in-App) **oder** aus Galerie wählen
2. Max. 60 Sekunden (MVP), Hochformat 9:16, Zuschneiden möglich
3. **Venue-Auswahl (Pflicht):** Liste der nächstgelegenen Venues per GPS, mit Suchfeld. Nutzer können **kein** neues Venue anlegen, falls nicht vorhanden: Meldung „Restaurant fehlt" an Admin
4. Wenn GPS-Position beim Upload im Umkreis von 150 m des Venues → `is_location_verified=true`
5. **Bewertungsdialog (Durchlaufen ist Pflicht, Ausfüllen nicht):**
   - Essen: 1–5 Sterne (überspringbar)
   - Service: 1–5 Sterne (überspringbar)
   - Preis-Leistung: 1–5 Sterne (überspringbar)
   - „War das Essen heiß?" Ja/Nein/Egal
   - „Zu wievielt wart ihr?" (Zahl, optional)
   - „Was habt ihr bestellt?", Gerichte aus der Venue-Liste wählen oder frei eingeben, je Gericht optional 1–5 Sterne
   - Freitext (optional)
   - Sichtbarer Hinweis: *„Videos mit Bewertung werden im Feed deutlich häufiger angezeigt."*
6. Sichtbarkeit wählen: öffentlich / nur Freunde / privat
7. Upload → `status='pending_review'` (im MVP 0/1 manuelle Freigabe durch Admin)

### 8.6 Nutzerprofil (MVP 1)
- **Standardmäßig privat.** Private Profile: nur Username, Avatar und Anzahl Videos sichtbar; Inhalte erst nach akzeptierter Follow-Anfrage
- Öffentlich schaltbar in den Einstellungen
- Anzeige: Videos (Raster), abgegebene Bewertungen, besuchte Restaurants
- Einstellungen: Profil, Sichtbarkeit, Radius, Sprache, Benachrichtigungen, Datenexport, Konto löschen

### 8.7 Restaurant-Schließungen (wichtige Sonderlogik)
- **Gastro schließt selbst:** `status='closing'`, `closing_since=now()`. Profil bleibt 12 Monate sichtbar mit Hinweis „Dauerhaft geschlossen". Danach `status='archived'` → für Nutzer nicht mehr sichtbar, Videos bleiben in der Datenbank erhalten (nicht gelöscht)
- **Nutzer meldet Schließung:** `reports` mit `reason='venue_closed'`. Ab 3 unabhängigen Meldungen → `status='closed_reported'`, Warnhinweis auf der Seite, Admin-Aufgabe zur Prüfung. Prüfung: OSM-Status, Google, Telefonanruf, ggf. Vor-Ort-Kontrolle
- Archivierte Venues bleiben unter ihrer URL erreichbar (HTTP 410 oder Hinweisseite), damit keine toten Links entstehen

### 8.8 Suche (MVP 1)
Ein Suchfeld mit Reitern:
- **Gerichte**, z.B. „Ramen" → Venues in der Nähe, die das anbieten, sortiert nach Gericht-Bewertung
- **Restaurants**, Name
- **Orte**, Stadt/Adresse → verschiebt Karte und Feed-Zentrum (für Reiseplanung, ohne physisch dort zu sein)
- **Profile**, Username
Umsetzung: PostgreSQL Full-Text-Search (`tsvector`), kein externer Suchdienst nötig.

### 8.9 Moderation & Meldungen (MVP 0)
- Melde-Button an Videos, Bewertungen, Profilen, Venues
- Admin-Oberfläche: offene Meldungen, Video-Freigabewarteschlange, Gastro-Accounts anlegen, Nutzer sperren
- **MVP 0/1: manuelle Freigabe** aller Videos vor Veröffentlichung
- Ab Phase 3: KI-Vorprüfung, manuell nur bei Auffälligkeit

---

## 9. Design & UX

**Grundhaltung:** professionell und ruhig, nicht verspielt. Das Essen ist bunt, die Oberfläche darf es nicht sein.

- **Feed:** dunkles Interface (Videos wirken auf Schwarz besser), Bedienelemente als Overlay, minimal
- **Karte und Gastro-Seiten:** helles Interface, klar strukturiert, gut lesbar, näher an Google Maps als an TikTok
- **Ein Akzentfarbton**, sparsam eingesetzt (Aktionen, aktive Zustände). Keine Farbverläufe, keine dekorativen Effekte
- **Typografie:** eine gut lesbare, neutrale Schrift (z.B. Inter). Klare Größenhierarchie
- **Navigation (Mobile):** untere Leiste mit vier Punkten, Feed, Karte, Upload (mittig, hervorgehoben), Profil. Suche als Symbol im Kopfbereich
- **Sterne-Darstellung:** immer die drei Werte getrennt zeigen (Essen / Service / Preis), nie zu einer Zahl zusammenfassen, das ist ein bewusstes Unterscheidungsmerkmal
- **Barrierefreiheit:** ausreichende Kontraste (WCAG AA), Untertitel-Option für Videos später einplanen
- **Ladeverhalten:** Videos vorladen (nächstes Video im Feed vorpuffern), Skelettansichten statt Ladekreisel

---

## 10. Rechtliches & DSGVO (ab MVP 0 einzuplanen)

Nicht optional, bei einer Plattform mit Nutzervideos, Standortdaten und Bewertungen ist das Haftungsrisiko real.

- **Einwilligungen** beim Registrieren: AGB, Datenschutzerklärung, separat für Standortverarbeitung
- **Cookie-/Tracking-Banner** (TTDSG), im MVP nur technisch notwendige Cookies, dann ist das Banner minimal
- **Auskunft & Export** (Art. 15/20): Nutzer kann alle eigenen Daten als JSON exportieren
- **Löschung** (Art. 17): Konto löschen → Profil und Videos werden entfernt; Bewertungen werden anonymisiert (nicht gelöscht, sonst verfälschen sich Durchschnittswerte, das ist zulässig, muss aber in der Datenschutzerklärung stehen)
- **Impressumspflicht** (§ 5 DDG), auch in der App erreichbar
- **Bewertungen:** Verdachtsmomente auf Fake-Bewertungen ernst nehmen. Gastros brauchen ein Widerspruchsverfahren gegen einzelne Bewertungen (Gegendarstellung + Prüfung durch uns). BGH-Rechtsprechung verlangt von Bewertungsportalen eine Prüfpflicht bei substantiiertem Widerspruch
- **Urheberrecht Videos:** Nutzer räumen in den AGB ein einfaches Nutzungsrecht ein; keine Musik im MVP (deshalb erst später, GEMA/Lizenzfragen)
- **Recht am eigenen Bild:** Hinweis beim Upload, dass erkennbare Personen zustimmen müssen
- **Minderjährige:** Altersgrenze 16 (DSGVO Art. 8 in DE), Abfrage bei Registrierung
- **DSA (Digital Services Act):** Als Hosting-Dienst brauchst du ab Start ein Melde- und Abhilfeverfahren (haben wir), einen Kontaktpunkt und Begründungspflicht bei Sperrungen

**Empfehlung:** Vor dem Nutzer-Launch (MVP 1) einmal einen Anwalt für IT-Recht über AGB und Datenschutzerklärung schauen lassen. Das ist die eine Ausgabe, an der man nicht sparen sollte.

---

## 11. Kostenrahmen

**MVP 0 (Ziel: unter 20 €/Monat)**

| Posten | Kosten |
|---|---|
| Supabase Free-Tier (500 MB DB, 1 GB Storage) | 0 € |
| Vercel Hobby | 0 € |
| Domain | ~1 €/Monat |
| E-Mail-Versand (Resend Free: 3.000/Monat) | 0 € |
| Karten-Tiles (MapTiler Free / Protomaps) | 0 € |
| Sentry Free | 0 € |
| **Summe** | **~1–5 €/Monat** |

Der Free-Tier von Supabase reicht für den Gastro-Pilot mit wenigen hundert Videos nicht lange (1 GB Storage). Sobald das eng wird: Supabase Pro (25 $/Monat) **oder** Videos auf Cloudflare Stream auslagern (5 $ pro 1.000 gespeicherte Minuten + 1 $ pro 1.000 gestreamte Minuten). Cloudflare Stream ist bei Video fast immer die günstigere und bessere Wahl, deshalb ab MVP 1 einplanen.

**Harte Video-Limits von Anfang an** (sonst explodieren die Kosten):
- max. 60 Sek. max. 100 MB pro Upload
- max. 10 Uploads pro Nutzer pro Tag
- Transkodierung auf max. 1080p

---

## 12. Monetarisierung (ab MVP 2, Architektur vorbereiten)

1. **Provision auf In-App-Bestellungen**, z.B. 5–8 % vom Bestellwert (über Stripe Connect `application_fee_amount`)
2. **Trinkgeld**, geht zu 100 % an die Gastro, keine Plattformgebühr darauf (wichtig für Akzeptanz und rechtlich sauberer)
3. **Bezahlte Feed-Platzierung**, Gastros zahlen für höhere Sichtbarkeit. **Muss als Werbung gekennzeichnet sein** (UWG)
4. **Gastro-Abo**, erweiterte Statistiken, mehrere Standorte, Prioritäts-Support
5. **Klassische Werbung**, erst bei relevanter Nutzerzahl sinnvoll

---

## 13. Empfohlene Umsetzungsreihenfolge für Claude Code

Arbeite diese Schritte der Reihe nach ab. Nach jedem Schritt lauffähiger Zustand.

1. **Setup:** Monorepo, Next.js, TypeScript, Tailwind, Supabase-Projekt, Umgebungsvariablen, `APP_NAME`-Config
2. **Datenbank:** Alle Tabellen aus Abschnitt 6 als Migrationen, PostGIS aktivieren, Indizes (räumlicher Index auf `venues.location`!)
3. **RLS-Policies** aus Abschnitt 7, vor allem anderen, sonst baut man später unsicher weiter
4. **OSM-Import:** Skript, das Overpass abfragt und `venues` befüllt. Erst mit einer Stadt testen, dann Deutschland
5. **Karte:** MapLibre-Ansicht mit Markern aus der DB, Umkreisabfrage per PostGIS
6. **Gastro-Seiten:** SSR-Seite `/g/[slug]`, SEO-Metadaten, Öffnungszeiten-Logik
7. **Auth:** Supabase Auth, Rollen, Gastro-Login mit Passwortwechsel-Zwang
8. **Gastro-Dashboard:** Video-Upload, Profilbearbeitung, QR-Generator
9. **Admin-Oberfläche:** Einladungen, Freigabewarteschlange, Meldungen
10. **Rechtliches:** Impressum, Datenschutz, AGB, Einwilligungen, Datenexport, Löschung
11. → **MVP 0 fertig, deployen und Gastros einladen**
12. Ab hier MVP 1: Nutzer-Registrierung, Bewertungsdialog, Feed, Suche, Profile
13. React-Native-App aufsetzen, gemeinsame Logik wiederverwenden

---

## 14. Offene Punkte (bewusst noch nicht entschieden)

- **Produkt- und Firmenname**, noch offen, deshalb überall `APP_NAME`
- **Open Source ja/nein**, Empfehlung: Repository zunächst privat halten. Eine Öffnung ist jederzeit möglich, eine Schließung nicht. Falls später: nur Frontend unter MIT, Backend-Logik und Datenimport privat
- **Gastro-Verifizierungsverfahren**, nach MVP 1 ausarbeiten (Vorschlag: Gewerbeanmeldung hochladen + Telefonanruf, Postbrief nur bei Zweifeln)
- **Kaltstart-Strategie im Detail**, automatisierte Gastro-Ansprache per E-Mail. Achtung: **Kaltakquise per E-Mail an Unternehmen ist in Deutschland nach § 7 UWG grundsätzlich unzulässig** ohne Einwilligung. Das muss vor dem Versand rechtlich geklärt werden; Alternative: Postkarte, Telefon (bei Unternehmen unter Umständen mit mutmaßlicher Einwilligung zulässig), oder Ansprache über die Gäste per QR-Code
- **Video-Maximallänge**, MVP 60 Sek. später eventuell 2 Min.
- **Bestellsystem-Details**, Abholzeiten, Stornierung, Warenkorb-Logik: mit MVP 2 spezifizieren
- **Anbindung Lieferando & Co.**, deren APIs sind nicht offen zugänglich; realistisch erst mit Verhandlungsposition
