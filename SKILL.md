---
name: geoguessr
description: "GeoGuessr plonk assistant for Guy. Read this file at the start of every GeoGuessr session or whenever Guy uploads a Street View screenshot for location help. Trigger on: GeoGuessr screenshots, \"plonk\", \"where is this\", \"which country\", \"which city\", or any visual geography identification task. Contains full deduction methodology, country clue reference tables, session protocol, and plonk guidance. This skill governs the full workflow from session start through final guess. Do not skip reading this file even for seemingly obvious locations."
---

# GeoGuessr Skill

## Session Protocol

1. Each round is fully independent. No carryover from prior maps or sessions.
2. **Environmental read first.** Before drilling into any sign, pole, or plate detail, establish: continent, biome, road quality, driving side. A Pyrenean mountain road with yellow chevrons and Western European tarmac is France/Spain/Andorra before any sign is read.
3. On first image: do a quick visual continent read, then fetch the matching geotips.net regional page:
   - `https://geotips.net/europe/`
   - `https://geotips.net/asia/`
   - `https://geotips.net/south-america/`
   - `https://geotips.net/north-america/`
   - `https://geotips.net/africa/`
   - `https://geotips.net/oceania/`
   - **If fetch fails:** immediately fire `web_search "geoguessr [continent/country] tips clues"` — never silently proceed without reference data.
4. Once country is confirmed, fetch the country-level page (e.g. `https://geotips.net/romania/`) for sub-regional placement. Same fallback applies.
5. **plonkit.net and geometas.com block fetches (403). Never attempt direct fetch.** Use `web_search "plonkit geoguessr [country] clues"` instead.
6. Mid-round: use `image_search` for bollards or poles when ambiguous between two candidates.
7. Show the deduction chain, not just the conclusion.
8. **Never suggest a country not on GeoGuessr.** No-coverage countries include: Cuba, Bosnia & Herzegovina, Paraguay, most of Central Asia, most of West/Central Africa, North Korea, Libya, Syria, Yemen. Default to a neighboring covered country instead.
9. **Result screen reading — NEVER mix these up:**
   - **Black dot = Guy's plonk (where he guessed)**
   - **Avatar/character pin = actual location (where it really was)**
   - The dotted line connects them, distance shown in the bubble. Always state "you plonked X, actual was Y" in that order.
10. **Business name rule:** If a legible business name is visible in the Street View, search it before committing to a plonk. A single search can resolve country and city in seconds.

---

## Deduction Order

Work through these in sequence. Stop narrowing once confident on country; use lower-tier clues for sub-country placement.

### Tier 1 — Instant Eliminators

| Clue | What to look for |
|------|-----------------|
| **Script / language** | Cyrillic, Arabic, Thai, Georgian, Armenian, Hangul, Japanese, Hebrew, Greek — each points to a tight country cluster |
| **Driving side** | Left = UK/Ireland, Australia, NZ, Japan, India, most of southern Africa, Indonesia, Thailand, Malaysia |
| **Google car** | Black snorkel front-right = Kenya. Red car = Russia/Mongolia. White roof rack = Kyrgyzstan. Black car with antennae = Russia. |
| **Landscape / biome** | Savanna, alpine, desert, fjord, tropical rainforest — rule out continents fast |

### Tier 2 — Country Narrowers

| Clue | Notes |
|------|-------|
| **Road line color** | Yellow center lines = Americas (except Chile = all white) + Iceland/Norway. White center = Europe/Asia/Africa/Oceania. Double yellow center = Brazil. Yellow outer lines = South Africa, Israel, Jordan, UAE, Oman. Poland = double white center (notable EU exception). |
| **Bollards** | See bollard table below |
| **Utility poles** | See pole table below |
| **License plates** | Yellow rear = Netherlands, UK (rear only). Blue EU strip = most of Europe. All-white no strip = Russia, Ukraine, some Balkans. |
| **Signs** | Font, color, shape of road signs vary by country. Direction sign color: green = most of Europe/Americas, blue = motorways in EU, white = UK, yellow = Australia. |

### Tier 3 — Sub-Country Placement

| Clue | Notes |
|------|-------|
| **Vegetation density/type** | Tropical coast vs. dry interior; pine forest vs. deciduous |
| **Terrain** | Mountains, plains, coast |
| **Architecture** | Urban density, building materials, roof styles |
| **Domain names / phone prefixes** | .br, .pl, .co.za on shop signs are strong pinpointers |
| **Misc** | Sun in north = southern hemisphere. Jollibee = Philippines. Pink taxis CDMX = Mexico City. Rickshaws = Bangladesh. Cobblestone = Portugal, Brazil, Central European town centers. Borassus palms = broadly Sub-Saharan Africa (not specifically West Africa — includes Uganda, Zambia, Tanzania). |

---

## Verification Gate (Mandatory Before Every Plonk)

Before committing to any plonk, run this check:

1. **Name 2-3 alternative countries** that fit the available clues.
2. **Eliminate each with a specific clue** — not a vibe. "It feels more Romanian" is not an elimination. "The holey poles reach the ground with yellow voltage markers, which is Romania not Hungary" is.
3. **Reverse deduction pass:** walk back through Tier 3 → Tier 2 → Tier 1 and check for contradictions. If anything conflicts with the country call, reopen the analysis.
4. If sole sub-regional anchor is partial text with no supporting visual clue, widen the plonk radius rather than committing precisely.

---

## Bollard Reference

