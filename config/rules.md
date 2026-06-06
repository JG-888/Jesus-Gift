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
- Enter the short **just before the 09:30 ET open** (never before 09:29), anticipating the drop.
- If it looks like it will **pop up first**: wait, let it run, and short the **top** once the
  up-move ends. Do **not** short into the middle of an up-move.
- If it **drops immediately**: get in as fast as possible (no pre-drop pop is coming).
- If there's **no clear trend**: wait until one is identifiable.
- Do **NOT** use JesseG's "short half now, add half at the top of the first ascend" method — it
  has not worked in practice here. Wait for the top instead.
- **Expect a small pop:** the stock often ticks **up a little right after 9:30 before dropping**
  (~2 of 3 setups in the owner's experience). This is normal — do **not** cover into the pop; hold
  for the impatience fall (§7). Covering early on the pop is the main mistake to avoid.

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

## 7. Exit / taking profit — the "impatience fall"
- There are up to three morning flushes: **~9:50, ~10:00, and ~10:30 ET**. These are **approximate** —
  the whole cluster can land ~5–15 min **before _or_ after** those times ("around" them); it varies by day.
- **Aim to cover at the LAST flush (~10:30), which is typically the lowest point = maximum profit.**
- **Treat the 9:50 and 10:00 flushes as confirmation, not exit cues.** The owner usually does **not**
  cover there — a drop at 9:50, and again at 10:00, is a *good* sign the setup is working; keep
  holding for the last flush.
- **If the last flush runs late, keep waiting — it can delay beyond the usual variance.** The tell that
  it is still coming (just delayed) is that **the first two flushes (~9:50 and ~10:00) already happened.**
  If you saw those two, the last one is usually still coming — wait for it (this tell is a strong guide,
  not absolute). Having no time cap, the agent can wait it out rather than cover early.
- Behavior at a flush: if the trend is up it squeezes down; if down it squeezes up.
- **Tell for more downside:** if the stock drops then stalls **before** a flush, it is coiling to push
  **further** down at the flush — wait for that lower point.
- Do **not** set a lower cover limit — let it run to the bottom and capture the full move.
- When the **last flush (around 10:30)** arrives, **cover any still-open short and end the day.**
- If you **miss** the bottom, cover anyway. Once you cover, you are **done for the day** — no second position.

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
- **One** trade per day — once you cover, you are done.
- **Never** revenge-trade to win back a loss from a mistake or something unknown.

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
- For **now**: short-only, strict criteria, profit only from clean setups.
- **Later**, with more experience and confidence: extend the playbook (e.g., also go **long** when a
  name is clearly going up) and refine any rule — provided the change reliably increases profit.
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

_Provenance: built from the "JesseG TRADING" note (text + 10 chart images) plus a screen-recording of
the owner's chat with JesseG (reviewed 2026-06-04). That chat confirmed the criteria working 3 times —
2 of which popped up before dropping — and the discipline of waiting for the impatience fall rather than
covering early._
