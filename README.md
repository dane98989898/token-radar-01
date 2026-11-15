# Token Radar — make.fun token-radar-01 entry

A radar for pre-token DeFi protocols: it ranks them by the implied value of a token
they may one day issue, and shows how to farm each one.

## Model (radar-2)

`implied = round(TVL x category_multiple x growth_mult x maturity_mult)`

- **category_multiple**: the median market-cap-to-TVL ratio of the category's tokenized
  peers in the same snapshot (market cap above zero, at least 3 peers). Too few peers
  falls back to the nearest neighbour category, or to the global median. Clamped to
  [0.05, 1.0]. The calibration table is computed from the snapshot at run time and
  printed in the dashboard's methodology section — no hardcoded prices.
- **growth_mult** = `1 + clamp(change_7d, -60, 120) / 100 x 0.5`
- **maturity_mult** = `0.6 + min(age_days, 1095) / 1095 x 0.4` (unknown age: 365 days)

The board covers all 40 watchlist protocols plus every rankable pre-token protocol
below the $50M floor. Live-token protocols are set aside with their market caps.

## Farm plans

Six steps each, derived from the protocol's own record: the action follows the
category, capital follows TVL ($250 / $150 / $100 / $50), gas follows the first
chain, the hold follows age (60 / 45 / 30 days). Every step states its cost and
its time.

## Run

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input ../kit/token-radar-01-kit/input/snapshot.json
```

Node 22+, standard library only, no network, deterministic: two runs write
identical bytes.

## Files

- `main.ts` — entrypoint: reads the snapshot, writes `board.json`, `data.js`,
  `dashboard/`, `screenshots/`, `dossier.json`.
- `lib/model.ts` — the model, the calibration, the farm plans, the board builder.
- `dashboard/` — the static page (index.html, app.css, app.js), no network.
- `screenshots/` — three real renders of the dashboard: the board, a detail view,
  a filtered view.
- `vendor/makefun-board.ts` — make.fun's dossier library, copied verbatim from the kit.
- `LICENSE` — MIT, as shipped by the kit.
