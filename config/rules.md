# JesseG Strategy Playbook — Low-Float Parabolic Short ("First Red Day")

> Read this top to bottom before trading. It explains **how** to apply the strategy.
> Exact thresholds (gain %, volume/float, prices, times) live in `strategy.yml` —
> the single source of truth. This file is the judgment and procedure around them.
> Strategy taught by JesseG; reported to work ~80% of the time when **every** criterion is met.

## 1. The idea in one paragraph
We short small-cap stocks that have just had a violent parabolic run-up — 100%+ in a day on
enormous volume relative to their float — at the moment they roll over and momentum clearly shifts down — classically as they turn red, though it
also counts while the stock is still mid-drop (§4). After an
extreme run, the first red day tends to keep falling: a price correction JesseG calls the
"low-hanging fruit." We enter just before the open and cover at the morning "impatience" flush,
which is the lowest point of the morning.

## 2. Why it works (the edge)
When a stock trades huge volume on its spike day, a wall of buyers gets trapped near the top.
Later, those buyers sell into any bounce just to escape near break-even, so the stock struggles
to ever push back through that high-volume day's price. That old high-volume day becomes hard
overhead **resistance** — which is both *why* the stock falls and *where* we anchor our risk.

## 3. Pre-market workflow (every trading day, before 09:30 ET)
1. **Find gainers on finviz.com — BEFORE the open.** finviz resets after 09:30 and stops showing
   the prior day, so check it pre-market. List stocks up 100%+ the day before.
2. **Check float on knowyourfloat.com** (finviz and Yahoo also show it). knowyourfloat lists *several*
   float figures from different sources — **use the average across them (plus finviz), but drop outliers.**
   E.g., if 3 of ~4 figures cluster within ~5M and one diverges by tens of millions (say ~30M apart),
   exclude that one and average the rest. Apply the same averaging-minus-outliers to volume. You want
   day volume ≥ ~10× this float (more is better).
3. **Check the chart on finance.yahoo.com — use the 5-day ("5D") view.** It shows the prior day, how it
   closed, and the current pre-market in one frame. Look for the parabolic arc ("n" / inverted-U) that
   was already rolling over into the close — that rollover is the momentum shift that signals a
   continued drop at the open. Confirm float again under Summary → Statistics → Share Statistics → Float.
4. Keep only names that satisfy the required criteria in §4 and have a clear resistance level for risk.

> **Timeframes:** selection (steps 1–4) is done on **daily** charts (and Yahoo's 5-day view). Once you're
> trading, all intraday timing — entry, the small pop, the impatience flushes, and the cover — is read on
> the **1-minute chart in TradeZero.**

## 4. Selection criteria — required unless marked _(preferred)_ (exact numbers in `strategy.yml`)
- [ ] Up 100%+ the prior day (a lone ~90% standout can be a discretionary exception).
- [ ] Day volume ≥ ~10× float (7–8× occasionally usable; prefer ≥10×).
- [ ] Price above $0.50 (TradeZero cannot short cheaper).
- [ ] **A clear change of momentum (the price is correcting / rolling over).** This does **NOT**
      require the stock to be *literally red*. It is satisfied by **either** form:
      - **(a) Red on the day** — the parabola's full correction already happened during the **prior
        market day**, so it opens **red vs. yesterday** and keeps dropping (a bit less intensely). The
        clearer case — the true **"low-hanging fruit."**
      - **(b) Mid-downfall, not yet red** — an **unfinished** parabola still completing its down curve,
        continuing to drop **into / after the 09:30 open** (this was **STI**). Momentum has clearly
        shifted and the price is correcting, so it **qualifies** even though it isn't red yet (not called
        "low-hanging fruit"). **Do not reject a candidate just because it isn't literally red.**
- [ ] A clear **resistance level** exists from a prior high-volume day. **No resistance → do not
      short** (no safe risk; wait for it to peak and form one).
- [ ] _(Preferred, not required)_ Chart shows a parabolic arc **rolling over** into the close.
      A chart that has **already flattened/corrected in pre-market does NOT disqualify the trade** —
      it just **lowers the odds** (the move may be partly spent), so be **more attentive**. In the
      owner's experience these became near-breakeven days (a few cents profit, rarely a loss) after
      sitting the full session.

## 5. Entry
- **Default rule — once a setup qualifies (§4), short it at exactly 09:29 ET, before the open.** Put
  the position on at the 09:29 minute, anticipating the drop — every qualifying day. This is what you
  do **unless** a specific, experience-based read says otherwise (below). **Do not sit out waiting for
  confirmation** — waiting is the rare exception, never the routine; **when in doubt, take the 09:29
  entry.** This governs entry **timing only**: if **no** setup qualifies per §4, still stand down —
  never force a trade just to be in.
