# Die Speisekarte

Anforderung: *„füge mal Seiten hinzu mit Speisekarte … als Speisekarte sehe
ich bspw. das von einem Restaurant in Spanien, das als reine Speisekarte
recht gut ist."*

Gemeint war eine digitale Karte im Stil der QR-Karten, die inzwischen in
vielen Lokalen hängen: man scannt, liest, mehr nicht.

## Zwei getrennte Orte

**1. Reiter auf der Gastro-Seite** (`/g/[slug]`, Reiter „Speisekarte")
Eine Vorschau: pro Kategorie die ersten drei Gerichte, darunter „+n mehr",
darunter der Weg zur ganzen Karte. Für Leute, die sich einen Überblick
verschaffen.

**2. Eigene Seite** (`/g/[slug]/speisekarte`)
Die ganze Karte, **ohne den Rahmen der App**: keine untere Navigation, keine
Fußzeile, kein Feed. Wer im Lokal sitzt, will lesen.

## Aufbau der eigenen Seite

```
┌──────────────────────────────────────┐
│ ‹ Zur Seite von Trattoria Bella      │   Rückweg, klein
│ Trattoria Bella  ✓                   │   Name, Verifiziert-Häkchen
│ Italienisch · Pizza · €€ · Adresse   │
│ 🥩 Fleisch  🐟 Fisch  🥗 Vegetarisch │   Angebot auf einen Blick
│ Jetzt geöffnet · bis 22:00 · 8 Gerichte │
├──────────────────────────────────────┤ ← ab hier klebt alles oben fest
│ [ Gericht suchen …              ✕ ]  │
│ (Vorspeisen)(Pizza)(Pasta)(Dolci)    │   Sprungleiste, folgt dem Scrollen
├──────────────────────────────────────┤
│ Vorspeisen                           │
│ ─────────────────────────────────    │
│ Bruschetta ¹                  6,00 € │
│ Geröstetes Brot, Tomate, Knoblauch   │
│ (Vegan)(Vegetarisch)                 │
│ ★★★★☆ 4,1 (5 Bewertungen)            │
│ …                                    │
├──────────────────────────────────────┤
│ Allergene                            │
│ ¹ Glutenhaltiges Getreide  ⁷ Milch   │   nur die tatsächlich vorkommenden
│ Alle Preise inkl. MwSt.              │
│ Angaben ohne Gewähr.                 │
│ [Zurück] [Karte teilen] [Drucken]    │
└──────────────────────────────────────┘
```

## Was die Karte kann

- **Sprungleiste** klebt oben und markiert die Kategorie, die gerade im Blick
  ist (über einen `IntersectionObserver`).
- **Suche** filtert innerhalb der Kategorien, die Gliederung bleibt erhalten.
- **Kennzeichnung** je Gericht: Vegan · Vegetarisch · Glutenfrei · Schärfe
  (ein bis drei Punkte) · Beliebt · Heute nicht verfügbar.
- **Allergene** als hochgestellte Zahlen, mit Legende am Fuß — die in
  Deutschland übliche Darstellung. In der Legende stehen nur die Allergene,
  die auf dieser Karte vorkommen.
- **Gästebewertung pro Gericht**, berechnet aus allen Bewertungen. Das ist
  das Alleinstellungsmerkmal aus dem Konzept: bewertet wird das Gericht,
  nicht das Restaurant.
- **Druckansicht**: beim Drucken fallen Sprungleiste, Rückweg und
  Schaltflächen weg, Gerichte werden nicht über Seitenumbrüche zerrissen.
- **Dunkelmodus** wie überall.

## Wer pflegt sie?

Der Gastro-Bereich unter `/gastro/speisekarte`:

- Kategorien anlegen, umbenennen, verschieben (Pfeile hoch/runter), löschen
  (mit Rückfrage, weil die Gerichte mit verschwinden).
- Gerichte anlegen und bearbeiten: Name, Beschreibung, Preis, Kategorie,
  Kennzeichnung, Schärfe, alle vierzehn Allergene, „beliebt", „heute
  verfügbar".
- Der Schalter „Heute verfügbar" wirkt sofort — ausverkaufte Gerichte stehen
  ausgegraut mit dem Hinweis „Heute nicht verfügbar" auf der Karte.
- Oben rechts führt „Als Gast ansehen" direkt auf die öffentliche Karte.

## QR-Code

Unter `/gastro/qr` lässt sich einstellen, wohin der Code führt: auf die
Gastro-Seite oder direkt in die Speisekarte. Die Zieladresse steht im
Klartext unter der Vorschau und lässt sich kopieren.
