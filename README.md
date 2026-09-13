# Betting data feeds — measured capability

Real-time and historical betting-market data. Every number on this page is measured from running
production systems on **2026-09-13**; nothing is estimated. Figures that could not be measured are
labelled *not measured* rather than filled in.

Contact: **Telegram: @firsttick** · Pricing: **on request**

---

## Live feeds

| Feed | Bookmaker | Sport | Markets | Delay: price change → available to you |
|---|---|---|---|---|
| Tennis full market feed | Tipsport.cz | Tennis | Match winner · **player total aces (over/under)** · match total aces · **player total double faults** · match total double faults · who hits more aces · who hits more double faults | **median 3.6 s · p95 6.9 s · worst case 7.7 s** |
| Match-winner openings & closings | Tipsport.cz | Tennis | Match winner: opening price + every subsequent revision to closing | not measured |
| Multi-book price tape | Tipsport.cz · iFortuna.cz · Betano.cz | Tennis | Match winner, current + opening, three books side by side | not measured (recorded price points median 151 s apart) |
| Tennis listing feed | GoldBet.it | Tennis | Match winner at first listing, ATP + Challenger, singles & doubles | not measured |
| Tennis listing feed | Inbet | Tennis | Match winner at first listing | not measured |
| Tennis new-listing feed | Tipsport.cz | Tennis | Men's ATP + Challenger prematch listings | not measured |
| Tennis feed | bet365 | Tennis | Men's Grand Slam + Challenger, singles & doubles, 16 tournament streams | **dormant** — last data 2026-07-06 |
| Basketball feed | bet365 | Basketball | EuroLeague event listings | **listing-only** — no prices in the sampled window |

The player-prop markets (aces, double faults) are the part most feeds do not carry. They are
delivered with the same delay as the match-winner price, per line, per player, with the full ladder
— every line the bookmaker offers, both sides, with the opening price flagged.

Full breakdown and limitations: **`latency_report.md`** · raw values: **`latency.json`**

---

## Volume and coverage

| Feed | Volume on record | Window |
|---|---|---|
| Tipsport tennis full market feed | 272 matches tracked (194 ATP · 75 WTA · 3 Challenger-men) · **12,751 price rows** · 1,026 opening prices · 36 distinct lines · 5,536 market-state snapshots | 2026-09-05 → 2026-09-13 |
| Tipsport match-winner openings | **14,126 matches**, 12,664 with a recorded move after the opening (5,556 ITF-men · 5,496 ATP · 2,354 WTA · 686 Challenger-men; 12,123 singles · 2,000 doubles) | 2026-06-02 → 2026-09-13 |
| Multi-book price tape | **3,971,522 price points** (Fortuna 1,443,012 · Tipsport 1,431,923 · Betano 1,096,587) · 72,003 in the last 24 h | 2026-07-07 → 2026-09-13 |
| GoldBet.it tennis | **6,501 listings** · 341 in the last 7 days · 1,535 in the last 30 | 2026-06-25 → 2026-09-13 |
| Inbet tennis | **6,687 listings** · 474 in the last 7 days · 2,002 in the last 30 | 2026-05-31 → 2026-09-13 |
| Tipsport new-listing feed | 2,806 listing events · 2,864 distinct listings | 2026-07-23 → 2026-09-13 |
| bet365 tennis (dormant) | 222 matches · 9,920 price updates · 16 tournament streams | 2026-07-06 |

Per-market breakdown of the 12,751 price rows: player total aces 6,465 · match total aces 5,781 ·
match total double faults 184 · player total double faults 174 · who hits more aces 87 ·
who hits more double faults 60.

Full detail: **`coverage.json`**

---

## Historical datasets

| Dataset | Size | Span |
|---|---|---|
| **Tennis serve statistics** — aces, double faults, 1st/2nd serve splits, service points, per-set breakdown | **86,797 matches** · 173,594 player-match rows · 4,168 players · 1,552 tournaments | 2021 – 2026 |
| **Match-winner opening & closing prices** | **82,738 matches**, 80,863 with both an opening and a closing price (Tipsport 80,166 · Fortuna 650 · Betano 47) | 2022 – 2026 |
| **Settled player-prop ladders** — full ace / double-fault ladder with the bookmaker's own settlement | 32 matches · 1,130 ladder rungs | 2026 |
| **Match results** — winner, score, retirement and walkover flags, closing prices | 18,089 matches | 2021 – 2026 |

Serve-statistics quality, verified at 86,722 matches:
**99.40 %** carry parsed ace counts for both players; **90.96 %** are fully internally consistent;
set-level sums reconcile with match totals for **99.9959 %** of player rows; the service-point
denominator reproduces the source's own reported first-serve percentage for **99.987 %** of rows.

Coverage by tier and season (% of matches with serve statistics):

| Tier | 2021 | 2022 | 2023 | 2024 | 2025 | 2026 |
|---|---|---|---|---|---|---|
| ATP | 98.89 | 99.71 | 99.65 | 99.57 | 99.55 | 99.49 |
| Challenger-men | 95.38 | 99.77 | 99.83 | 99.70 | 99.67 | 99.74 |
| WTA | 98.77 | 99.50 | 99.57 | 99.67 | 99.56 | 99.62 |

Match-winner history by tier: Challenger-men 40,247 · WTA 16,404 · ATP 15,346 · ITF-men 5,211 ·
ITF-women 4,402 · Challenger-women 549.
By year: 2022 13,563 · 2023 15,032 · 2024 15,546 · 2025 15,002 · 2026 23,593.

---

## Uptime

Measured 2026-09-13 14:09 UTC.

- Host continuously up **107 days**; **0 restarts** in the last 7 days.
- Every live feed listed above was **active** at measurement time, with **0 unplanned restarts**
  since its current start.
- Current continuous runs: tennis market feed **22.1 h**; market capture and pricing **3.5 h**
  (redeployed the same morning); moneyline pipeline, multi-book tape, GoldBet, Inbet and bet365
  intake **33.5 h** each.

A full 7-day availability percentage was **not** reconstructed — see `latency_report.md §6`.

---

## Sample data

Redacted extracts are in `samples/`. Match identifiers are replaced by a one-way hash.

| File | Contents |
|---|---|
| `samples/ace_open_close.csv` | 20 ace / double-fault lines, opening price and closing price with UTC timestamps, number of recorded price rows, and the percentage move |
| `samples/moneyline_open_close.csv` | 20 match-winner openings and closings, both sides, with UTC timestamps and the move |
| `samples/ace_ladder_history_one_match.csv` | **1,067 rows** — the complete price history of every ace and double-fault line for one ATP match, from first listing to close |

---

## Other sports, bookmakers and exchanges

**Custom low-latency scrapers built on request for your sport, bookmaker or exchange.**

Tell us:

1. **Sport** — which sport or sports?
2. **Bookmaker / exchange** — which operator, and which country's site?
3. **Markets** — which ones? (match winner, handicaps, totals, player props, in-play, …)
4. **Required delay** — what delay from price change to your system is acceptable?
5. **Delivery format** — API, websocket push, CSV drop, database, or something else?
6. **History** — do you need a historical backfill, and how far back?
7. **Volume** — how many matches/events per day, and how many concurrent consumers?

Send the answers to **Telegram: @firsttick**. Pricing: **on request**.

---

*Draft — not for distribution until the items in `PUBLISH_CHECKLIST.md` are resolved.*
