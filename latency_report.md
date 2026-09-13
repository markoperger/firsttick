# Latency report

All figures measured **2026-09-13**. Raw values in `latency.json`.
Nothing in this document is estimated from experience or extrapolated — every number is read from
production data, and every figure that could **not** be measured is marked `not measured`.

---

## Headline

| Feed | Bookmaker | Sport | Delay: price change → available to a client |
|---|---|---|---|
| Tennis full market feed (incl. player props) | Tipsport.cz | Tennis | **median 3.6 s · p95 6.9 s · worst case 7.7 s** |
| Match-winner openings & closings | Tipsport.cz | Tennis | not measured (see below) |
| Multi-book match-winner tape | Tipsport.cz, iFortuna.cz, Betano.cz | Tennis | not measured; recorded price points **median 151 s apart** (Betano 165 s) |
| Tennis listing feed | GoldBet.it | Tennis | not measured |
| Tennis listing feed | Inbet | Tennis | not measured |
| Tennis feed | bet365 | Tennis | not measured — feed dormant since 2026-07-06 |
| Basketball feed | bet365 | Basketball | not measured — events tracked, no prices in the sampled window |

---

## 1. Tipsport tennis full market feed

**What it carries:** match winner plus the full player-prop set —
player total aces (over/under), match total aces, player total double faults,
match total double faults, who hits more aces, who hits more double faults.

**Measured delay, price change → available to a client:**

| | seconds |
|---|---|
| median | **3.6** |
| p95 | **6.9** |
| worst case | **7.7** |

Timing sample: **n = 27** individual end-to-end timings taken over a **265 s** window
(2026-09-13 14:04:00Z – 14:08:25Z), on top of **5,536** recorded market-state changes over a
**42.3 h** window (2026-09-11 19:51:43Z – 2026-09-13 14:07:00Z).

Component timings actually observed within that chain (milliseconds):

| component | n | min | median | p95 | max |
|---|---|---|---|---|---|
| feed read duration | 27 | 388 | 535 | 797 | 835 |
| read complete → row available to a client | 27 | 578 | 875 | 1752 | 1857 |

**Honest limitation.** Tipsport does not stamp its own prices with a server-side publication time.
The segment from "Tipsport publishes the change" to "we first read it" is therefore **bounded at
≤ 5 s**, not directly observed; every other segment above is directly measured. The headline
median/p95/worst-case combine the bounded segment with the measured ones, so the true delay is at
or below these figures, never above them.

**Change density:** in the 42.3 h window, a recorded market-state change landed every
**25 s (median)**, p95 60 s, max 75 s — i.e. the market itself moves frequently enough that the
delay figure above is the binding constraint, not the change rate.

---

## 2. Tipsport tennis match-winner openings and closings

**Delay: not measured.** This feed is stored as one opening price plus a running latest price per
match, not as a timed tape, so a per-change delay cannot be derived from it.

What *is* measured: **14,126 matches** carry an opening price, of which **12,664** have a recorded
price revision after the opening. Window 2026-06-02 → 2026-09-13.

---

## 3. Czech multi-book match-winner tape (Tipsport, Fortuna, Betano)

**Delay: not measured.** Only the interval between successive recorded price points was measured.

Gap between recorded price points, last 6 h (seconds):

| Bookmaker | n | min | median | p95 | max |
|---|---|---|---|---|---|
| Tipsport.cz | 9,740 | 5 | 151 | 1,433 | 3,550 |
| iFortuna.cz | 9,732 | 5 | 151 | 1,433 | 3,550 |
| Betano.cz | 2,265 | 74 | 165 | 1,508 | 1,572 |

**3,971,522** price points on record overall; **72,003** in the last 24 h; **180** matches priced in
the last 6 h.

---

## 4. GoldBet.it and Inbet tennis listing feeds

**Delay: not measured** for either. Both record a first-seen timestamp per listing with no
publish-side reference to compare against, so no delay figure exists.

| Feed | Events total | Last 7 d | Last 30 d | First → last |
|---|---|---|---|---|
| GoldBet.it (ATP + Challenger, singles & doubles) | 6,501 | 341 | 1,535 | 2026-06-25 → 2026-09-13 |
| Inbet | 6,687 | 474 | 2,002 | 2026-05-31 → 2026-09-13 |

Both were live and heartbeating at measurement time (2026-09-13 14:03Z).

---

## 5. bet365

**Tennis — built but dormant.** Last data **2026-07-06 22:07:25Z** (≈ 69 days before measurement).
While running it covered **16 tournament streams** (men's Grand Slam + Challenger, singles and
doubles), captured **222 matches** and received **9,920** price updates within a single 7 h window
on 2026-07-06. No current timing sample exists, so **no delay is claimed**.

**Basketball — running, listing-level only.** At measurement time it tracked **10 EuroLeague
events**, refreshing coupons every **15 s** and the index every **75 s**, but **0 priced rows** were
present in the sampled window. No price-delay figure exists for this feed.

---

## 6. Uptime

Measured 2026-09-13 14:09Z.

- Host: continuously up **107 days** (since 2026-05-28); **0 host restarts** in the last 7 days.
- Every live feed listed above was **active** at measurement time with **0 unplanned restarts**
  since its current start.
- Current continuous runs: tennis market feed **22.1 h**; market capture and pricing layer
  **3.5 h** (redeployed 2026-09-13 12:39 CEST); moneyline pipeline, GoldBet, Inbet, multi-book tape
  and bet365 intake **33.5 h** each.
- Process starts recorded in the last 7 days (deployments included, **not** a failure count):
  tennis market feed 4, market capture 10, pricing layer 21, moneyline pipeline 1, GoldBet 1,
  Inbet 1, bet365 intake 2.

**Honest limitation:** a full 7-day availability percentage was **not** reconstructed. What is
stated is the current continuous run plus the count of starts in the window.
