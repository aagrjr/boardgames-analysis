# AGENTS

## Purpose

One self-contained page, with no dependencies or build step, for board-game purchase decisions:

- **`index.html`** — a ranked wishlist with weight, playtime, overlap with the collection, and verdicts. Games outside the current shortlist appear at the end.

GitHub Pages publishes the files from the root of `main`: <https://aagrjr.github.io/boardgames-analysis/>. A push to `main` republishes the site, usually in under a minute.

The page, `README.md`, and this file are in English. Keep the public README brief and free of personal decision history.

## Source of truth for collection data

The user's BGG collection export (`collection.csv`) is the source for personal ratings, play counts, `own`/`prevowned`, and weight. **Check the export before making claims about the collection.** Earlier claims made from memory needed correction.

Useful fields: `rating`, `numplays`, `avgweight`, `average`, `usersrated`, `rank`, `own`, `prevowned`, `bggbestplayers`, `bggrecplayers`, and `itemtype` (filter for `standalone`).

- `own=0` and `prevowned=0` with recorded plays means the game was played but never bought. Do not confuse this with a game that was bought and sold.
- The export contains duplicate rows for some `objectid` values, sometimes with different ratings. Deduplicate by `objectid` before adding play counts.
- **An expansion's `numplays` is always wrong — plays log against the base game.** Every `itemtype: expansion` row in the export reads 0 or near 0 whether or not it has hit the table: the user has played SETI: Space Agencies twice and both Galactic Cruise expansions, and all of them show 0. **Never call an expansion unplayed, never count expansion rows in a play-count statistic, and never recommend one as "still in the box".** Filter to `itemtype == 'standalone'` for any measurement, and ask the user about an expansion rather than reading the export. Ignoring this produced the false claim that 11 of 21 heavy games were unplayed; the real figure over base games was 4 of 15.
- **Personal ratings are stale first impressions, not maintained judgements.** The user does not revisit them, so a rating records how a game felt on the day it was logged and drifts from what he thinks now: he re-played Vantage on 2026-11-14 and said 8.5 against the 8.0 in the export. Treat `rating` as weak, noisy evidence. **`numplays` is the behavioural signal and carries the argument**; use a rating only as a tiebreaker or colour, never as the basis of a verdict, and never read a gap of a few tenths between two games as meaningful.
- For scale when a rating is used anyway: across 145 games with exactly one play the median rating is 8.0 and the mean 7.81, and only 12% reach 9.0. The "9+ after one play is his baseline" claim is false in general — it holds only for the heavy-Euro slice, where 38% of 29 such games reached 9.0.
- Purchases can be newer than the export. `EXCLUIDOS` in `index.html` records later purchases with reasons beginning `owned` or `already bought`. Entropy was one such case.

## BGG data

`xmlapi2` returns **401 Unauthorized** without a session. The site's internal API has been used from an already open `boardgamegeek.com` tab:

- `/api/geekitems?objectid=<id>&objecttype=thing&subtype=boardgame` — designers, artists, mechanics, player counts, and playtime. Credits are in `item.links.boardgamedesigner` and `.boardgameartist`.
- `/api/geekitem/linkeditems?ajax=1&linkdata_index=boardgamedesigner&objectid=<person>&objecttype=person&pageid=<n>&showcount=50&sort=rank&subtype=boardgamedesigner` — a person's catalogue. It returns at most 50 items per page; paginate with `pageid`. For artists, replace both instances of `boardgamedesigner` with `boardgameartist`.
- `/api/dynamicinfo?objectid=<id>&objecttype=thing` — rating, rating count, and weight (`item.stats.average`, `.usersrated`, `.avgweight`), rank (`item.rankinfo`), and the best-player-count poll (`item.polls.userplayers.best`). The poll works for games outside the collection export too.

As checked on 2026-10-01, `api.geekdo.com` responded to `curl`, while name search (`/geeksearch.php`) encountered Cloudflare. To find a person's ID, follow the designer link from a known game; to find games, use that person's `linkeditems`. Recheck current access before relying on this observation. Creator credits collected on 2026-09-30 are in `designers.html`.

## Ludopedia

- `/jogo/<slug>` pages did not respond to `curl` when checked; they were accessible through an open `ludopedia.com.br` browser tab.
- **Search works, at `/search?search=<name>`** — server-rendered, so a `fetch` from an open Ludopedia tab returns the results. (`/busca?...` is a 404; that wrong path is why search was previously recorded as unusable.) Each result anchor gives the title with its year and the `/jogo/<slug>` link.
- **A game can have a separate entry for its Brazilian edition, and the derived slug finds the foreign one.** `/jogo/jamaica` lists only foreign publishers; the Galápagos release lives at `/jogo/jamaica-revised`, titled "Jamaica (Edição Revisada)". Deriving the slug from the English name and reading one page is therefore not enough to conclude a game has no Brazilian edition — **search the name and check every entry whose title starts with it.**
- The site rate-limited after roughly 90 requests, returning HTTP 429 with a short body. **Check response status before parsing.** Mark a Brazilian edition as unverified after a 429; do not interpret it as absent. Avoid large scans.
- The HTML marks a Brazilian publisher with `<img class="ludo-fh-br" title="Editora nacional">` inside `.ludo-fh-cred`, immediately before the first Brazilian publisher.
- The publishers popup is grouped by country, and its content is already in the fetched HTML. A Brazilian publisher is reliably marked by `img.ludo-fh-br` inside the publishers `.ludo-fh-cred`, and it is the first publisher anchor there.
- **The page shows only the first two publishers and hides the rest behind a `.ludo-fh-mais` (`+N`) button.** Reading only the visible group produces false negatives: that is how Jamaica, published here by Galápagos, was recorded as having no Brazilian edition. **No flag in the visible portion does not mean no Brazilian edition.** Read every publisher before concluding absence; when in doubt set `brUnknown`.
- General rule for this page: given a choice between asserting something unverified and saying "unchecked", say unchecked. A false "no Brazilian edition" can make the user import a game that is on sale locally.