| Country | Description |
|---------|-------------|
| Denmark | White with red reflector, very common |
| UK | Large red reflector on top |
| Poland | Single broad red diagonal stripe |
| Hungary | Red front / white back reflector |
| Albania | Italy-style, black stripe all the way to ground |
| Spain | Black/white + yellow rectangle |
| Thailand | Obelisk shape, alternating black and white |
| Malaysia | Two red rectangles |
| Estonia | Cylindrical; rectangular front + 2 circle rear reflectors |
| Latvia | Thin plank; white rectangular front + 2 white circles rear |
| Lithuania | Wedge shape; orange front + white rear |
| Germany | Red-and-white horizontal stripes (distinctive, common on rural Landstraße) |

---

## Utility Pole Reference

| Country/Region | Description |
|----------------|-------------|
| Japan | Yellow/black diagonal stripes, rectangular cross-section, elevated off ground |
| Taiwan | Diagonal yellow/black stripes, touching ground |
| South Korea | Similar to Japan but fewer stripes, almost touching ground |
| Hungary | Concrete poles with large round holes ("holey poles"), holes go to ground |
| Poland | Concrete holey poles, holes stop above ground |
| Romania | Holey poles to ground + yellow voltage marker + white base paint |
| Ukraine / Russia | T-shaped crossbar with multiple wires; older wooden poles common |
| Bosnia / Serbia area | T-shaped crossbar, characteristic of ex-Yugoslav infrastructure |
| New Zealand | Distinctive short wooden poles with multiple arms |

---

## Baltic Quick Reference

Kept as local methodology aid — these three countries are uniquely hard to separate and the side-by-side diff is faster than any fetch.

| Clue | Estonia | Latvia | Lithuania |
|------|---------|--------|-----------|
| Warning sign border | Thin red, no outline | Thick red border | Thin red + thin white outline |
| Bollard | Cylindrical, rect front + 2 circle rear | Thin plank, white rect + 2 white circles rear | Wedge, orange front + white rear |
| Km marker | Perpendicular to road | Parallel to road | V-shape (two sides at 45°) |
| Language unique chars | Õ, Ä (Finnish-like) | Ā, Ē, Ī, Ū (macrons) | Ė unique; words end in -ai, -as |
| Street name suffix | -tee (rural) | iela | g. (gatvė) |
| Guardrail reflectors | None | White or red | Orange |
| Center road line | Single white | Double white | Single white |
| Default plonk | Near Tallinn (north) | Near Riga (north-central) | Near Vilnius (southeast) |
| Coverage bias | Degraded/grainy image quality common in Estonian coverage | Western Latvia (Kurzeme/Riga corridor) most common | Eastern Lithuania near Vilnius most common |

**Baltic disambiguation rule:** Double white center line = Latvia first. Require a positive Estonian or Lithuanian identifier (bollard, km marker, language chars) before overriding.

---

## Eastern Europe Quick Reference

| Country | Key tells |
|---------|-----------|
| Romania | Holey poles to ground + yellow voltage marker + white base paint; yellow town entry signs with skyline |
| Hungary | Holey poles (holes stop above ground); pink single-story houses; red bollard front reflector |
| Poland | Holey poles (holes stop above ground); double white center lines; green direction signs. Sandy pine forest with no clues = bias toward central Poland, NOT the German border — Poland has far more rural forest Street View coverage than Brandenburg. |
| Ukraine | Red Google car; white-painted tree trunks; blue/yellow license plate strip; poorly maintained roads |
| Russia | Black Google car with antennae; all-white plates no EU strip; Cyrillic |
| Croatia | Completely white plates; two-story houses; house numbers white on blue |
| Serbia | Cyrillic + Latin; bollard red rectangle on left side |
| Bulgaria | Cyrillic direction signs (+ Latin translation below); "ът" definite article form distinguishes Bulgarian from Russian Cyrillic |
| Czech Republic | Large arrow direction signs; thin red plate border + gap |
| Slovakia | Rectangular direction signs with small white arrows; "psom" on no-dog signs |

---

## Special Cases

### Featureless Park Road — Capital Cities Map
Formal allée road with no signs, poles, buildings, or vehicles:

| Country | Differentiators |
|---------|----------------|
| **Paris** ← default plonk | Pale grey gravel, manicured plane trees in strict rows, wide allée, Bois de Boulogne or Vincennes |
| Moscow | Birch/mixed forest, rougher path surface, Sokolniki or Gorky Park |
| Warsaw | Similar to Paris but narrower, less manicured, Łazienki Park |
| Berlin | Tiergarten: dense mixed woodland, less formal layout than Paris |

**Rule:** On a capitals map with a featureless formal park road and no other clues, default plonk is Paris.

### Qatar
Suburban/construction aesthetic repeats uniformly across the entire peninsula. Without a visible landmark, road sign, or geographic anchor, sub-country placement is unreliable. **Default plonk: central Doha.** Bubble antenna on Google car = Qatar meta confirmation.

### Indonesia — Kalimantan vs Java
Indomaret storefront alone does not lock to Java. Kalimantan (Borneo) urban areas can strongly resemble Javanese streetscapes due to transmigration. Check for additional Java-specific tells (dense urban fabric, Javanese script, batik motifs) before committing to a Java plonk.

---

## Plonk Recommendations

When giving a precise placement, always anchor geographically. Always include:

- The region/province AND where it sits within the country (e.g. "northwestern corner", "central highlands", "southern coast")
- A nearby major city or landmark for orientation (e.g. "about 80km northeast of Kraków")
- Explicit map navigation instructions if the location is remote or obscure — do not assume Guy knows where it is

Example: "I'd place this in the Podkarpackie region of southeastern Poland, roughly 60km south of Rzeszów."
