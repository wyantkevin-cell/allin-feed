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

## Feed v1.2 (2026-11-04): more additive fields
Nothing above changed. `feed_version` is now "1.2". Every new field is optional and is null when its source is missing. A missing source never removes a game from the feed.

### Spread and total (for game sims)
- `market_spread_home`: the book's point spread from the home side. Negative means home is favored (e.g. `-3.5`). This is the current DraftKings line as ESPN shows it. For NHL it is the puck line (almost always ±1.5), which is not an estimate of the margin.
- `market_spread_home_price`, `market_total_over_price`: American odds for the home spread side and for the over.
- `market_total`: the book's game total.
- `spread_total_source`: e.g. `espn_draftkings_current`.
- `spread_total_as_of_pt`: when the feed fetched the line from ESPN. ESPN doesn't timestamp lines, and the feed caches them for up to 10 minutes.
- `allin_implied_spread_home`: always null. ALL_IN predicts win probability only and has no fitted margin model, so it can't produce a principled implied spread.
- Tennis: always null. NBA/NHL games for the next day are often null until books post lines.

### Sample size (for shrinkage)
- `n_games_home`, `n_games_away`, `n_effective` (= the smaller of the two), `n_definition`.
- Team sports (NBA, NHL, NFL, CFB): the number of current-season games each team has in ALL_IN's training data through `fit_cutoff`. This is exactly the count the fit stores (`cur_season_games`). For CFB, only games with an FBS team are in the data, so an FCS opponent can show a very small n (e.g. 1).
- Tennis: the total number of singles matches the player has in ALL_IN's tennis Elo history. That history covers tour, qualifying, Challenger and ITF results since 2019, then ESPN tour-level results. It is not a last-12-months count. The Elo K-factor shrinks as this count grows.
- **Current-season n understates the information in the ratings.** ALL_IN ratings carry prior seasons forward with regression toward the mean at each season change. Elo keeps 50% of last season's deviation for NBA, 67% for NFL, and 80% for CFB and NHL. Form and offense/defense components keep 50% (CFB EPA efficiency keeps 60%). A team with n = 7 is not "7 games of information." If you use `w = n/(n+10)`, treat n as a conservative floor.

### Adjustment flags (which adjustments are already inside `p_ratings_only_home`)
- `nfl_qb_adjusted` (NFL) / `cfb_qb_adjusted` (CFB): true when the core includes the starting QB's rating and ESPN's injury report was checked. A QB listed Out or Doubtful is replaced by a backup prior. NFL and CFB cores also apply a small capped adjustment for non-QB players listed Out or Doubtful.
- `nhl_goalie_adjusted`: true for every NHL game. The starting goalie's GSAx per start and a backup flag are core features. `nhl_goalie_source` says whether the goalie came from ESPN's probable starter or fell back to the team's last starter.
- `nba_availability_adjusted` / `nhl_availability_adjusted`: true when ESPN injury availability went into the game. In `p_ratings_only_home` it applies at full weight. In the published `p_allin_home` (market blend) only 25% of that shift is credited, because the market is assumed to have priced most injury news already.
- A flag is false when the adjustment doesn't apply to that sport or the injury check failed for that run.
- `weather_adjusted`: always false. ALL_IN applies no weather adjustment.

### Venue
- `venue`, `venue_city`, `venue_state`, `venue_country`, `indoor`, `neutral_site`, from ESPN event data, for all team sports.
- `venue_lat`, `venue_lon`: from ALL_IN's venue cache when the venue is in it (mostly NFL/CFB), otherwise null.
- `venue_source`: where the venue fields came from.
- Tennis: all null.