## Collection benchmarks

These figures were calculated from the export and underpin the page's verdicts. Recalculate before changing them.

| Weight | Owned standalone games | Mean plays |
|---|---:|---:|
| < 2.0 | 44 | 7.66 |
| 2.0–2.5 | 23 | 4.48 |
| 2.5–3.0 | 12 | 4.42 |
| 3.0–3.5 | 10 | 4.40 |
| 3.5+ | 13 | 2.54 |

**Solo play is a large share of the plays, and it gates by setup cost, not by weight.** The user told us (2026-11-16) that two of Sanctuary's ten plays and two of Vantage's three were solo, and that he deliberately did not set up the heavy games to play alone. That is the mechanism behind the 3.35 line: a heavy game is not harder to enjoy, it is gated on a group night, and group nights are scarcer than solo nights. Across the 52 owned solo-capable standalone games the median plays by weight band are <2.5 → 3, 2.5–3.35 → **4**, >3.35 → 2, so the sweet spot holds even after restricting to games he *could* play alone.

**The export cannot tell solo from multiplayer** — `numplays` is a single number. Never assert a solo/group split from it; ask the user.

**Consequence for the wishlist: solo support is a real value multiplier, and a game above 3.35 that only plays with a group needs a much stronger case.** When comparing two candidates of similar weight, the one that plays at one is the better buy for this collection.

**The 3.35 weight line:** games from 3.00 to 3.35 have a median of four plays; above 3.35, the median is two, with five of fourteen at zero or one play.

**The "long cooperative games fail" rule was wrong about the mechanism — treat it as a weak prior, never a veto.** The record it was built on: six long coops tried, none past three plays, four of six sold (Mansions of Madness 180min, Robinson Crusoe 120min, Spirit Island 120min, Chronicles of Crime 90min sold; Deep Regrets 150min, Sherlock Holmes Consulting Detective 120min owned). Short coops thrive by contrast — Hanabi 10 plays, Pandemic Legacy: Season 1 9, Sherlock 13 7, Pandemic 6, Just One 5. **But Vantage breaks it.** At 180 minutes it is the longest of the lot, and the user played it three times in the first days of owning it (2026-11-09, -14 and -16; the export still shows 1, see the lag rule). So duration is not what killed the others, and the failures do not share a weight either (Spirit Island 4.0 and Chronicles of Crime 2.1 both died). What the six do share is a scripted campaign or scenario book that has to be resumed; Vantage resets to a fresh planet every session. Until that is measured, say "long coops have a bad record here, with one clear exception" and let play counts decide — do not argue a purchase down on length alone.

**Cooperative games have a different weight profile.** The active cooperative games sit at weight 2.66 or below. Above 2.5, every purchased coop had one or two plays except Pandemic Legacy: Season 1, a campaign meant to be finished, and Vantage (2.35 average weight, so not a counterexample to this one).

**Best with two does not predict plays.** Rechecked against the 2026-10-01 export over every owned game with a weight and a `bggbestplayers` value: below weight 2.5, games best with two have a median of 3 plays against 4 for the rest; at 2.5 and above both sit at a median of 2. The earlier reading of 6.5 versus 2.0 came from one narrow slice (nature/science engine builders) and does not generalise. Weight — the 3.35 line — is what predicts plays; the duration effect once claimed for cooperative games did not survive Vantage (see above). Treat the BGG player-count poll as a description of how a game plays at two, never as evidence that the user will play it more.

**Competitive deckbuilders have seen more play than cooperative ones.** Competitive examples include Clank!, Dune: Imperium, Clank!: Catacombs, Star Wars: The Deckbuilding Game, and Lost Ruins of Arnak. Aeon's End and Marvel Champions were played once each and were not purchased.

## Editing data

In `index.html`, `JOGOS` holds one game per line; `EXCLUIDOS` holds games outside the shortlist as `["name", "reason"]`.

For scripted replacements, assert that the old text occurs exactly once before replacing it. Take care to read a complete game record: a previous edit cut off part of one by assuming it occupied only one line.

Do not hard-code counts in subtitles. The page computes them from its data, and the self-test compares rendered text to the calculation.

## Self-tests

Open the page with `#test` appended to the URL and check the console for `Board-game checks passed`. Run this check after manual data edits. Existing tests have caught stale expected purchase order and BGG rank values.

For a local preview, serve the directory with `python3 -m http.server 8731` and open `http://127.0.0.1:8731/index.html#test`. Add `?v=N` before `#test` when reopening after an edit to avoid a cached copy.

## Recommendation guidance

Support recommendations with collection data rather than a poll of options. Check direct evidence about a specific game before applying category averages. Distinguish liking a game from repeatedly playing it, and lead with play counts rather than ratings (see the ratings caveat above). When the user decides against a recommendation, record that in the game's evidence and move on without reopening the decision.
