# ALL_IN feed

Latest pre-start forecasts: `allin/latest.json` (home-team probabilities, PT timestamps). Updated after each logging run.

## Feed v1.1 (2026-11-04): additive fields
Required fields are unchanged: `generated_at_pt`, and for each game `sport, league, event_id, start_pt, home, away, p_allin_home, market_mid_home, source_logged_at_pt`. A game is published only when all of these are non-null.

Top level: `feed_version` ("1.1"), `game_count`, `counts_by_league`, `fields` (descriptions of the fields below).

Optional per-game fields:
- `p_allin_away`, `market_mid_away`: away-side probabilities (1 - home).
- `model_vs_market_pts`: (p_allin_home - market_mid_home) x 100. Positive means ALL_IN is higher on the home side.
- `p_ratings_only_home`: ratings-only home probability, with no market input.
- `favorite`, `favorite_prob`: the side ALL_IN favors and its probability.
- `confidence_tier`: toss-up (<55%), lean (55-65%), strong (>65%).
- `layer`: EARLY = ratings+market blend (more than 60 min before start); LATE = market price only.
- `model_version`, `fit_cutoff`: sub-model id and the last date of data used in the fit.
- `angle`: a plain-English summary built deterministically from the numbers (ratings vs market, NHL goalies/rest, injuries, tennis surface Elo). It describes the forecast and is not betting advice.
- Tennis only: `tournament`, `surface`, `best_of`.

Tennis convention: `home` = player 1 and `away` = player 2. Tennis has no real home side.
