# Design-System

Quelle: `Website-/src/styles/tokens.css`, dort und nur dort stehen Farbwerte.
In Komponenten wird ausschließlich über Variablen zugegriffen.

## Haltung

> Professionell und ruhig, nicht verspielt. Das Essen ist bunt, die
> Oberfläche darf es nicht sein. (Konzept, Abschnitt 9)

Ein Akzentton, sparsam. Keine Farbverläufe, keine Dekoration. Ein einziger
erlaubter Schatten (`--shadow-float`).

## Farben

| Rolle | Hell | Dunkel |
|---|---|---|
| Grundfläche | `#FFFFFF` | `#131316` |
| Zweite Fläche | `#F6F6F4` | `#1D1D21` |
| Text | `#17171A` | `#F1F1F3` |
| Text zweitrangig | `#6B6B72` | `#A3A3AC` |
| Akzent | `#E4572E` | `#F2704A` |
| Rahmen | `#E4E4E1` | `#2B2B31` |
| Sterne | `#E4A02E` | `#E9AE45` |

Der Feed ist **immer** dunkel, Videos wirken auf Schwarz besser. Karte und
Gastro-Seiten folgen der eingestellten Darstellung.

## Dunkelmodus, geändert in Runde 5

**Vorher:** nur in der App, die Website blieb hell.
**Jetzt:** auf beiden Zielen, Standard „Automatisch" (folgt dem Gerät).

Grund: Die Website war dadurch dauerhaft weiß, und in der APK war der
Dunkelmodus praktisch unerreichbar, die Einstellung lag hinter der
Anmeldung, und die App zeigt ohne Anmeldung nur die Anmeldeseite. Die Katze
biss sich in den Schwanz.

Der Umschalter steht deshalb jetzt **in der Kopfleiste**, auch auf der
Anmeldeseite. Zusätzlich in den Einstellungen und im Design-Panel.

## Abstände und Maße

`4 · 8 · 12 · 16 · 24 · 32 · 48 · 64` (`--sp-1` bis `--sp-16`).
Radien: Bedienelemente 8 px, Karten 12 px, Chips rund.
Bedienhöhe 44 px. Inhaltsbreite maximal 1120 px.

## Die Bausteine

`Button` · `IconButton` · `Field` (+ `Input`, `Textarea`, `Select`,
`PasswordInput`, `Checkbox`, `Radio`, `RadioCard`, `Switch`, `Stepper`,
`Dropzone`, `StrengthMeter`) · `Card` · `Chip` · `Badge` · `Notice` ·
`Avatar` · `Thumb` · `VerifiedMark` · `OnSiteBadge` · `Stars` /
`RatingCompact` / `RatingFull` / `StarInput` · `VideoTile` · `PlaceRow` ·
`ReviewCard` · `EmptyState` · `Skeleton` / `SkeletonRow` / `SkeletonTile` ·
`Spinner` / `LoadingBlock` · `Modal` · `Toast` · `Menu` / `FilterChip` ·
`Tabs` / `Accordion` · **`ServingRow` / `ServingPicker`** (neu in Runde 5).

## Sterne, bewusst dreigeteilt

Essen, Service, Preis stehen immer getrennt und werden nie zu einer Zahl
zusammengefasst. Das ist ein Unterscheidungsmerkmal gegenüber Google
(Konzept 9). Mittelwerte mit einer Nachkommastelle, Einzelbewertungen als
ganze Zahl, die Sternegrafik abgerundet (3,8 → drei volle Sterne).

## Laden, Skelette zuerst, Kreisel als Ausnahme

Regel aus Konzept 9: Skelettansichten statt Ladekreisel. Sie zeigen, wie die
Seite gleich aussieht.

Seit Runde 5 gibt es zusätzlich `Spinner` und `LoadingBlock`, für die Fälle,
in denen es nichts zu skizzieren gibt: ein laufender Button, ein Nachladen
unter bestehendem Inhalt, eine Seite, die noch gar nichts zeigen kann. Im
Profil laufen beide zusammen: Skelettkacheln plus ein Kreisel darunter.

## Barrierefreiheit

Kontraste nach WCAG AA in beiden Darstellungen. Sichtbarer Fokusring.
`prefers-reduced-motion` wird beachtet. Symbolzeilen (Angebot) tragen eine
verständliche Beschriftung für Screenreader, auch wenn nur Symbole zu sehen
sind.
