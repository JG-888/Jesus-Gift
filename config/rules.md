# JesseG Strategy Playbook — Low-Float Parabolic Short ("First Red Day")

> Read this top to bottom before trading. It explains **how** to apply the strategy.
> Exact thresholds (gain %, volume/float, prices, times) live in `strategy.yml` —
> the single source of truth. This file is the judgment and procedure around them.
> Strategy taught by JesseG; reported to work ~80% of the time when **every** criterion is met.

## 1. The idea in one paragraph
We short small-cap stocks that have just had a violent parabolic run-up — 100%+ in a day on
enormous volume relative to their float — at the moment they roll over and turn red. After an
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
2. **Check float on knowyourfloat.com** (finviz and Yahoo also show it). You want day volume
   ≥ ~10× the float (more is better).
3. **Check the chart on finance.yahoo.com.** Look for the parabolic arc ("n" / inverted-U) that
   was already rolling over into the close — that rollover is the momentum shift that signals a
   continued drop at the open. Confirm float again under Summary → Statistics → Share Statistics → Float.
4. Keep only names that satisfy **every** criterion in §4 and have a clear resistance level for risk.

## 4. Selection criteria — ALL required (exact numbers in `strategy.yml`)
- [ ] Up 100%+ the prior day (a lone ~90% standout can be a discretionary exception).
- [ ] Day volume ≥ ~10× float (7–8× occasionally usable; prefer ≥10×).
- [ ] Price above $0.50 (TradeZero cannot short cheaper).
- [ ] Turned **red on the day** with a clear momentum shift.
- [ ] Chart shows a parabolic arc **rolling over** — NOT already flattened/corrected in pre-market
      (if it's already flat, skip; the move is spent).
- [ ] A clear **resistance level** exists from a prior high-volume day. **No resistance → do not
      short** (no safe risk; wait for it to peak and form one).

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
- Risk is defined by **the resistance level, not a fixed percentage.**
- **Exit immediately on any market halt/hold** — momentum is lost and the behavior is unknown.
- These levels may be refined with experience, as long as the change reliably increases profit.

## 7. Exit / taking profit — the "impatience fall"
- Cover at the **impatience flush**: the morning's lowest point and your maximum profit.
- Impatience times cluster around **9:50, 10:00, 10:30 ET** — approximate, not fixed (anywhere from
  ~15 min before to ~50 min after).
- Behavior at an impatience time: if the trend is up it squeezes down; if down it squeezes up.
- **Tell for more downside:** if the stock drops then stalls **before** an impatience time, it is
  coiling to push **further** down at the impatience time — wait for that lower point.
- Do **not** set a lower cover limit — let it run to the bottom and capture the full move.
- If you **miss** the impatience fall, cover anyway.
- After you cover, you are **done for the day.** Do not open another position.

## 8. Position sizing
- The account is small for now → use about **half the account** (TradeZero's usable short
  buying power is ~half of capital).
- Shares are borrowed in **lots of 100**; borrowing costs a small fee, so borrow only what you can
  actually use — don't over-borrow.
- Size up like this **only when every criterion is met** — the size is justified by the strategy's
  ~80% edge, nothing else.

## 9. Capital management
- Pay yourself **50%** of profits; reinvest **50%** to grow the account.
- Once the account **doubles, withdraw your original principal** and trade only with earned money.
- Trade only with money you have **no attachment to.**

## 10. Hard rules (non-negotiable)
- **Never** place a trade unless **every** criterion is met.
- **Never** trade before 09:29; only trade the **09:30–11:30** window.
- **One** trade per day — stop after the impatience fall.
- **Never** revenge-trade to win back a loss from a mistake or something unknown.
- **Always** keep a resistance level as your risk; never short without one.
- **Always** exit on a halt.

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
- **Low-hanging fruit** — JesseG's term for the high-probability short: a parabolic gainer correcting on its first red day.
- **Impatience fall** — the morning flush (~9:50 / 10:00 / 10:30 ET) where holders give up; the lowest point and our cover target.
- **Black swan** — an irrational, extreme move (≈ +1000%+).
- **Sympathy stocks** — names that move because a related/leading stock is moving, not on their own news.

_Provenance: built from the "JesseG TRADING" note (text + 10 chart images) plus a screen-recording of
the owner's chat with JesseG (reviewed 2026-06-04). That chat confirmed the criteria working 3 times —
2 of which popped up before dropping — and the discipline of waiting for the impatience fall rather than
covering early._
