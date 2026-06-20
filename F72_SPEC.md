# F72 — Recursive Curve Ownership Engine

## Paradigm
This is **not a phase machine**. It is a **Recursive Curve Ownership Engine**.

Price is not candles. Price is **curves inside curves**. Every curve — parent or
recursion — obeys the *same* lifecycle. A recursion is just another curve at a
smaller scale, possibly inverted relative to its parent. There are no special curve
types and no new nomenclature.

The engine's job is never "what phase comes next?" It is to continuously answer:

1. **Who owns price?** (which curve currently controls)
2. **Are we Building or Terminal?**
3. **How much curve remains?** (`remainingCurveBudget`)
4. **How many recursive cycles are physically possible?** (`expectedRecursiveDepth`)

Confusing *Building* with *Terminal* is where traders get liquidated. That
distinction is the whole game.

## The one lifecycle (applies recursively, at every scale)
```
Point 4 Origin → Expansion → Pre-Convexity → Induction → Liquidity → New High/Low
→ TRANSITION → Retracement → Retr Pre-Convexity → Retr Induction → Retr Liquidity
→ Demand/Supply Return → (Origin of the next curve)
```
The only differences between curves are **scale · orientation · available space**.

## Key principles
- **New High does not end the cycle.** After a New High, late participants enter,
  the internal trend breaks (Phase-2 CHoCH), and that spawns an *inverted recursive
  curve* inside the parent. Same physics, opposite orientation.
- **Recursions can recurse.** 1–4+ recursions may occur before the parent high
  finally transitions into retracement. Not special cases — just curves in curves.
- **Curve budget determines recursion depth.** There is no fixed count. Depth is
  constrained by geometry: remaining distance to the HTF target, compression,
  convexity width, velocity, time. Wide convexity → large loops; compressed →
  failure swing + small loops; tiny → immediate entry.
- **FU structures solve the hard human problem.** The indicator already maps FU
  candles, flip zones, supply/demand, imbalances across all timeframes — the thing
  a human cannot do by eye. That hard part is **done**.
- **Expansion side is easy.** From FU flip → HTF flip zone is "just expansion."
  Minor recursions are subordinate; the destination is known. Almost boring.
- **Terminal side is critical.** Inside the HTF flip zone: induction → counter-trend
  → recursive models → terminal sequence → supply/demand → new campaign. This is
  where entries are made or lost.
- **Compression only matters in terminal regions** (highs, lows, supply, demand, HTF
  flip zones) — it determines recursion size, count, and transition speed.
- **FU ownership can merge campaigns.** If a recursive curve reacts off and then
  *respects* the parent FU flip zone, ownership transfers back: Camp B collapses
  into Camp A — it was one continuous campaign, and the parent cycle still has
  unfinished work. Tracking this ownership transfer is critical.
- **Successful transition makes continuation near-certain.** After transition,
  price trends to the HTF flip zone with high probability. The hard question is not
  "will it trend?" but "have we already entered terminal behavior?"

## Priority hierarchy (build the engine top-down)
- **Level 1 — Campaign Ownership:** Expansion Campaign vs Terminal Campaign.
- **Level 2 — Location:** Building · Transitioning · Approaching HTF Zone · Inside HTF Zone.
- **Level 3 — Compression Regime:** Wide · Medium · Compressed · Failure Swing.
- **Level 4 — Recursive Depth:** 0 · 1 · 2 · 3 · 4+.
- **Level 5 — Micro Phases:** Origin/Expansion/Induction/Liquidity/Return — *least important.*

Micro phases are the bottom layer, not the driver.

## Master objective
Maintain a continuously-updating map of: curve ownership, campaign ownership,
recursive depth, transition maturity, remaining curve capacity, and terminal-sequence
development — so the machine can state whether price is **still building** or has
**already entered the entry (terminal) cycle.**

---

