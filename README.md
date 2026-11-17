# token-radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/mimittt16/token-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/mimittt16/token-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar for the make.fun token-radar-01 bounty: it reads a sealed DefiLlama protocol snapshot, ranks every pre-token protocol by an implied value, and hands in a static dashboard with a farm plan for each one.

## The model (radar-2)

Implied value follows only from the snapshot's own figures — no protocol name is special-cased, and the peer multiples are recomputed from whatever snapshot the program is given:

```
implied = round( tvl_usd x peer_multiple
                 x (1 + clamp(change_7d, -50, 100) / 100 x 0.5)
                 x (0.6 + min(age_days, 1095) / 1095 x 0.4)
                 x (1 + 0.03 x min(chains - 1, 5)) )
```

- **peer_multiple** — the median market-cap/TVL of the live-token protocols in the same category, computed from this snapshot. A category with fewer than 3 live peers is shrunk toward the global median (weight 3); a category with none uses it. Clamped to 0.02x–1.50x.
- **growth** — the protocol's seven-day TVL change, clamped so one wild week cannot decide a rank.
- **maturity** — how long the protocol has run with no token on record; an unlisted age uses 365 days.
- **chain reach** — up to +15% for protocols live on more chains.

The board covers the whole 40-protocol watchlist plus every other rankable pre-token protocol below the floor (198 rows on this snapshot). Wrapped, staked and bridged products of tokens that already trade are left out: they are not tokens that do not exist yet. Live-token protocols are set aside with their market caps shown, never mixed into the ranking.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
```

`main.ts` (Node 22, standard library only) writes `board.json`, `data.js`, `dashboard/`, `screenshots/` and `dossier.json` to the output directory. Both runs write identical bytes.

## Layout

- `main.ts` — the entry program: reads the snapshot, scores the board, writes the outputs and the dossier.
- `lib/model.ts` — the radar-2 model, the farm-plan generator and the snapshot parser.
- `vendor/makefun-board.ts` — make.fun's dossier library, copied from the kit unchanged.
- `dashboard/` — the static page: sortable/filterable board, per-protocol detail view, peer table and model panel. No network.
- `screenshots/` — three real renders of the dashboard: the board, a detail view and a filtered view.

---

<sub>Entered on [make.fun](https://make.fun/?ref=1xds55qg&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
