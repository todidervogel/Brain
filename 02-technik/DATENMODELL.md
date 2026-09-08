# Datenmodell

> **Seit Runde 9 sind das echte SQLite-Tabellen**, keine Objekte in einer
> JSON-Datei mehr. Das Schema steht in `Server/src/store/schema.js`, mit
> Typen, Bedingungen (`NOT NULL`, `CHECK`) und Indizes. Der Umzug auf
> PostgreSQL ist damit eine Übersetzung und kein Umbau.

Die Tabellen folgen dem Konzept (Abschnitt 6).

## Zwei Tabellen, die nicht im Konzept stehen

| Tabelle | Warum |
|---|---|
| `zugaenge` | userId, verfahren, salz, hash, gesetztAm. **Passwörter liegen nicht mehr bei den Konten.** Vorher stand das Passwort im Klartext in `users`, und an drei Stellen im Code musste daran gedacht werden, es wieder herauszunehmen. Ein Feld, das es im Nutzerobjekt nicht gibt, kann auch nicht versehentlich in einer Antwort landen. Gehasht mit scrypt (N=16384, r=8, p=1), eigenes Salz je Konto, verglichen mit `timingSafeEqual`. |
| `sitzungen` | token, userId, erstellt, gesehen, laeuftAb. **Anmeldungen überstehen den Neustart.** Vorher lagen sie in einer Map im Arbeitsspeicher; über ngrok startet der Server öfter, als einem lieb ist. |

Beide gehören dem Server, nicht der Fachlogik: `Server/src/domain/` fasst sie
nie an, sondern fragt den Store „stimmt das Passwort?" und bekommt ja oder
nein.

## Neue Felder in Runde 9

| Tabelle | Feld | Wofür |
|---|---|---|
| `users` | `emailVerified`, `phoneVerified`, `verificationSkipped` | Im MVP darf die Bestätigung übersprungen werden. Dass das passiert ist, bleibt am Konto stehen, damit später gezielt nachgefragt werden kann, statt es zu vergessen. |
| `users` | `website`, `phone` | standen vorher nur manchmal drin |
| `places` | `bildUrl`, `bildQuelle`, `bildLizenz` | ein echtes Bild, wenn es eines gibt, mit Nennung, sonst darf es nicht gezeigt werden |
| `places` | `quelleUrl`, `quelleStand` | woher die Beschreibung stammt und wie alt sie ist |
| `places` | `country`, `region`, `closingSince` | aus dem Import |

**Weggefallen:** `users.password` (siehe `zugaenge`) und `places.verified`, das wird aus `claimStatus` gerechnet (`derive.js`). Ein gespeicherter Wert
daneben hätte irgendwann etwas anderes gesagt als die Rechnung.


| Tabelle | Felder (Auszug) | Abweichung vom Konzept |
|---|---|---|
| `users` | id, username, name, email, phone, role, private, joined, bio, website, radius, status, reportCount, notify, placeId (bei Gastro), mustChangePassword, emailVerified, phoneVerified, verificationSkipped | Im Konzept `profiles`. **Kein Passwort**, das liegt in `zugaenge`. |
| `places` | id, osmId, slug, name, cuisine, tags, price, category, **serving**, lat, lng, address, zip, city, phone, website, claimStatus, claimedBy, status, description, features, hours, menuNote | **`serving` ist neu** (siehe unten). `location` ist als lat/lng statt PostGIS-Punkt gespeichert. |
| `menuCategories` | id, placeId, name, description, sort | **Neu**, im Konzept gab es nur `dishes`. Ohne Kategorien ist keine lesbare Speisekarte möglich. |
| `dishes` | id, placeId, categoryId, name, description, priceCents, **diet**, **allergens**, **spicy**, **popular**, **available**, confirmed, sort | Die fett gesetzten Felder sind neu und kommen aus der Anforderung „richtige Speisekarte". |
| `videos` | id, placeId, authorId, authorType, caption, views, durationSec, verifiedOnSite, visibility, status, createdAt, rejectReason | wie Konzept |
| `reviews` | id, videoId, placeId, authorId, createdAt, verifiedOnSite, ratingFood, ratingService, ratingPrice, groupSize, foodHot, dishes[], text, likes, answer, anonymized | `review_dishes` steckt als Array in `dishes` statt in einer eigenen Tabelle. |
| `follows` | followerId, followingId, status | wie Konzept |
| `likes` | userId, videoId | wie Konzept |
| `saves` | userId, type ('video'\|'place'), targetId | **Neu**, Lesezeichen waren im Konzept nicht modelliert. |
| `notifications` | id, userId, type, actor, text, createdAt, unread, link | **Neu** |
| `reports` | id, createdAt, reporterId, targetType, targetId, label, reason, note, count, status, handledBy | wie Konzept |
| `invites` | id, placeId, email, sentAt, status | im Konzept `venue_invites` |
| `suggestions` | id, name, address, type, reportedBy, createdAt, status | **Neu**, die Vorschläge aus „Restaurant fehlt" (E.4) brauchten eine Ablage. |
| `auditLog` | id, at, admin, action, object, note | **Neu**, Begründungspflicht nach DSA (Konzept 10). |
| `locations` | id, name, detail, lat, lng | Sprungziele in der Suche, die drei Gegenden mit Daten |
| `seenVideos`, `searchHistory`, `searchPopular` | Listen von Texten | Stehen zusammen in `listen(name, position, wert)`. Drei Tabellen mit je einer Spalte wären kein Gewinn. `searchPopular` wird aus den Daten gerechnet, nicht erfunden, ein Vorschlag, der zu nichts führt, ist eine Sackgasse. |

## Das neue Feld `serving`

Aus der Anforderung: *„dass man auf den ersten Blick erkennt, was da serviert
wird, ob die Gastro nur Getränke, vegan kann, Fleisch, Fisch oder
Meeresfrüchte hat"*.

```js
serving: ['getraenke' | 'fruehstueck' | 'vegan' | 'vegetarisch' | 'fleisch'
        | 'fisch' | 'meeresfruechte' | 'suess' | 'halal' | 'glutenfrei']
```

- Wird als Symbolzeile angezeigt (`ServingRow`), nicht als Fließtext.
- Ein Betrieb mit **ausschließlich** `getraenke` bekommt das deutliche Label
  „Nur Getränke", der wichtigste Fall, den man auf den ersten Blick sehen
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
Legende am Fuß, die in Deutschland übliche Darstellung. In der Legende
stehen nur die Allergene, die auf dieser Karte tatsächlich vorkommen.
