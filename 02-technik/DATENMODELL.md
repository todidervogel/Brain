# Datenmodell im Prototyp

Die Tabellen folgen dem Konzept (Abschnitt 6), damit der spätere Umzug auf
PostgreSQL/Supabase eine Übersetzung ist und kein Umbau.

| Tabelle | Felder (Auszug) | Abweichung vom Konzept |
|---|---|---|
| `users` | id, username, name, email, password, role, private, joined, bio, radius, status, reportCount, notify, placeId (bei Gastro), mustChangePassword | Im Konzept `profiles`. Passwort liegt hier mit in der Tabelle, weil es keinen Auth-Dienst gibt. |
| `places` | id, osmId, slug, name, cuisine, tags, price, category, **serving**, lat, lng, address, zip, city, phone, website, claimStatus, claimedBy, status, description, features, hours, menuNote | **`serving` ist neu** (siehe unten). `location` ist als lat/lng statt PostGIS-Punkt gespeichert. |
| `menuCategories` | id, placeId, name, description, sort | **Neu** — im Konzept gab es nur `dishes`. Ohne Kategorien ist keine lesbare Speisekarte möglich. |
| `dishes` | id, placeId, categoryId, name, description, priceCents, **diet**, **allergens**, **spicy**, **popular**, **available**, confirmed, sort | Die fett gesetzten Felder sind neu und kommen aus der Anforderung „richtige Speisekarte". |
| `videos` | id, placeId, authorId, authorType, caption, views, durationSec, verifiedOnSite, visibility, status, createdAt, rejectReason | wie Konzept |
| `reviews` | id, videoId, placeId, authorId, createdAt, verifiedOnSite, ratingFood, ratingService, ratingPrice, groupSize, foodHot, dishes[], text, likes, answer, anonymized | `review_dishes` steckt als Array in `dishes` statt in einer eigenen Tabelle. |
| `follows` | followerId, followingId, status | wie Konzept |
| `likes` | userId, videoId | wie Konzept |
| `saves` | userId, type ('video'\|'place'), targetId | **Neu** — Lesezeichen waren im Konzept nicht modelliert. |
| `notifications` | id, userId, type, actor, text, createdAt, unread, link | **Neu** |
| `reports` | id, createdAt, reporterId, targetType, targetId, label, reason, note, count, status, handledBy | wie Konzept |
| `invites` | id, placeId, email, sentAt, status | im Konzept `venue_invites` |
| `suggestions` | id, name, address, type, reportedBy, createdAt, status | **Neu** — die Vorschläge aus „Restaurant fehlt" (E.4) brauchten eine Ablage. |
| `auditLog` | id, at, admin, action, object, note | **Neu** — Begründungspflicht nach DSA (Konzept 10). |
| `seenVideos`, `searchHistory`, `locations`, `searchPopular` | Listen | Hilfsdaten |

## Das neue Feld `serving`

Aus der Anforderung: *„dass man auf den ersten Blick erkennt, was da serviert
wird — ob die Gastro nur Getränke, vegan kann, Fleisch, Fisch oder
Meeresfrüchte hat"*.

```js
serving: ['getraenke' | 'fruehstueck' | 'vegan' | 'vegetarisch' | 'fleisch'
        | 'fisch' | 'meeresfruechte' | 'suess' | 'halal' | 'glutenfrei']
```

- Wird als Symbolzeile angezeigt (`ServingRow`), nicht als Fließtext.
- Ein Betrieb mit **ausschließlich** `getraenke` bekommt das deutliche Label
  „Nur Getränke" — der wichtigste Fall, den man auf den ersten Blick sehen
  will.
- Erscheint auf: Trefferliste (Karte, Suche), Kartenvorschau,
  Restaurantleiste im Feed, Gastro-Seite (ganz oben), Speisekarte,
  Betriebswahl beim Hochladen.
- Ist Filter auf der Karte und in der Suche (Und-Verknüpfung: es werden nur
  Betriebe gezeigt, die **alles** Ausgewählte anbieten).
- Wird vom Gastro selbst gepflegt (Profil und Einrichtungsassistent).

Unterschied zu `features`: `features` sind Umstände (Außenplätze, WLAN,
Kartenzahlung), `serving` ist das Essen selbst.

## Allergene

`ALLERGEN_KEYS` in `src/data/seed.js` enthält die vierzehn nach
Lebensmittelinformationsverordnung kennzeichnungspflichtigen Allergene. Auf
der Speisekarte erscheinen sie als hochgestellte Zahlen am Gericht, mit einer
Legende am Fuß — die in Deutschland übliche Darstellung. In der Legende
stehen nur die Allergene, die auf dieser Karte tatsächlich vorkommen.