## Mapping F72 to signals the indicator ALREADY computes
| F72 concept | Existing signal in F16/F72 code |
|---|---|
| FU candles / flip zones | `f_fuPool` nodes · `flipTop`/`flipBot` · `_ft`/`_fb` per curve |
| HTF flip zone / target | higher-rung `se##_ft`/`se##_fb`; network attractor; FEZ |
| Supply/Demand | flip-zone extremes · network nodes |
| Compression Index | `_compIdx` (displacement + efficiency) |
| Convexity width | `_convScore` / `convSmooth` |
| Recursive depth | `_recBrk` (Phase-2 CHoCH counter, armed by pullback pivots) |
| Transition maturity | `_recDom` dominance transfer (completes at 50%) |
| Who owns price | canonical wave `_dir`/`_wdir` + which rung's curve contains price |
| Build vs Terminal | price approaching HTF flip zone (build) vs inside it (terminal) |
| Remaining curve budget | distance from price to the HTF flip zone / objective |

The hard part (structure discovery) is solved. The remaining work is **assigning
structure to the right stage of the campaign** — an ownership/location layer on top
of the existing FU/flip-zone/compression machinery, not another 14-phase rewrite.

## Proposed build order
1. **Level 1–2 (ownership + location):** a Campaign Ownership engine that classifies
   Expansion-Campaign vs Terminal-Campaign and Building/Transitioning/Approaching/Inside,
   from the canonical wave's relationship to the nearest HTF flip zone. Surface as a
   compact readout. (Low risk — additive, reuses existing signals.)
3. **Level 3–4 (compression + recursive depth + curve budget):** add
   `compressionRegime`, `expectedRecursiveDepth`, `remainingCurveBudget`,
   `transitionMaturity` as readouts.
4. **Terminal-side detail:** induction → liquidation → terminal recursion inside the
   HTF zone, gated by compression.
5. **Ownership-merge logic:** detect when a recursion respects the parent FU and
   collapse Camp B back into Camp A.
6. Micro-phase labels (current region engine) demoted to a subordinate, optional layer.


---

## Terminal Mechanics & Psychology (2022 refinements)

### Why transition happens — late-trend FOMO
Late in a trend, FOMO is universal: every participant piles into the move regardless
of who they are. That late, exhausted FOMO entry is what produces the **late Change
of Character** that turns the market. The edge is **countering the main move at that
exhaustion point**. (Sells were historically hard precisely because holding too long
ignored this — recognising late-trend FOMO → terminal CHoCH was the unlock.)

### The Four Shifts (canonical) — Wyckoff imprint
Transition points — **at highs AND at supply/demand** — always contain **FOUR shifts**.
This is the same psychological imprint Wyckoff drew: **Spring · Test · LPS1 · LPS2** =
four waves. Not five, not three — always four. So:
- The recursive transition cycle **targets 4 shifts** as its reference count.
- **Compression can compress it** (failure swing + fewer/smaller recursions), but 4 is
  the canonical target at both the high transition and the S/D transition.

### The flip zone is a LIVE environment (not empty range)
A range is a live battlefield — algos, discretionary traders, everyone interacting
with the curve. Within it:
- **FIB 61 / 70 / 78 = manipulation zones.** Expect **displacement** here (participants
  enter), but this is **NOT** where the main induction happens.
- **Main induction is always at the key S/R = the FLIP** — specifically the **LOWEST
  flip**. The engine must find the lowest flip.
- At the lowest flip → **terminal events**: induction liquidation + the terminal
  liquidation-wave cycle.
- Once price strikes supply/demand → **internal liquidities build** → **entry cycles**.
- **Entry-cycle behaviour depends on the convexity** of the move hitting into S/D
  (wide convexity → large terminal recursion; tight → failure swing + tiny recursion).

### Fluidity — no hard constraints
The market is fluid; do **not** gate on sessions or fixed times. Reasoning must be
fluid to capture ~95% of moves. Recursion timing is variable:
- At **extremely important points** the recursive cycle can complete in **minutes**
  (price doesn't hang about).
- **Typically ~3 hours** of price action at transitional points (both highs and S/D).
- Treat ~3h as a soft ceiling, not a rule.

### Implementation implications
- Recursive-transition target = **4 shifts** (high transition *and* S/D transition),
  compression-adjustable downward (failure-swing path).
- Terminal side must distinguish **FIB 61/70/78 manipulation/displacement** from the
  **flip induction**, and anchor induction to the **lowest flip**.
- Entry-cycle complexity is read from **convexity at S/D**, not a fixed sequence.
- No session/time gating; allow minutes-to-~3h recursive completion.
