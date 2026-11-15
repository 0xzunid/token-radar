# token-radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/0xzunid/token-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/0xzunid/token-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar: it reads a sealed snapshot of the protocol universe, ranks every protocol that records no token by what its launch would be worth, and hands in a web dashboard with the board, a detail view for each protocol, and the steps to farm it. On this snapshot it scores 198 pre-token protocols — the watchlist's 40 plus 158 more the list leaves out, below its floor and above it — and sets aside 24 protocols that already have a live token. Entered by 0xzunid, built by agent Muse for the make.fun `token-radar-01` competition.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
```

Node 22, standard library only. Reads the snapshot, writes `board.json`, `data.js`, the dashboard and the screenshots into the output directory, and `dossier.json` last. The snapshot is the kit's sealed `input/snapshot.json`.

## The model (radar-2)

```
implied = round( tvl_usd x sector(category)
  x (1 + clamp(change_7d, -50, 150) / 100 x 0.6)
  x (0.7 + min(age_days, 1460) / 1460 x 0.5)
  x (1 + 0.02 x min(chains - 1, 5)) )
```

- **Candidate set:** every protocol that records no token (no market cap, no CoinGecko id, no symbol) and is not a wrapped, staked or bridged product of one that trades, not an exchange, and not a versioned pool. The watchlist's own rule, without its TVL floor: 198 rows here.
- **Sector multiple:** what a sector's token is worth per dollar of TVL it holds — derivatives and lending richest, bridges and payments thinnest. Keyed by the snapshot's category field, never by a protocol's name.
- **Growth:** the protocol's seven-day TVL change, clamped, weighted 0.6.
- **Maturity:** how long it has run with no token, from the day it was first listed; an unlisted age uses 365 days.
- **Breadth:** a small bonus for protocols live on more chains.

Implied value is the radar's own estimate, not a price, and not a prediction that a token is issued. "No token" means the protocol records no token in the snapshot; the record is not proof it has none.

## Farm plans

Each row carries 8 steps derived from the protocol's own record (category, chains, TVL, age): a fresh wallet, the category's action with its capital and the chain's gas estimate, a second action on another chain, a weekly qualifying action, a hold period, a points-page check, an exit plan, and a farm log. Gas is priced for the protocol's first chain and stated as an estimate.

## Layout

- `main.ts` — the entrypoint: snapshot in, board and dashboard out.
- `lib/model.ts` — the model, the board, the farm plans, the snapshot parser.
- `vendor/makefun-board.ts` — the make.fun dossier library, as the kit ships it.
- `dashboard/` — the static page (index.html, app.css, app.js). No network.
- `screenshots/` — the board, a detail view, and a filtered view.

---

<sub>Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
