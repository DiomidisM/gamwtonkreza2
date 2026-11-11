# gamwtonkreza2

A daily Greek geography game. Each day, five green dots appear one after another on a
plain map of Greece, each on a real town or village. Type the name. The closer your answer
is to the dot, the more points you get: up to 1,000 per round, 5,000 per day.
After all five rounds, the prefecture (νομός) borders appear on the map.

- Everyone gets the same five places on the same day.
- A new set starts every day at 00:00 Greek time.
- Each person plays once a day. The game remembers this in the browser, like Wordle.
  It isn't a hard lock: clearing the browser's data or using another device lets someone
  play again.
- Each day has a number, shown next to the date and in the shared result: 10 November 2026 is #1.
- Two red boxes in the bottom right corner count the days to a date, then the days since:
  4 June 2027 ("μέρες χωρίς / με τον Κοντογιάννη") and 26 November 2026 ("μέρες χωρίς / με τον Αχιλλέα").
  They look exactly like the counter box of the original game, Πάρε 5. On phones they sit at
  the end of the page, so they never cover the answer box. Set in `BANDS` in `tools/template.html`.

## Practice

`practice.html` is the same game with five random places on every load (still at least 75 km
apart). Nothing is saved: it never counts as today's game, never changes the statistics or the
record, and a reload starts a new five. The shared result says "practice" in place of the day
number and date, and links to the daily game. For now nothing links to the practice page:
only someone who knows its address can open it. `tools/build.py` writes both pages (`PRACTICE` in
`tools/template.html`).

## Play

Open `index.html` in any web browser (double-click it). It works offline, except for
the fonts. To share it, put the file on any web host, or use the claude.ai link.

## How the daily places are chosen

Set in `tools/template.html` (`POOL`, `REST`, `MIN_APART`) and `tools/prepare_data.py`:

- All five are drawn at random from 5,688 places: every town and village with 100+ people
  at the latest census (2021 for almost all) that is drawn on the map, except suburbs.
- Population is the most recent value on Wikidata, not the largest one ever recorded.
- A suburb is a place inside the built-up area of a bigger town of 5,000+ people:
  12 km around Athens, 7 km around Thessaloniki, and 1.4 km × √(population ÷ 10,000)
  around any other town (Patras about 5.7 km, a town of 10,000 about 1.4 km).
  Piraeus counts as a city of its own.
- A place does not come back for at least 7 days.
- The day's five places are at least 75 km apart. Any minimum favours the edges of Greece a
  little, since a central place has more neighbours to clash with: at 75 km central Greece
  comes up about 7–10% less often than its fair share (about 15% at 100 km, 2–4% at 50 km).
- The draw comes from the date alone, so every browser gets the same places.
- Most places in Greece are villages, so about 6 in 7 dots land on a village of under 1,000 people,
  and about 2 in 5 on one of under 250.

You can answer with any of 8,014 Greek towns and villages (100+ people at some census, suburbs left out), in Greek or Latin letters. Someone in a suburb answers with the city, e.g. Αθήνα for Peristeri.
Nicknames work too: typing "Σαλούγκα" offers Θεσσαλονίκη (`ALIASES` in `tools/template.html`).

## Results screen

Total score, best score, perfect rounds (exact answers), the closest and the farthest guess of
the day, and the day's average distance next to your average over all earlier games.

## Look

One light look on every device, using WorldsWire's exact light palette: black text and
outlines (#13110f) on beige (#f5f1ec), cards #f8f5f1, grey #46413e, a green target dot
(#22c55e) and a blue guess dot (#3b82f6). Inter for text, IBM Plex Mono for numbers
and labels. IBM Plex Mono has no Greek letters, so Greek falls back to Inter.

## Statistics

The **Στατιστικά** button (top of the page, and on the results screen) opens a window with:
games played, average score, best score, current streak (and longest), perfect rounds in
total, the closest guess ever, the average distance over all rounds, the share of green
rounds (800+ points), and every past day with its five places and your answers.

They are worked out from the games saved in the browser, so they survive reloads and
closing the browser. They are lost only if the browser's data is cleared, in a private
window, or on another device or browser.

## Saved games

Each browser keeps its own games. `SAVE_V` in `tools/template.html` is the save format:
raising it forgets every saved game once, including today's once-a-day lock.

## Scoring

Exact place (under 0.5 km): 1,000. Otherwise `1000 × (1 − (km/350)^0.954924)^1.196671`,
a smooth curve through 10 km = 960 and 100 km = 650 that reaches 0 at 350 km.
Anything 350 km or more away scores 0. Set in `tools/template.html` (`ZERO_KM`, `CURVE_A`, `CURVE_B`).

| Distance | 5 km | 10 km | 25 km | 50 km | 75 km | 100 km | 150 km | 200 km | 250 km | 300 km | 350 km |
|----------|-----:|------:|------:|------:|------:|-------:|-------:|-------:|-------:|-------:|-------:|
| Points   |  979 |   960 |   905 |   816 |   732 |    650 |    494 |    348 |    213 |     93 |      0 |

A round is green from 800 points (up to about 54 km) and yellow from 400 (up to about 181 km).
Games saved with an earlier curve keep the points they got then.

## What's inside

- `index.html`: the whole game in one file. `practice.html`: the practice version.
- `tools/`: what was used to make it. You only need this to change the game.
  - `template.html`: the page design and the game rules.
  - `data.json`: the towns and villages, the map shapes and the prefecture borders.
  - `build.py`: puts `template.html` and `data.json` together into `index.html`.
    Run `python3 tools/build.py` after any change.
  - `prepare_data.py` and `places.rq`: how `data.json` was made from
    [Wikidata](https://query.wikidata.org) and the
    [Natural Earth](https://www.naturalearthdata.com) map (via the `world-atlas` package).
  - `prepare_borders.py`: adds the prefecture borders, from the
    [geoBoundaries](https://www.geoboundaries.org) municipalities of Greece (public domain).
    Athens' four sectors count as one prefecture and the Islands unit belongs to Piraeus,
    as before 2011.