- **Experience-based exception (rare, NOT the default):** only when you have a specific read that the
  stock will **pop up first** instead of dropping, hold off at 09:29 and short the **top** of that
  post-open push once the up-move ends (never short into the middle of an up-move). The "wait until a
  clear trend shows" judgment is part of *this same* exception — it applies only when you're genuinely
  unsure it will drop immediately, **not** as a routine gate. If you do wait and it drops immediately,
  get in as fast as possible.
- Do **NOT** use JesseG's "short half now, add half at the top of the first ascend" method — it
  has not worked in practice here.
- **Expect a small pop:** once short, the stock often ticks **up a little right after 9:30 before
  dropping** (~2 of 3 setups in the owner's experience). This is normal — do **not** cover into the
  pop; hold for the impatience fall (§7). Covering early on the pop is the main mistake to avoid.

## 6. Stop / risk
- Set the cover-ceiling (stop) at the **second resistance level, not the first.** Price often pokes
  the first resistance but rarely reaches the second.
- **Trail it down as the trade works:** as the stock falls, move the cover-stop **down to the next
  lower support/resistance** — do this on every trade. It progressively cuts risk and locks in profit,
  and when the initial high-volume stop sits far above entry it gives a much tighter, safer invalidation.
- Risk is defined by **the resistance level, not a fixed percentage.**
- **A market halt/hold is NOT a forced exit.** JesseG has traded through halts profitably, so don't
  treat one as an automatic stop. But a halt tends to **kill momentum**, so **early on — before the
  agent has built its own experience — strongly consider banking any profit already made on a halt,
  even if it is before an impatience flush.** Refine this with experience.
- These levels may be refined with experience, as long as the change reliably increases profit.

## 7. Taking profit, re-entry & the spring bounce (long side)

There are **two** distinct ways out — keep them separate.

**(a) The by-principle lower limit — your reasonable profit target.**
- Hold a lower limit **"by principle"**: a price kept **in your head, NOT a limit order on the platform.**
  Never place a platform lower-limit order — keeping it mental keeps you in control and never caps the move.
- Set it at the **lowest place you reasonably expect _this_ share to reach**, judged from **its own chart**
  and your experience with it — usually low, but still **above the pre-spike base** (its price before the
  spike that built the mountain/parabola). Read it from the share's **own** chart on the **Yahoo Finance
  5-day ("5D") general view** (the same chart used for selection in §3) — never another share's chart.
- It is the point where the share **stops dropping but does not reverse.** When price reaches (or dips
  below) your by-principle low **and stalls there without reversing, take the profit** — be *reasonable,
  not greedy* (Ecclesiastes 5:10); don't hold out for a theoretical lower low that may never come.
- **A stall is not a reversal.** Price can pause (stop falling, or rising) for a while and still continue
  the same way — tell a stall from a true reversal by experience and close attention.
- An early flush (~9:50 / 10:00) that has **not** reached your by-principle low is usually just
  confirmation / coiling for **more** downside — keep holding; only take profit once the by-principle low
  is reached and stalls.

**(b) The exit time — the last hour of impatience.**
- The morning flushes cluster at **~9:50 / 10:00 / 10:30 ET** (approximate, ~5–15 min either side; the
  last can run late — if the first two happened, the last is usually still coming).
- The **exit time** is the **last hour of impatience**, when the share has **finished dropping and is
  clearly starting to reverse.** If you are still short then, cover — that is the **final** exit.
- Behavior at a flush: if the trend is up it squeezes down; if down it squeezes up.

**Re-entry — taking by-principle profit does NOT end the day.**
- After you take profit at your by-principle low, the day is **not over.** If the share then bounces up a
  little and you catch the **top of that small up-move before it falls again,** you may **short it again** —
  there are more chances to fall **as long as not all the impatience hours have passed.**
- This still honors **one trade per day**: you exited only to take *reasonable* profit (Ecclesiastes 5:10),
  not to close out the day. The day truly ends at the last-impatience reversal (b).
- If you **miss** your by-principle low or the last-flush bottom, cover anyway.

**The spring bounce — an optional LONG on the rebound (the long side).**
- After the short reaches its by-principle low and **reverses up**, that rebound is the **spring bounce** —
  released elastic energy after the drop. You may trade that up-leg **long (buy).**
- **Strongest when the low lands at an impatience hour** (e.g. ~10:00): you then get a window — up to the
  **next** impatience hour (~10:30) — for the bounce to run before that next hour flips it back down.
- **Long entry signal:** the reversal shows as **3–5 up-candles (3–5 min) on the 1-minute chart**, with
  price still **between the low and the original market-open price** → buy.
- **Long exit — the "high-limit-by-principle"** (the mirror of the by-principle low): the **original
  market-open price.** Sell when it is **reached or just surpassed.**
- After the long, the **next impatience hour** typically reverses it back down → **re-short** the new
  down-leg. The morning oscillates: down → bounce-up → down, between impatience hours, until the last one.
- Same discipline as the short: the high-limit is **mental** (no platform limit order), read from the
  share's **own** 5-day chart; take *reasonable* profit (Ecclesiastes 5:10). It still counts as **one
  trade per day** — you're managing the same name's morning move.

## 8. Position sizing
- The account is small for now → use about **half the account** (TradeZero's usable short
  buying power is ~half of capital).
- Shares are borrowed in **lots of 100**; borrowing costs a small fee, so borrow only what you can
  actually use — don't over-borrow.
- Size up like this **only when the required criteria are met** — the size is justified by the
  strategy's ~80% edge, nothing else.

## 9. Capital management
- Pay yourself **50%** of profits; reinvest **50%** to grow the account.
- Once the account **doubles, withdraw your original principal** and trade only with earned money.
- Trade only with money you have **no attachment to.**

## 10. Hard rules vs. guidelines

**Hard rules (non-negotiable):**
- **Never** place a trade unless the required criteria (see §4) are met.
- **Never** enter earlier than ~09:29 (the pre-open short).
- **Always** keep a resistance level as your risk; never short without one.
- **One** trade per day. Taking *reasonable* profit at your by-principle low and re-entering the same
  name's continued fall while impatience hours remain (§7) still counts as one trade; the day ends at
  the last-impatience reversal.
- **Never trade to "repair a wrong"** — don't chase back a loss from a mistake or the unknown with an
  impulsive trade; it isn't a qualifying setup.

**Guidelines (NOT hard — soften and refine with experience):**
- The **09:30–11:30 session is a guideline, not a limit.** It suits a human with limited hours; the
  agent has no time cap and may work past 11:30 once it has learned profitable improvements. Early on,
  lean toward wrapping up around 11:30. The real day-end anchor is the **last impatience flush
  (around 10:30)** — when it comes, cover any open short and end the day.
- A **halt is not a forced exit** (see §6): JesseG trades through them; early on, strongly consider
  banking profit on a halt since it tends to kill momentum.

## 11. Governance & evolution
- "Be what you want the Automatic to be before you make it automatic" — master the discipline
  manually and faithfully before trusting it to automation.
- Short remains the **primary** side; the long side is now **active via the spring bounce (§7)** — buying
  the rebound off the by-principle low. Keep selection strict; profit only from clean setups.
- **Keep evolving** with experience and confidence — refine any rule, provided the change reliably
  increases profit (and validate it with the setup/execution tracking in `SCHEMA.md`).
- This account's guiding context, in the owner's words:
  - "He who loves money will not be satisfied with money." — Ecclesiastes 5:10
  - "Look carefully then how you walk… making the best use of the time, because the days are evil." — Ephesians 5:15–16

## 12. Worked examples (all fit the pattern)
- **EQ (Equillium):** a prior 144M-volume day set resistance at 1.36; next session it pushed to
  1.25, failed at that resistance, was shorted ~1.24 with the stop at 1.36, and faded to ~0.90.
- **JFB:** ~5M float; 10M vol / +32% Fri 9/26, then 25.5M vol / +85% Mon 9/29; dropped at 9:30 the
  next morning (9/30).
- **BQ:** ~3M float; 49M vol / +234% Mon 9/29; dropped at 9:30 on 9/30.
- **YCBD:** ~3M float; 198M vol / +127% Mon 9/29; dropped ~20% on 9/30.

## 13. Glossary
- **Low-hanging fruit** — the **clearer** form of the momentum-shift criterion: the parabola's correction completed during the prior market day, so the stock opens **red** and keeps dropping. (A not-yet-red stock still mid-downfall also satisfies that criterion — see §4 — but isn't called low-hanging fruit.)
- **Impatience fall** — a morning flush where holders give up (~9:50 / 10:00 / 10:30 ET, approximate —
  ~5–15 min either side). The **last** one (around 10:30) is typically the lowest point and our cover
  target; earlier flushes are confirmation.
- **Black swan** — an irrational, extreme move (≈ +1000%+).
- **Sympathy stocks** — names that move because a related/leading stock is moving, not on their own news.
- **Spring bounce** — the up-leg rebound after the short's by-principle low reverses (released elastic energy); traded LONG up to the original market-open price. See §7.
- **High-limit-by-principle** — the LONG mirror of the by-principle low: the original market-open price, where you sell the spring-bounce long. A mental target, not a platform order.

_Provenance: built from the "JesseG TRADING" note (text + 10 chart images) plus a screen-recording of
the owner's chat with JesseG (reviewed 2026-06-04). That chat confirmed the criteria working 3 times —
2 of which popped up before dropping — and the discipline of waiting for the impatience fall rather than
covering early._
