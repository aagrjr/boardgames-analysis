# AGENTS

## Purpose

Two self-contained pages, with no dependencies or build step, for board-game purchase decisions:

- **`index.html`** — a ranked wishlist with weight, playtime, overlap with the collection, and verdicts. Games outside the current shortlist appear at the end.
- **`designers.html`** — tabbed designer and artist catalogues showing relevant games from creators whose work has performed well in the collection.

GitHub Pages publishes the files from the root of `main`: <https://aagrjr.github.io/boardgames-analysis/>. A push to `main` republishes the site, usually in under a minute. The pages link to each other, and a self-test checks those links.

The pages, `README.md`, and this file are in English. Keep the public README brief and free of personal decision history.

## Source of truth for collection data

The user's BGG collection export (`collection.csv`) is the source for personal ratings, play counts, `own`/`prevowned`, and weight. **Check the export before making claims about the collection.** Earlier claims made from memory needed correction.

Useful fields: `rating`, `numplays`, `avgweight`, `average`, `usersrated`, `rank`, `own`, `prevowned`, `bggbestplayers`, `bggrecplayers`, and `itemtype` (filter for `standalone`).

- `own=0` and `prevowned=0` with recorded plays means the game was played but never bought. Do not confuse this with a game that was bought and sold.
- The export contains duplicate rows for some `objectid` values, sometimes with different ratings. Deduplicate by `objectid` before adding play counts.
- Purchases can be newer than the export. `EXCLUIDOS` in `index.html` records later purchases with reasons beginning `owned` or `already bought`; `designers.html` uses these as an ownership fallback. Entropy was one such case.

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

## Catalogue rules (`designers.html`)

- For the six expanded catalogues, select the top 20 BGG-ranked games that also have a rating of at least 7.4 and at least 1,000 ratings, plus any game the user owned or played. Rank uses a Bayesian average; it is not the same as the raw rating. If the cutoff changes, scan the full creator catalogue, not only its top 20.
- The 1,000-rating floor filters out unreliable tiny samples. Previously observed catalogue sizes were 227 Cathala credits and 234 Dutrait credits; an exhaustive display would be less useful for purchase decisions.
- The 7.4 rating floor applies to every tab. Games the user still owns and their expansions are exempt, as are the explicit `wantBack` and `requested` exceptions below. A game that was sold or merely played does not survive on a rating below the line just because the user rated it highly — T.I.M.E Stories (BGG 7.34, user 9.5) was removed under this rule. The rank and rating-count cutoffs apply only to the expanded tabs.
- `requested: true` marks a specific game the user explicitly asked to add despite the rating floor. Keep the reason visible in `creditNote`. Calimala is the sole current case; its Ian O'Toole art belongs to the Alley Cat second edition.
- `wantBack: true` is a deliberate per-row exception for a game the user sold and wants to buy again. It exempts the row from the 7.4 floor and the rating-count floor, and renders a "want it back" tag so the reason is visible rather than looking like a data error. Abyss carries it. Set it only when the user says so, never to rescue a row from a rule.
- `linkeditems` entries with `rank` 0 are usually expansions or promos. Filtering for `rank > 0` isolates standalone games without another request — but note that this hides **every** expansion, which is how Galileo Galilei: Luna went missing.
- **Expansions**: list an expansion only when the user owns the game it expands. It is judged by that base game, so it is exempt from both the 7.4 floor and the 1,000-rating floor (Azul: Crystal Mosaic rates 7.26; Luna has 159 ratings). It renders indented under its base, outside the sort order.
- Each row carries `melhor` (the BGG best-player-count range) and `dois` (`excellent at 2` / `good at 2` / `poor at 2`), the same vocabulary and chip styling as the wishlist. `dois` is derived from the poll at `/api/dynamicinfo`: 2 inside the best range, inside the recommended range, or neither. A game that cannot seat two carries no `dois`.
- Only full playable expansions belong — no promo cards, tiles, medals or mini-expansions. Read the BGG description before including one: Unconscious Mind: Free Association is named like a full expansion but its contents are 29 cards and 6 tiles of mix-and-match modules, and it was removed for that reason. A BGG `subtypes` of `boardgameexpansion` says nothing about size. There is no automatic signal for this: weight, player count and playtime are all inherited from the base game, and rating counts track release date rather than size. The included list is a case-by-case judgement; the self-test only blocks names matching promo/cards/tiles/pack/mini-expansion.
- When two editions represent the same game, keep the one carrying the strongest personal record (`own` > `sold` > `played` > none); break ties by rating count. This retains 7 Wonders (2010) over its Second Edition and The Castles of Burgundy: Special Edition over the base game. Stockpile and Kraftwagen are explicit user choices: keep only Stockpile: Epic Edition, and only the newer Kraftwagen.
- Every row needs BGG measurements and a real collection status. Do not add rows with invented IDs, missing weight, or guessed ownership. The self-test enforces this.

