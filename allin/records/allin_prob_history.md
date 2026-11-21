# ALL_IN (Model A) probability history

Exported from the ALL_IN pre-tip logs on 2026-11-20, Pacific Time, for the Sim Says panel backtest. These files are model probabilities and market mids only. They do not include bot-card trades, fills, or credentials.

Coverage starts with the first logs on 2026-10-21. Sports included: NBA, NHL, NFL, CFB, tennis (ATP/WTA), and a handful of late-season MLB games. There is no soccer in this log. Upcoming or not-yet-graded games have a blank `home_win`.

## Files

- `allin_prob_history_early.csv` — one row per game. This is the file to use for a backtest. The probability is taken from the earliest EARLY-layer log at least 60 minutes before the then-listed start time, when one exists. If no such row exists, the fallback is noted in `selection`.
- `allin_prob_history_snapshots.csv` — every logged snapshot of the same fields, so a different cutoff can be chosen.

## Fields

- `p_allin_home` / `p_allin_away` — Model A (ALL_IN) win probability for the home side (tennis: player 1) and the away side (player 2).
- `confidence_tier` — derived from `favorite_prob` with the public feed rule: toss-up (<55%), lean (55–65%), strong (>65%). The live h2h log did not store this field historically.
- `p_ratings_only_home` — ratings-only home probability, with no market input.
- `market_mid_home` — contemporaneous no-vig / blended market mid for the home side.
- `layer` — EARLY is a ratings+market blend; other labels (including LATE or market-only) are as logged.
- `lead_min` — minutes from `logged_utc` to the start time listed at that log.
- `start_utc_latest` (early file only) — last start time seen in the logs for that game. Tennis and CFB start times were sometimes updated after the first log.
- `home_win` — 1 if the home side (tennis: player 1) won, 0 if it lost, blank if not yet graded. `close_mid` is the market mid near start, when graded.
- `graded` — 1 if a final result has been joined from the early-graded files.

Tennis convention matches the public feed: `home` is player 1 and `away` is player 2.