## Collection benchmarks

These figures were calculated from the export and underpin the pages' verdicts. Recalculate before changing them.

| Weight | Owned standalone games | Mean plays |
|---|---:|---:|
| < 2.0 | 44 | 7.66 |
| 2.0–2.5 | 23 | 4.48 |
| 2.5–3.0 | 12 | 4.42 |
| 3.0–3.5 | 10 | 4.40 |
| 3.5+ | 13 | 2.54 |

**The 3.35 weight line:** games from 3.00 to 3.35 have a median of four plays; above 3.35, the median is two, with five of fourteen at zero or one play. `designers.html` uses this line.

**Cooperative games have a different pattern.** The seven active cooperative games all have weight at most 2.66. Every purchased cooperative game above 2.5 had only one or two plays, apart from Pandemic Legacy: Season 1, a campaign meant to be completed.

**Best with two does not predict plays.** Rechecked against the 2026-10-01 export over every owned game with a weight and a `bggbestplayers` value: below weight 2.5, games best with two have a median of 3 plays against 4 for the rest; at 2.5 and above both sit at a median of 2. The earlier reading of 6.5 versus 2.0 came from one narrow slice (nature/science engine builders) and does not generalise. Weight — the 3.35 line — and, for cooperative games, duration are what predict plays. Treat the BGG player-count poll as a description of how a game plays at two, never as evidence that the user will play it more.

**Competitive deckbuilders have seen more play than cooperative ones.** Competitive examples include Clank!, Dune: Imperium, Clank!: Catacombs, Star Wars: The Deckbuilding Game, and Lost Ruins of Arnak. Aeon's End and Marvel Champions were played once each and were not purchased.

## One entry per game in `designers.html`

Collapse alternative editions of the same game. Rococo: Deluxe Edition replaces base Rococo; Glen More II: Chronicles replaces Glen More. Keep the newer Kraftwagen: Age of Engineering, and retain both Stockpile editions as requested.

Expansions are included with an `expansion` tag and an `exp` field naming the base game. Their verdict depends on whether the base game is owned, rather than applying the 3.35 weight line. Darwin's Journey: Fireland Expansion is the test case.

The `br` field names a Brazilian publisher when one is confirmed. A missing `br` value must not be interpreted as absence when verification failed. Ludopedia game slugs usually derive from the English title: lowercase, remove accents, and replace nonalphanumeric runs with hyphens. `CO₂: Second Chance`, `Masters of Renaissance`, and `Age of Steam` are known exceptions in `LUDO_FIXO`. Check alternatives before concluding that a page or Brazilian edition is absent.

The original 61 links were revalidated on 2026-10-01. The original rows derive their Ludopedia URLs with `ludoSlug()`; `LUDO_FIXO` contains the exceptions. The self-test checks the URL shape. Ludopedia's Brazilian-publisher marker does not distinguish a released edition from an announced one. `index.html` distinguishes `released` and `announced` in `brasil.s` because those entries were checked individually.

Wishlist decisions do not belong in the creator catalogue. A game removed from the wishlist can still be missing from the collection.

## Editing data

In `index.html`, `JOGOS` holds one game per line; `EXCLUIDOS` holds games outside the shortlist as `["name", "reason"]`. In `designers.html`, `JOGOS` also holds one record per line, and `tabs` identifies every creator credited on that game.

For scripted replacements, assert that the old text occurs exactly once before replacing it. Take care to read a complete game record: a previous edit cut off part of one by assuming it occupied only one line.

Do not hard-code counts in subtitles. Both pages compute them from their data, and the self-tests compare rendered text to the calculation.

## Self-tests

Open either page with `#test` appended to the URL and check the console for `Board-game checks passed` or `Designer-catalogue checks passed`. Run these checks after manual data edits. Existing tests have caught stale expected purchase order and BGG rank values.

For a local preview, serve the directory with `python3 -m http.server 8731` and open `http://127.0.0.1:8731/index.html#test`. Add `?v=N` before `#test` when reopening after an edit to avoid a cached copy.

## Recommendation guidance

Support recommendations with collection data rather than a poll of options. Check direct evidence about a specific game before applying category averages. Distinguish liking a game from repeatedly playing it: high ratings after one play are common. When the user decides against a recommendation, record that in the game's evidence and move on without reopening the decision.
