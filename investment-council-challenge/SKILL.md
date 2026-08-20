---
name: investment-council-challenge
description: Pressure-test an investment thesis, trade, position, or mispricing claim through a council of five orthogonal advisors — Bear, Bull, Base Rate Skeptic, Market Structure Analyst, Risk Manager — who analyze independently, peer-review anonymously, and are then audited by a challenge round that resolves what the whole council missed before the chairman's final verdict. EXPLICIT INVOCATION ONLY: fires solely on "challenge council this", "council this with the challenge round", "run the challenge council", "investment-council-challenge", "challenge-council this". Do NOT fire on unqualified "council this trade/position/thesis", "investment council", "pressure-test this position", or capital-at-risk asks like "should I buy X" — those route to investment-council. NOT for macro commentary without a position, portfolio construction, fund-manager due diligence, or first-principles business understanding (use business-decoder).
---

# Investment Council — Challenge Round

Five advisors calibrated for capital-at-risk decisions, plus a challenge round that audits the chairman before the verdict is final. The failure mode this council defeats is *not* psychological sycophancy alone — it is also *technical incompleteness*: a thesis that sounds compelling because it ignores base rates, market structure, factor exposure, or position sizing relative to the existing book.

This council is for institutional-grade thesis stress-testing — not for life, strategy, or business decisions, and not for fund-manager due diligence. Use `business-decoder` for first-principles understanding of what a company does and how it makes money — that is the front-of-funnel lens, run *before* a thesis exists to pressure-test.

**This is the challenge-round variant.** It is a separate, parallel-installable skill: `investment-council` is unmodified and remains the default. This variant fires only on explicit invocation, so the two never collide and any output can be attributed to the skill that produced it.

---

## CHANGELOG

**v1.9** — Insider flow + technical setup made standing council responsibilities (four-stage maintenance review, 2026-08-20). Architecture re-examined against the added load and **re-confirmed SINGLE-RUN (CEILING)** — one primary signal fires (sequential dependency, satisfied in-context, not by staging), compression measured **absent** on a real run at 16% under the specified ceiling, and the HTML report confirmed rendering in-turn through v1.8. The escalation from a flat single-run verdict is honest: the new load consumes the Market Structure Analyst's remaining headroom, which is a routing problem solved by sending raw facts to the Position Context Block, not an architecture problem. Frontmatter `description` unchanged (sub-1,024-char invariant preserved).
- **The FAT PITCH gate required evidence the council had no instrument to gather (the headline defect).** The gate names a *structural floor — forced/anchored buyer* among its conditions, and nothing anywhere in the skill instructed anyone to look for one. On a real run the chairman did not merely omit it; he **affirmatively asserted** "there is no structural floor" and spent that assertion as one of three grounds to cap conviction, while a multi-billion-dollar accelerated share repurchase and an eight-figure open-market chief-executive purchase both existed at run time. A gate condition with no upstream instrument produces confident false denials. The limb is now **evidence-bound** to a Position Context Block row and must be adjudicated on stated grounds, never assumed absent.
- **A bid and a belief are different objects.** The structural-floor limb is satisfied by a **buyback or ASR in force** — a dated, mechanical absorber of supply — and **never by insider purchases alone**. A purchase that is 13% of an executive's own stake can be 0.01% of market capitalization; both figures are true at once and mean opposite things. The two are recorded separately in the Position Context Block and are never summed or merged. Conflating them is the exact channel by which two bullish-reading signals would inflate a tier.
- **Insider flow is gated, and the gate is a conjunction:** a **single listed common equity** AND a **long-direction thesis with capital at risk at or near current levels** (new entry, add, trim, or exit on a held long). The row is **three-state and never silent** — not-applicable with the gate reason, an explicit no-material-signal stamp *showing the attempt and the trailing-twelve-month window*, or the finding. A module that is silent when it has nothing bullish to say and voluble when it does is a bullish-bias module; the three-state stamp is what prevents that. **Persistence over twelve months outranks any single filing** — one purchase is close to noise; consistent buying in size by senior executives across a year is a materially different object.
- **The technical read is ungated** and costs no new calls — pattern, support/resistance, and the invalidation level are harvested from the `get_price_history` bars the skill already retrieves on every run, daily and weekly. Previously that call's entire specified output was "a one-line trend note." Feeds the Risk Manager's stop and add zones, and a **dual invalidation** (technical *and* fundamental) in The Verdict for DECISION and BOTH runs. **The One Trigger section stays singular** — its singularity is argued, not incidental, and dual invalidation lives in The Verdict instead.
- **The Risk Manager consumes Position Context Block rows, never the Market Structure Analyst's output.** Advisor independence (Step 2) forbids inter-advisor consumption, and the shared digest is the mechanism that already routes book overlap and options data. Routing the new rows the same way delivers the consumption without touching the load-bearing independence property.
- **Conviction symmetry added to the chairman's own checks.** v1.8 installed symmetry in the challenge round only — the layer that is blind, since it receives the bundle and cannot retrieve. The chairman, who can see everything, ran with two checks that hunted manufactured *skepticism* and narrative, and on a real run concluded the council's caution was "earned" when a later audit found genuine under-rated conviction. Check 3 tests whether caution is *paid for*, in whichever direction the evidence points, and applies in reverse to the two new bullish-reading signals.
- **Calibration sited inside each examination, not appended as a caveat.** Insider buying means *management believes the stock is undervalued* and does not resolve the thesis binary. Three motives coexist and are weighed rather than chosen between: genuine belief in undervaluation; signalling and price stabilization, cheap relative to the balance sheet; and anchoring or tunnel vision, the buyer being the person most committed to the strategy under question. Cutting the other way, a chief executive's read on industry trajectory is sometimes the best available precisely because of what they see. Insiders are frequently early and do catch falling knives. A bounce off a washed-out low is a **bottoming attempt**, not a confirmed reversal. Both signals adjust downside support and entry discipline — floors, stops, add zones, staging — and only rarely the tier.
- **Advisor word budgets differentiated, deliberately.** The uniform 400-word ceiling contradicted this skill's own designation of the Market Structure Analyst and Risk Manager as *leading* advisors on DECISION questions. Market Structure moves to floor 380 / ceiling 520; Risk Manager to floor 280 / ceiling 450. **The budget is headroom, not enforcement** — measurement shows self-reported word counts running 18–31% optimistic on reviews, and v1.8's target-to-floor conversion left four of five reviews still under floor. **Named required stamp lines are the enforcement**, because structural presence is verifiable in a way word counts demonstrably are not.
- **Peer review and the challenge round gain a coverage check, worded as confirmation and not discovery.** All three audit layers operate on the bundle and cannot retrieve; a gap originating in what was never fetched is invisible to every one of them. On the real run, five reviewers asked what all five advisors missed produced seven genuine collective misses and **not one was a retrieval gap**. The check therefore asks only whether the mandated stamps are present and non-perfunctory. An explicit Opus-5 carve-out protects both from being stripped under the "no verification steps" rule, alongside the existing carve-out for live verification.
- **The architecture decision now carries its own tripwire.** Single-run was asserted with a date and no invalidation condition. It now records what would reopen it: a measured compression signal, a rendered total approaching the ceiling, or a third mandatory examination in any one advisor slot. The HTML report is named as the **first** relief action under length pressure, ahead of thinning any council content — a valve deliberately retained and unspent.
- **Also:** `historical_vol` added to the options row so the surface is read implied *against* realized (same call, no extra cost — the model was already fetching it); sibling cross-references to `investment-council` made version-agnostic — a hard-coded sibling version number goes stale the moment the sibling is bumped, and the pointer carries no information the folder name does not.
- **Not changed, deliberately:** the five-advisor architecture and the other three advisor prompts, advisor and review independence, randomized blind peer review with de-anonymized presentation, the CIO synopsis, the 4a→4b→4c flow in one turn, "the challenger audits, never authors," exactly one challenge round, the mandatory resolution block, FAT PITCH reachability on a same-model challenge, the One Trigger section, every scope fence, the borrow/SI stamp discipline, the acronym and standalone-readability mandates, the Slate & Ember theme, and the Step 1 / Hard Rules redundancy.

**v1.8** — Challenge-round variant, recalibrated for Opus 5 (four-stage maintenance review, 2026-07-25). Forked from `investment-council` v1.7 as a **separate, parallel-installable skill** so both can run side by side for A/B comparison; v1.7 is untouched. Architecture re-confirmed **SINGLE-RUN** on re-examined premises — compression signal empirically absent across four exhibits (the terminal unbounded section is the longest in every run and grows with the others), no checkpoint wanted, and staging would multiply the independence seam rather than heal it. The challenge cycle therefore runs *inside* the single turn. Frontmatter `description` rewritten for explicit-invocation-only triggering (sub-1,024-char invariant preserved).
- **The peer-review loop is closed (the headline fix).** Step 3 asks all five reviewers what the *entire* council missed — and until now nothing consumed the answer: the synopsis is fenced from resolving it, the chairman's mandate was to resolve conflicts *between* advisors (which by construction cannot address what none of them raised), and no verdict section owned it. Measured across four real runs, ~20 collective-miss items were raised and 4 reached a verdict — an ~80% drop rate, including a reviewer's finding that a source dossier showed signs of fabricated precision, which would have discounted every number in the framed question. Step 4 is now **4a draft → 4b challenge → 4c final**, all in one turn. The challenge round's mandatory first job is to walk every collective-miss item and name the ones the draft leaves unresolved.
- **The challenge is conviction-symmetric.** It hunts *under-rated* conviction — a dropped structural floor, an under-priced asymmetry, a base rate more favourable than the Skeptic allowed — as explicitly as it hunts unearned conviction. A challenge briefed only to find excess optimism is a one-directional ratchet, which contradicts this skill's own guard that a council which can never reach high conviction is its own failure mode.
- **FAT PITCH remains reachable on this surface.** A same-model in-context challenge **counts as an executed challenge** for conviction purposes. Honest provenance stamping is retained as a *provenance* rule and never as a conviction cap — a same-model challenge is labelled as such and is never described as cross-model. *(Deliberate, documented deviation from the source proposal, which barred FAT PITCH on runs without a cross-model challenger — a condition permanently unmeetable in chat, which would have removed the top tier outright.)*
- **The challenger audits, never authors.** No verdict, no conviction tier, no position size, ever. The chairman may overrule it with stated reasoning, and a run that defers to the challenger by default is a failed run.
- **Review-step independence specified to the Step 2 standard.** A real run produced a reviewer opening "A *again*" — visible evidence of one review conditioning on another. Root cause was asymmetric specification: advisor independence got four sentences, review independence got one clause. Now equal.
- **Word targets converted from suggestions to floors and ceilings**, and the chairman synthesis — previously the *only* unbounded section, and the one instructed to run at length — is now capped. Across three runs, nine of ten reviews in the earlier versions and five of five in the latest sat *below* the stated floor, while the one section with a tight ceiling ran 68% over it. Opus 5 runs written deliverables long by default, which makes these constraints rather than guidance.
- **"What the Market Structure Tells You" is now adjudicative.** It was the shortest verdict section in every run and substantially restated the Market Structure Analyst, because it asked the chairman the same descriptive questions the advisor had already answered. It now asks what the structural read *changes about the decision*.
- **Opus 5 calibration, confined to one labelled section.** Depth exhortations removed (thinking is on by default; the reasoning *method* is what matters and is preserved verbatim); an explicit prohibition on adding verification or self-recheck steps; sub-agent delegation bounded to the five advisors, capped at five, never for verification or the challenge; the `≤3 reads` context cap lifted; and an explicit note that **the live-verification mandate is empirical grounding, not self-verification, and must never be stripped as an Opus-5 anti-pattern.**
- **Not changed, deliberately:** the five advisors and their prompts, advisor independence, randomized blind peer review with de-anonymized presentation, the CIO synopsis, the verdict structure and FAT PITCH gate, every scope fence, the borrow/SI stamp discipline, the acronym and standalone-readability mandates, the Slate & Ember theme, and the duplication between Step 1 and Hard Rules (redundancy in guardrails is cheap insurance).

**v1.0 – v1.7** — pre-fork lineage, carried by the parent. Everything before v1.8 is the history of `investment-council`, not of this variant: initial release; the capability-activation pass (live-verification at framing, conviction-tiered verdict, mandatory anti-sycophancy and anti-narrative checks); an adversarial QC integration (degraded-run rule, length-pressure safeguard); the "Slate & Ember" report theme; the live-data grounding pass that introduced the IBKR (Interactive Brokers) connector and the Position Context Block; two precision sharpenings on borrow/short-interest stamping and the cost-basis tax note; and the peer-review standalone-memo pass (de-anonymized presentation, restate-before-reference, the CIO-to-IC synopsis). The full design journal for those versions lives in [`../investment-council/SKILL.md`](../investment-council/SKILL.md) and is deliberately not duplicated here.

---

## Trigger Conditions

**Explicit invocation only.** This skill runs in parallel with `investment-council`. Triggering is never ambient — it fires only when the challenge round is named.

**Mandatory triggers:** `challenge council this`, `council this with the challenge round`, `run the challenge council`, `challenge-council this [trade/position/thesis]`, `investment-council-challenge`, `run the challenge variant`.

**Stay silent** on every unqualified council trigger — `council this trade`, `council this position`, `council this thesis`, `investment council`, `pressure-test this position`, `war room this thesis`, `stress-test this position` — and on unqualified capital-at-risk asks (`should I buy/sell/short X`, `is X mispriced`, `help me size this`). Those belong to `investment-council`. If the user's intent is ambiguous, do not guess and do not run both: ask which variant they want, in one line.

**Do NOT trigger** on macro commentary without a specific position, on portfolio construction questions, on fund manager evaluation, or on first-principles business understanding (use `business-decoder`).

---

## Decision vs Analysis — The Framing Fork

Before convening the council, classify the question:

- **DECISION question** — "Should I take this position?" The output must end with position-sizing guidance and a fit-with-existing-book check. The Risk Manager and Market Structure Analyst lead.
- **ANALYSIS question** — "Is this asset mispriced?" The output must end with a mispricing magnitude estimate (cheap/fair/rich, ideally with a sketch of fair value vs market price) and a list of what would have to be true. The Bear, Bull, and Base Rate Skeptic lead.

Some questions are both ("I think Costco is mispriced and I'm thinking of sizing it at 4% of the book"). Run both lenses; the chairman addresses both in the verdict.

State the classification in the framed question explicitly so all advisors know which mode they're in.

---

## The Five Advisors

1. **The Bear** — full short thesis. What kills this position. What the bull case is missing. Specific catalysts that mark the thesis broken. Required to commit to at least one falsifiable bear claim.

2. **The Bull** — full long thesis. Asymmetric upside. What the market is mispricing and why. What has to be true for the position to compound. Required to commit to at least one falsifiable bull claim.

3. **The Base Rate Skeptic** — quants out the qualitative story. What does the historical distribution say? What's the prior on companies/trades/themes that look like this? What's the hit rate on similar setups? Defaults to "the market is usually right" and forces the bull and bear to clear that bar. *(Grounding/memory-flag discipline per the advisor prompt.)*

4. **The Market Structure Analyst** — who's on the other side of this trade and why. Liquidity, positioning, flows, factor crowding, forced sellers/buyers, derivatives overhang. Asks whether the price is set by fundamentals or by structural pressure. Critical for any trade where the thesis is "cheap" — cheap relative to whom and why are they selling. When the Position Context Block carries live options data (IV percentile, open interest, option volume, and realized volatility), read it directly rather than reasoning from memory — measured positioning, IV only and no Greeks, and implied read *against* realized rather than implied alone. *(Grounding/memory-flag discipline per the advisor prompt.)*

   **Two examinations are mandatory in every run.** Both are read from the Position Context Block and never asserted from memory.

   - **Insider flow and the issuer bid — gated.** The gate is a conjunction: the subject is a **single listed common equity** AND the thesis is **long-direction with capital at risk at or near current levels** (new entry, add, trim, or exit on a held long). Outside the gate, stamp `insider flow: n/a` with the reason and move on. Inside it, weigh: **direction and type** — open-market purchase (Form 4 code **P**) versus sale (**S**), option exercise or award (**M/A**), a pre-set **10b5-1** plan, or a 401(k)/ESPP rebalance; **seniority** — chief executive, chief financial and chief operating officers carry the highest signal, directors less, junior officers least; **cluster versus isolated**; **persistence across a trailing twelve months** — a single purchase is close to noise, whereas consistent buying in meaningful size by senior executives across a year is a materially different object, and the trend matters more than any one filing; **size against two denominators** — the insider's own existing stake and compensation, *and separately* market capitalization, which frequently point opposite ways and must **both** be stated; **timing** against the drawdown, earnings blackout windows, and known catalysts. The **issuer's own bid** — buyback authorization, accelerated share repurchase (ASR) — is recorded as a **separate object** and never merged with insider activity: a repurchase is a mechanical, dated absorber of supply; an insider purchase is a statement of belief. Encode the asymmetry: open-market **cluster** buying by senior management is the high-signal case; selling is mostly noise unless unusual in size, timing, or concentration.
   - **Technical setup — ungated**, for any instrument with price history. Name things; do not gesture at them. Examine the **primary trend on daily and weekly bars** — is a bounce counter-trend, or a genuine reversal? — the **pattern, named** (double-bottom, base, breakdown, flag); **key support and resistance** and where price sits against them; the **volume signature** (was the low a forced flush or capitulation? is the bounce on rising or fading volume? did price reverse *on* the last bad catalyst?); and the **distance to the level that invalidates the setup**.

   **Calibration on both — mandatory, and it binds.** Insider open-market buying means *management believes the stock is undervalued*. It does not resolve the thesis binary. Three motives coexist and must be weighed rather than chosen between: genuine belief in undervaluation; signalling and price stabilization, which is cheap relative to the balance sheet; and anchoring or tunnel vision, the buyer being the person most committed to the strategy under question. Cutting the other way, a chief executive's read on industry and business trajectory is sometimes among the best available precisely because of what they see — and insiders are frequently early, and do catch falling knives. It is one more piece of evidence weighed against the whole picture, never a conclusion. Likewise, a bounce off a washed-out low is a **bottoming attempt**, not a confirmed reversal, until resistance clears or the fundamental binary resolves — technicals are aggregations of fundamental views and cannot confirm a turn the fundamentals have not. Both reads adjust **downside support and entry discipline** — floors, stops, add zones, staging — and only rarely the conviction tier itself. Neither is grounds to upgrade conviction, nor to pull a staged add forward ahead of an unresolved catalyst.

5. **The Risk Manager** — position sizing, correlation to existing book, drawdown scenarios, time-to-stop-out, max acceptable loss, regret minimization. Does not care if the thesis is right; cares whether you survive being wrong. When the Position Context Block surfaces overlapping holdings, read them directly and argue concentration / correlation against the *actual* book (does this diversify or double down?) — qualitative overlap, not a computed portfolio beta. **Consume the Position Context Block's structural rows directly — never another advisor's output; advisors are independent.** The insider/issuer row feeds the downside case and sizing: a **buyback or accelerated share repurchase (ASR) in force is a partial structural floor** — a dated, mechanical bid — whereas insider purchases are evidence of belief and are **not** floor arithmetic. The technical-levels row supplies the **stop or exit level** and any **add zones** (a retest of support that held is an add zone, not a warning). For DECISION and BOTH runs, the sizing output must reference the technical invalidation level explicitly. For decision questions, this advisor's output is load-bearing. *Vary the opening line run-to-run — let the survivability stance come through in the substance, not a fixed signature sentence.*

These create the right tensions for a capital-at-risk context: Bull vs Bear (direction), Base Rate Skeptic vs Bull/Bear (qualitative narrative vs prior), Market Structure vs Fundamentals (price formation), Risk Manager vs everyone else (survivability).

---

## Session Protocol

### Step 1 — Frame the Question

Context scan: look for any portfolio context file, recent trade notes, position-sizing rules, the user's existing book composition, any uploaded research or screen output. Read what the framing actually needs rather than rationing reads — stop when further reading would not change the framed question.

**Live-verify the thesis's load-bearing facts (mandatory).** Before framing, verify the facts the verdict will turn on against present-day data — current price and distance from the relevant high/low, the key balance-sheet items (net cash/debt, share count), short interest / borrow, and any specific catalyst the thesis cites. Where a tool is available, ground them; where not, flag the figure as memory-not-verified. Correct any stale or wrong premise explicitly in the framed question (e.g., "the thesis cites an entry well above the current price, which is already through the stated stop; the headline cash figure is a small fraction of that once convertible debt is netted"). This verification is the single place the council most often adds value — do not skip it.

**Price facts are connector-first (IBKR).** Resolve the name with `search_contracts` (apply exact-symbol discipline), then take the live quote, 52-week range, prior close, and distance-from-high from `get_price_snapshot`, and a trend read from `get_price_history` (daily bars, `include_corporate_actions=true` so a split does not fake a gap, `outside_rth=false`). **Stamp every figure real-time vs delayed** — US-equity real-time is confirmed entitled; treat other asset classes as delayed / subscription-dependent until verified. Do NOT spend a web search on the name's price when the connector can serve it. Web search remains the primary source for all non-price facts — catalyst, filings, borrow / short interest, news — and is the price fallback only when the connector cannot resolve the instrument or the entitlement is absent (e.g., the published skill running without IBKR), in which case say so.

**Build the Position Context Block (mandatory).** Assemble one small, normalized fact table at framing and append it to the framed question, so all five advisors read a single common baseline (independence off a shared digest, not five separate JSON reads). Handle the connector landmines once, here, not five times. It carries, in plain rows with units and a per-figure real-time/delayed stamp:
- **Price & range** — live last, bid/ask, prior close, 52wk high/low, distance-from-high (from `get_price_snapshot`); a one-line trend note (from `get_price_history`).
- **Technical levels** — harvested from the `get_price_history` bars already retrieved (**daily and weekly; no additional calls**): the **named pattern**, key **support and resistance**, where price sits against them, and the **level that invalidates the setup**. Trend, drawdown context, and the volume signature stay in the price row above. Raw levels only — interpretation belongs to the advisors. Routed to the Market Structure Analyst (read) and the Risk Manager (stop / add zones). Ungated: it runs for any instrument with price history.
- **Options surface** — live `implied_volatility_percentile` (a fraction, e.g. 0.94 = 94th pct), `option_open_interest`, `option_volume`, underlying average option volume, and `historical_vol` — all from the same `get_price_snapshot` call on the name, so the surface can be read **implied against realized** rather than implied alone. This row is routed to the Market Structure Analyst — measured derivatives positioning, not "the surface is probably…". Options data is **IV only; no Greeks** (compute from IV + spot + strike + T + r if a delta read is needed).
- **Short interest / borrow (positioning)** — *web-sourced; the connector does not expose this.* For shorted, squeeze-prone, or positioning-driven names, **attempt** current short interest (% float), borrow fee / availability, and days-to-cover at framing (Ortex / Fintel / S3 / NASDAQ settlement data, accepting the bi-weekly settlement lag). Routed to the Market Structure Analyst. "Unavailable" is acceptable **only after a genuine search attempt** and must be stated as such — never silently left memory-not-verified. (For a shorted name this is often the single most informative structural datapoint; do not skip the attempt.) **For every other name, no search is required — but the borrow/SI read must still carry a stamp** (a number, or an explicit "immaterial / general-knowledge, not verified" tag, e.g. "GC borrow, SI low — not verified to a number"). A bare "borrow is easy" assertion without a stamp is not acceptable.
- **Insider flow & issuer bid (gated)** — *web-sourced (Form 4 / SEC EDGAR); the connector does not expose this.* The gate is a **conjunction**: the subject is a **single listed common equity** AND the thesis is **long-direction with capital at risk at or near current levels** (new entry, add, trim, or exit on a held long). This row is **three-state and never silent**: (i) *gate not fired* → `n/a — [ETF / index / fund / basket / crypto | short thesis]`; (ii) *gate fired, nothing material* → an explicit **no-material-signal stamp that shows the attempt** — the source and the trailing-twelve-month window covered — never a bare conclusion; (iii) *gate fired, signal present* → the Form 4 detail (filer and role, transaction code, open-market vs. plan, date, size in shares and dollars, resulting change to the insider's stake) **and, recorded separately, the issuer's own bid** (buyback authorization / ASR, size and date, in force or completed). **Insider purchases and issuer repurchases are distinct objects and must never be summed or merged** — one is a statement of belief, the other a mechanical bid. Routed to the Market Structure Analyst (interpretation) and the Risk Manager (floor / sizing). A missing row, or state (ii) asserted without evidence of an attempt, is a defect the peer review and the challenge round are instructed to flag.
- **Held / not-held flag + book overlap** — one read-only `get_account_positions` call returns the whole book. Use it two ways: (i) the **held/not-held flag** for the name auto-classifies the run as *new-entry* (not held) vs *existing-position* (held), with the user's stated direction resolving add vs trim vs exit — it stops the council from treating a name you already hold at, say, 3% as a clean new entry; (ii) surface the **existing holdings that overlap** the candidate thematically or statistically (names + weights) and route them to the Risk Manager's correlation-to-existing-book mandate (does this diversify or double down?). This is **qualitative** overlap from the positions already read — no extra calls, no price-history sweep; computed portfolio beta / MCR / concentration deltas remain a separate portfolio-impact pass.
- **Cost-basis fence** — if held, cost basis and unrealized P&L are read but **fenced**: they feed only the exit/trim tax-realization note and the thesis-invalidation check (is current price already through the stated stop?). They are **never** an input to forward sizing — embedded gain / loss must not drive size (disposition-effect guard). On a **TRIM or EXIT** decision, *deliver* the note rather than merely naming it: state the **mechanical realized P&L** the action crystallizes (≈ (price − avg basis) × shares transacted; flag that the exact figure depends on lot selection in IBKR) and whether it is a harvestable loss or a taxable gain. **Scope guard:** this is a realized-P&L flag plus a "check against your own tax treatment" pointer — NOT tax advice, NOT jurisdiction-specific rules, NOT wash-sale / holding-period / liability computation (cross-border treatment is the principal's to determine).

Every call in this block is read-only and "always allow" (`search_contracts`, `get_price_snapshot`, `get_price_history`, `get_account_positions`); no order tool is touched at any point in a council run. Book-level **computed** marginal risk — portfolio beta to a benchmark, marginal contribution to risk, quantitative correlation / concentration deltas — is **out of scope** for this first-read skill; it is a dedicated portfolio-impact pass run when you are seriously sizing. **Qualitative** book-overlap (which holdings the candidate concentrates against) is in scope and routed to the Risk Manager — see the held/not-held row.

**If retrieval is unavailable** (web search and the IBKR connector both off, or tools off), the run is *degraded, not blocked*: do not silently verify from memory. Flag every load-bearing fact as memory-not-verified, refuse to assert precise figures you cannot source, and lower the conviction ceiling accordingly — a FAT PITCH or other high-conviction verdict requires verified facts. If only the connector is absent (no IBKR), fall back to web search for price, skip the held/not-held flag, and note both — the council still runs.

Conventions: use a short-tenor risk-free rate (R_rf) consistent with the horizon under analysis, and state which tenor you used. For high-beta or crypto-linked names, express **tail beta** = fund/asset loss ÷ benchmark loss in 5th-percentile stress periods, to the dominant risk factor.

Frame the question with: classification (DECISION / ANALYSIS / BOTH), the security or theme, the user's stated thesis, the verified present-day facts (with any corrections flagged), position sizing context if known, time horizon, and the relevant risk-free rate convention. Do not editorialize.

If the question is too vague to frame ("council this Tesla"), ask one clarifying question: *direction, horizon, sizing.* Then stop.

---

### Step 2 — Convene the Council (five independent advisors)

Generate all five advisor analyses from the *same framed question*, each one independent. **Independence — not literal simultaneity — is the goal.** No advisor conditions on, references, or is shown another advisor's response. If your runtime offers genuine parallel sub-agents, they may be used **for the five advisors only, one per advisor, capped at five** — never to verify, never to review, never to run the challenge round. If you are running in a single context (the normal case), generate the five without letting an earlier advisor anchor a later one (do not summarize or carry one advisor's take into the next). Advisor identities are never revealed to each other.

Each advisor receives this prompt:

```
You are [Advisor Name] on an Investment Council.

Your role: [advisor description from above]

The question is classified as: [DECISION / ANALYSIS / BOTH]

---
[framed question]
---

Respond from your role. Be specific and falsifiable. Hard requirements:

- Commit to at least one specific, quantitative claim (a number, a multiple, a probability,
  a position size, a stop level — something that can be wrong). No "consider valuation" platitudes.
- If your assigned role disagrees with the user's thesis, say so directly. The user did not
  bring this question to the council to be flattered.
- If your honest answer is "the thesis is correct and the market has it wrong for the reasons
  stated," say that. Reflexive skepticism is not the goal.
- Reference base rates, factor exposures, and market structure where relevant. This is an
  institutional council, not a retail forum.
- If you are the Base Rate Skeptic or Market Structure Analyst, ground your figures (base
  rates, hit rates, borrow, short interest, options skew, credit spreads) in retrieved or
  verified data where available; where you rely on memory, say so. Do not assert a precise
  base rate or borrow level you cannot source.
- If you are the Market Structure Analyst, your response must carry two **named stamp
  lines**: one for **insider flow** (the finding, or an explicit no-material-signal stamp
  showing the source and the trailing-twelve-month window searched, or an explicit
  not-applicable with the gate reason) and one for **technical levels** (named pattern,
  support/resistance, invalidation level). Structural presence is the requirement — a
  missing or perfunctory stamp is a failed response. The word budget below is headroom
  for these examinations, not the instruction to perform them.
- Acronym discipline: define every acronym at first use with a brief parenthetical
  (e.g., "FCF (free cash flow)").

Length: floor 200 words, ceiling 400 — with two role-specific budgets, set deliberately
rather than uniformly, because these two advisors carry mandated examinations the other
three do not and both lead on DECISION questions:
  - **Market Structure Analyst** — floor 380, ceiling 520 (the standard structural remit
    plus the insider-flow and technical-setup examinations).
  - **Risk Manager** — floor 280, ceiling 450 (consumes the two structural rows and must
    reference the technical invalidation on DECISION and BOTH runs).
These are limits, not suggestions. Do not truncate substantive analysis to reach the floor,
and do not pad to fill it. No preamble.
```

---

### Step 3 — Anonymized Peer Review (five independent reviewers)

Collect all five responses. Relabel them Response A through E using a *randomized* mapping (do not preserve advisor order). Generate five reviews, each independent.

**Review independence is held to the same standard as advisor independence (Step 2).** No reviewer conditions on, references, or is shown another reviewer's output. Each reviewer sees only the framed question and the five anonymized responses. A review that elevates a claim "again," or notes a point was "also flagged," has seen another review and has failed the spec — regenerate it. (Reviewers *do* see all five advisor responses; referring to those is correct and expected.)

```
You are reviewing the outputs of an Investment Council on this question:

---
[framed question]
Classification: [DECISION / ANALYSIS / BOTH]
---

Anonymized responses:

**Response A:** [response]
**Response B:** [response]
**Response C:** [response]
**Response D:** [response]
**Response E:** [response]

Answer these three questions. Reference responses by letter only — you do not know which
advisor produced which.

1. Which response makes the strongest specific, falsifiable claim, and what is that claim?
2. Which response has the biggest analytical blind spot — a missed factor exposure, an
   ignored base rate, an unaddressed liquidity issue, an unstated assumption? Be specific.
3. What did ALL five responses miss? Look especially for: regime risk (does the thesis only
   work in one macro regime?), correlation risk (would this position blow up at the same
   time as other things in a typical institutional book?), the "what would the smart short
   seller say" question, and what the borrow / options surface / credit market is signaling
   that the equity narrative is not.

Coverage check (mechanical — this is not a fourth question and not your own analysis).
Confirm that the structural response carries both mandated stamps, and report either as a
defect if absent: an **insider-flow stamp** (a finding, an explicit no-material-signal with
the window searched, or an explicit not-applicable with the gate reason) and a
**technical-levels stamp** (named pattern, support/resistance, invalidation level). A stamp
that is present but perfunctory — a bare conclusion with no evidence of an attempt — is also
a defect. You are checking that the examination *ran*; you are not performing it.

Form norm — write so the review stands on its own. This section is read by people who do
*not* have the responses or the input research dossiers in front of them:
- **Restate before you reference.** When you cite a response's claim, name the claim in a
  clause — not "B's blind spot is X" but "B treats the partnerships as de-risking, and the
  blind spot in that is X." A reader who cannot see Response B must still follow you.
- **Define every acronym at first use** with a brief parenthetical (e.g., "probability-weighted
  (PW) fair value"). No bare initialisms.
- **Explain any dossier-only mechanic in plain English.** If a point leans on a fact or
  mechanism that lives only in the input research — a share-pledge structure, a contract
  clause, a cohort base rate — state in one clause what it is and why it matters *before* you
  build on it. The test: someone reading only this peer-review section, with no dossier,
  still gets the full picture. Never name-drop a mechanic as if the reader already has it.

Length: floor 250 words, ceiling 350. These are limits, not suggestions. Self-contained over
terse — do not compress a point into shorthand a reader without the responses could not
decode, and do not pad.
```

**Presentation — de-anonymize.** The five reviews are *generated* blind, as above. When you
*present* them — in the transcript and the HTML report — replace Response A–E with the actual
advisor names now that the mapping is known (e.g., "Response B" → "The Bull"). The randomized
blind generation is preserved untouched; only the displayed output is de-anonymized, so the
reader is never asked to hold a letter-to-advisor map in their head.

**Peer-review synopsis (mandatory — closes this step).** After the five reviews, write one
plain-English paragraph in the voice of a CIO briefing an investment committee, stating what
the council's cross-examination *collectively* established: the blind spots more than one
reviewer flagged, the single claim multiple reviewers independently elevated as strongest,
and the one question the whole council left unresolved. No acronyms, no jargon, no letter or
reviewer indexing — a partner who reads only this paragraph understands what the peer review
found and why it matters. Ceiling 180 words — a limit, not a suggestion. This summarizes *what the review
established*; it is **not** the verdict and must not pre-empt the chairman — no conviction
tier, no position size, no recommendation (that is Step 4's job).

---

### Step 4 — Chairman Synthesis (4a draft → 4b challenge → 4c final)

**All three sub-steps run inside the same turn.** There is no checkpoint, no pause for user
input, and no separate chat. The chairman synthesizes twice: a draft that the challenge round
attacks, then a final that resolves the challenge. Everything upstream of this step — framing,
the five independent advisors, blind randomized peer review, the synopsis — is untouched by the
challenge round.

#### Step 4a — Draft verdict

The chairman receives: the original question, the framed question, the Position Context Block, all five de-anonymized advisor responses, all five peer reviews.

The draft is **internal**. It is not rendered in the transcript or the HTML report, and it is never presented to the user as the verdict. Its only consumer is Step 4b.

```
You are the Chairman of an Investment Council.

The question:
---
[framed question]
Classification: [DECISION / ANALYSIS / BOTH]
---

ADVISOR RESPONSES:
**The Bear:** [response]
**The Bull:** [response]
**The Base Rate Skeptic:** [response]
**The Market Structure Analyst:** [response]
**The Risk Manager:** [response]

PEER REVIEWS:
[all 5]

Before writing the verdict, work through the full council: weigh the strongest falsifiable
claims against each other, resolve where advisors genuinely conflict (do not average them),
and decide which voice the evidence most supports. As part of that, run the two checks below.

Then OPEN the synthesis with a brief "Internal checks run" note stating the outcome of both
(this is mandatory in the output, not optional):

1. Anti-sycophancy: did the council reflexively manufacture skepticism because being
   skeptical sounds smart? If the bull case actually clears the base rate bar and the
   structural story holds, say so. The user is an institutional investor, not a retail
   buyer who needs protecting from their own enthusiasm.
2. Anti-narrative: did the council rely on a compelling story rather than a quantitative
   bar? If the Bull and Bear are trading anecdotes while the Base Rate Skeptic is being
   ignored, weight the Base Rate Skeptic.
3. Conviction symmetry: is the caution in this draft *paid for*, or adopted because caution
   reads as rigour? Test the tier in whichever direction the evidence points, not only
   downward — excess skepticism is an analytical error exactly like excess optimism. Apply
   the test in reverse as well: if the draft leans bullish on the insider-flow or technical
   read, remember that those two signals adjust downside support and entry discipline —
   floors, stops, add zones, staging — and only rarely the tier itself.

Acronym discipline: define every acronym at first use with a brief parenthetical (e.g.,
"probability-weighted (PW) fair value"); no bare initialisms anywhere in the verdict.

Length: ceiling 900 words for the verdict, floor 700. A limit, not a suggestion — adjudicate
tightly rather than restating the advisors.

Now produce the verdict using this exact structure:

## Where Bull and Bear Converge
Rare and high-signal. If both the long thesis and the short thesis agree on a fact, that
fact is probably real. If they don't converge on anything, say so explicitly — that is
also signal.

## The Asymmetry
Sketch the payoff. Best-case scenario, base case, bear case, with rough probabilities or
at least relative ordering. Identify whether this is a positive-skew, negative-skew, or
symmetric setup. State expected value qualitatively even when precise numbers aren't available.

## What the Market Structure Tells You
Adjudicate, do not restate the Market Structure Analyst. Name the specific structural fact
doing the work — borrow, options surface, implied against realized volatility, credit, float,
forced flows, factor crowding, insider flow, the issuer's own bid, the technical setup and its
invalidation level — and
answer what it *changes about the decision*: does the structure support the fundamental case,
contradict it, or merely time-shift it (right thesis, wrong entry)? Say what it is worth in
decision terms: size now, wait, or express it differently. If the structure changes nothing
about the decision, say that explicitly — that is also a finding.

## The Verdict
Lead with an explicit conviction tier: FAT PITCH / FAVORABLE / FAIR / PASS / AVOID.
Reserve FAT PITCH for the rare case where ALL of these hold: Bull and Bear converge on the
upside (or the downside is demonstrably capped), the base rate is supportive, there is a
structural floor, the skew is positive, AND the Risk Manager concurs that a *meaningful* survivable size is warranted (not
a token toe-hold). If any condition fails, the ceiling is FAVORABLE. Do not award FAT PITCH
to clear a low bar — but when the conditions are genuinely met, say so; a disciplined council
that can never reach high conviction is its own failure mode.

**The structural-floor limb is evidence-bound and must be adjudicated, never assumed absent.**
It is satisfied by a *bid* — an authorized buyback or accelerated share repurchase (ASR) in
size and in force, another forced or anchored buyer, asset backing, or balance-sheet support —
and it must be cited to the Position Context Block's insider/issuer row. **Insider purchases do
not satisfy it.** They are evidence of management belief, not a mechanical bid: a purchase that
is 13% of an executive's own stake can be 0.01% of market capitalization, and both figures are
true at once. If the row reads `n/a` or `no material signal`, state that this limb fails and
on what basis — a bare assertion that no structural floor exists, made without consulting the
row, is a failed verdict.

- For DECISION questions: the conviction tier, a clear recommendation (take it / add /
  pass / wait for trigger / trim), a specific position size as % of NAV, and a **dual
  invalidation** where a technical level exists: the **price/technical trigger** (from the
  Position Context Block's technical-levels row) **and** the **fundamental trigger**, given
  as two separate lines. Where no technical level is meaningful, say so explicitly and give
  the fundamental trigger alone. (Marginal vol/factor contribution and book-level beta impact are
  out of scope for this first-read skill — express size as % of NAV here; reserve
  risk-unit sizing for a dedicated portfolio-impact pass.) On a **trim or exit**
  recommendation for a held name, deliver the mechanical realized-P&L tax note per the
  cost-basis fence (realized gain/loss crystallized, harvestable-loss/taxable-gain flag) —
  do not merely name it; keep it within the fence's scope guard (no tax advice).
- For ANALYSIS questions: cheap / fair / rich, with a rough magnitude (e.g., "10–15%
  rich on a 5-year normalized basis"), and the list of what would have to be true for
  the contrary view.
- For BOTH: address both.

You may side with the dissenting advisor if their reasoning is strongest. You may side
with the user against the council if the anti-sycophancy check says so.

## The One Trigger That Would Change Your Mind
A single, observable event or data point that would flip the verdict. This is the most
useful line in the entire synthesis — it tells the user what to monitor.

Stamp the draft `DRAFT — pending challenge`.
```

#### Step 4b — Challenge round

**Governing law: the challenger audits, never authors.** It produces no verdict, no conviction
tier, no position size, no recommendation — ever. The chairman remains the single authority over
the capital decision. This keeps one accountable synthesis and stops the run from silently
deferring.

The challenger receives one bundle and nothing else: the framed question and classification, the
Position Context Block, all five de-anonymized advisor responses, all five peer reviews, and the
draft verdict. It does **not** receive the chairman's or the advisors' internal reasoning — only
their delivered output.

**Provenance.** On this surface the challenger is an in-context pass by the same model under an
adversarial framing. Stamp it honestly in both outputs as `challenger: same-model (in-context)`.
It genuinely closes the loop; it does **not** deliver cross-model orthogonality, and must never
be described as though it does. **This stamp is a provenance record, not a conviction cap** — see
Hard Rules on FAT PITCH.

```
You are an external challenger to a completed investment council. You AUDIT; you do NOT author.
Produce no verdict, no conviction tier, no position size, no recommendation of any kind.

---
[framed question]
Classification: [DECISION / ANALYSIS / BOTH]
[Position Context Block]
---

ADVISOR RESPONSES: [all 5, named]
PEER REVIEWS: [all 5]
DRAFT VERDICT: [the 4a draft]

Return a structured challenge covering these five headings, in this order:

1. UNRESOLVED COLLECTIVE MISSES — your first and most important job. The peer review asked
   every reviewer what ALL FIVE advisors missed. Walk each item they raised. For each, state
   whether the draft verdict actually resolves it, engages it, or drops it. Name every one the
   draft drops. This is the single most common failure of this council: the collective-miss
   finding gets generated and then goes nowhere.
   Then, mechanically: confirm the structural response carried an **insider-flow stamp** and
   a **technical-levels stamp**, and that neither is perfunctory. A missing or bare stamp is
   a reportable defect. You cannot retrieve — you are checking that the mandated examination
   ran, not running it yourself.

2. DRAFT CLAIMS THAT DON'T SURVIVE SCRUTINY — load-bearing assertions in the draft verdict
   that the evidence in this bundle does not actually support. Name the claim, name the gap.

3. CONVICTION — BOTH DIRECTIONS. Test the tier symmetrically, and report whichever applies:
   (a) UNEARNED CONVICTION — the tier is not paid for by the evidence, especially a FAT PITCH
       or FAVORABLE leaning on narrative rather than a quantitative bar.
   (b) UNDER-RATED CONVICTION — the draft is more conservative than its own evidence warrants:
       a structural floor the draft dropped, an asymmetry it under-priced, a base rate more
       favourable than the Base Rate Skeptic allowed, a bull point that was never adjudicated,
       or caution adopted because caution reads as rigour. Excess skepticism is an analytical
       error exactly like excess optimism, and you are required to look for it.

4. COLLECTIVE BLIND SPOTS — what the entire council AND the chair share and therefore could
   not catch: a regime dependence none priced, a correlation the whole book carries, the
   "what would the smart short seller say" question no voice raised.

5. ADDRESSABLE RE-TASKS — for each gap that new advisor work could close, one specific
   instruction routed to a NAMED advisor (e.g., "Market Structure: pull days-to-cover; the
   draft asserts an easy borrow with no number").

Every point must be specific and falsifiable, in the same register the advisors are held to.
"Consider more risk" is a failed challenge. If a heading has nothing real, write "nothing
material" and move on — you are explicitly permitted to return a thin challenge. Manufacturing
a challenge to look thorough is a failure, not a success.

Acronym discipline: define every acronym at first use.

Length: ceiling 450 words, floor 350 — unless you are returning "no material challenge," in
which case be brief and say why.
```

#### Step 4c — Re-synthesis (final verdict)

The chairman receives the challenge and, **for each point, does exactly one of three things**:

- **Re-task** — dispatch the named advisor for a *bounded addendum*: a short, targeted response
  to that one point, not a full re-run. Fold the addendum into the final verdict.
- **Adjudicate directly** — resolve the point from material already in hand, no new advisor work.
- **Overrule** — reject the challenge with stated reasoning. **The chairman may overrule the
  challenger.** This is load-bearing: without it the run degenerates into deferring to the
  challenger, which violates "audits, never authors." A run that accepts every challenge point
  without adjudication has failed.

The chairman then emits the **final verdict** — same structure as 4a, no longer stamped DRAFT —
plus a mandatory closing block:

```
## Challenge Round
Challenger: same-model (in-context).
For each point the challenger raised: what it was, and how it was resolved — marked
**addressed** (re-tasked, with the addendum's finding), **adjudicated** (resolved from material
in hand, with the reasoning), or **overruled** (rejected, with the reason). If the challenge
moved the conviction tier in either direction, say so explicitly and say why.
Ceiling 250 words.
```

This block is the visible proof the loop did work and is the highest-signal paragraph in the
audit trail. It is mandatory even when the challenge returned nothing material — in that case it
records that fact, which is itself informative.

---

### Step 5 — Generate Outputs

Two files saved to the working directory. **Filenames are deliberately distinct from `investment-council`'s** (`council-investment-*`) so both skills can run against the same name without colliding, and so any artifact can be attributed to the skill that produced it:

- **`council-challenge-transcript-[YYYYMMDD-HHMMSS].md`** — full transcript: original question, framed question with classification and the verified facts, all five de-anonymized advisor responses, all five peer reviews **presented with advisor names** (de-anonymized per Step 3 — letters replaced once the blind generation is complete), the **peer-review synopsis**, the **challenge round in full** (the challenger's structured output), then the chairman's **final** synthesis including the mandatory Challenge Round resolution block. The internal Step 4a draft verdict is **not** included. Header carries the skill name, version, and challenger provenance stamp. **This is the primary artifact.**

- **`council-challenge-report-[YYYYMMDD-HHMMSS].html`** — a single self-contained HTML file with inline CSS. Clean, scannable: question and classification at top, chairman verdict prominent (including the conviction tier), a small payoff-asymmetry visual at the top for DECISION or BOTH questions (a horizontal bar showing bear / base / bull outcomes), collapsible sections for each advisor's full response, a collapsible peer-review section **that uses advisor names rather than letters**, the **peer-review synopsis rendered as a short, prominent, non-collapsed block** adjacent to that section (a partner skims it without expanding anything), a **Challenge Round block — also non-collapsed — rendered directly above the final verdict**, carrying each challenger point and its resolution (addressed / adjudicated / overruled) with the challenger provenance stamp in the metadata line, the full challenge text available in a collapsible beneath it, footer with timestamp. The Challenge Round block uses the existing ember-hairline idiom — no new accent colour.

  **Visual theme — "Slate & Ember" (dark, editorial).** Render the report dark and low-glare — no pure black, no blue:
  - Page base neutral graphite (`#161616`); card / panel surfaces `#202020`; expanded collapsible bodies `#262625`.
  - Primary text warm off-white (`#E8E6E0`); secondary / metadata text warm grey (`#94918A`); borders a single hairline of low-opacity white (`rgba(255,255,255,0.09)`).
  - The accent is one warm ember (`#DD8B5A`), used only as a hairline / outline — never as a large filled block: an ember-outlined conviction-tier badge (transparent fill, ember text), a thin ember rule under section titles, and a left-border that appears on a collapsible only when it is open.
  - Headings in a serif (`Georgia, Cambria, serif`); body in the system sans stack (`system-ui, "Segoe UI", sans-serif`).
  - Payoff-asymmetry bar: thin segments separated by hairline gaps with no outer frame, desaturated and warm — bear `#A85C50`, base `#5F5C55`, bull `#7F9468` — never saturated RGB.
  - Squared corners (~4px), generous spacing, restrained and calm. Fully self-contained inline CSS, no flash; respect `prefers-reduced-motion` and keep keyboard focus visible.

Open the HTML file after generating, where the runtime supports it, so the user sees it immediately.

---

## Hard Rules

- The thesis's load-bearing facts must be live-verified at framing and any correction stated explicitly. Skipping verification is forbidden. If retrieval is unavailable, do not verify from memory — flag all load-bearing facts as memory-not-verified and cap conviction below FAT PITCH.
- Price/52wk facts for the name are verified **connector-first** (`get_price_snapshot` for the quote/range/distance-from-high, `get_price_history` for trend), each figure stamped real-time vs delayed. Web search is the price fallback only when the connector cannot resolve the instrument or the entitlement is absent, and remains the primary source for all non-price facts. A single **Position Context Block** (price/range, live options IV percentile/OI/volume, short interest/borrow, held/not-held flag with weight + book overlap) is built once at framing from read-only calls and appended to the framed question. Cost basis and unrealized P&L are **fenced** — usable only for the exit/trim tax note and the thesis-invalidation check, never an input to forward sizing. Book-level **computed** marginal risk (portfolio beta, MCR, quantitative concentration deltas) is out of scope; **qualitative** book-overlap reasoning from the positions read is in scope and routed to the Risk Manager.
- For shorted, squeeze-prone, or positioning-driven names, short interest / borrow cost / days-to-cover must be **attempted** via web search at framing (the connector does not expose them) and routed to the Market Structure Analyst. "Unavailable" is permitted only after a genuine attempt — never a silent memory-deferral. For every other name, no search is required, but the borrow/SI characterization must still carry a **stamp** (a number, or an explicit "immaterial / general-knowledge, not verified" tag) — a bare assertion is not acceptable.
- **Insider flow and the issuer bid are examined on every qualifying run.** The gate is a conjunction: a **single listed common equity** AND a **long-direction thesis with capital at risk at or near current levels** (new entry, add, trim, or exit on a held long). The Position Context Block row is **three-state and never silent** — not-applicable with the gate reason, an explicit no-material-signal stamp showing the attempt and the trailing-twelve-month window searched, or the finding itself. Persistence across twelve months outranks any single filing. Insider purchases and issuer buybacks/ASRs are recorded as **distinct objects** and are never summed or merged. Routed to the Market Structure Analyst (interpretation) and the Risk Manager (floor / sizing).
- **The technical read is ungated** and runs for any instrument with price history, harvested from the `get_price_history` bars already retrieved (daily and weekly, no extra calls): named pattern, support and resistance, and the invalidation level. Routed to the Market Structure Analyst (interpretation) and the Risk Manager (stop / add zones). On DECISION and BOTH runs the sizing output must reference the technical invalidation, and the verdict must carry a **dual invalidation** — technical plus fundamental — where a technical level exists. **The One Trigger section remains singular**; dual invalidation lives in The Verdict.
- **Neither new signal is grounds to upgrade conviction.** Insider buying and a bounce off a washed-out low adjust downside support and entry discipline — floors, stops, add zones, staging — and only rarely the tier. The FAT PITCH structural-floor limb is satisfied by a **bid** (buyback/ASR in force, forced or anchored buyer, asset backing, balance-sheet support) and **never by insider purchases alone**; where the insider/issuer row is empty, the limb must be adjudicated as failing on stated grounds rather than asserted absent.
- The classification (DECISION / ANALYSIS / BOTH) must be stated explicitly in the framed question. Skipping this is forbidden.
- All five advisors run independently; no advisor sees another's response. **All five peer reviews likewise run independently; no reviewer sees another reviewer's output.** The Risk Manager is not optional even on ANALYSIS questions — capital lives on the other side of every analysis.
- Quantitative claims required from every advisor. An advisor that produces only qualitative prose has failed the spec; the chairman must flag it.
- Position size must be expressed as % of NAV or in risk units (vol contribution, factor exposure), never in dollar amounts unless the user provided a portfolio size.
- Always randomize the anonymization mapping for peer review. Generation is blind; the mapping is revealed and the letters replaced with advisor names only in the *presented* output (transcript + HTML), never during generation.
- The chairman must surface the anti-sycophancy and anti-narrative checks in the output, is allowed and encouraged to disagree with the majority if the reasoning supports it, and may side with the user against the council if the anti-sycophancy check says so.
- FAT PITCH is gated: award it only when every condition in the verdict block holds. When they do, do not withhold it.
- **The challenge round is mandatory and runs on the bundle only** — framed question, Position Context Block, the five advisor responses, the five peer reviews, and the draft verdict. The challenger never sees internal reasoning traces.
- **The challenger audits, never authors.** No verdict, no conviction tier, no position size, ever. The chairman **may overrule** the challenger with stated reasoning; deferring to the challenger by default is a failed run.
- **The challenge round's first mandated job is the unresolved collective misses** from peer review, and its conviction test is **symmetric** — under-rated conviction is reported as readily as unearned conviction.
- **Exactly one challenge round per run.** No second pass on this surface, regardless of what the first round surfaces.
- **The Challenge Round resolution block is mandatory in both outputs**, including when the challenge returned nothing material.
- **A same-model in-context challenge counts as an executed challenge for conviction purposes. FAT PITCH remains reachable.** The provenance stamp (`challenger: same-model (in-context)`) is an honesty requirement about *what ran*, never a cap on *what may be concluded*. A same-model challenge must never be described as cross-model. *(Distinct from the degraded-retrieval rule above, which caps conviction because facts are unverifiable — a missing cross-model challenger removes no verified fact.)*
- Do not council questions that have one defensible quantitative answer (e.g., "what's the duration of a 10Y Treasury at 4%"). Just answer them.

---

## Notes for Claude

- This skill is **single-run by design** (architecture re-confirmed Jul 2026 against Opus 5). Generate the full council — framing, five advisors, five peer reviews, synopsis, draft verdict, challenge round, final verdict, both output files — in one pass, with no checkpoint and no pause for user input. **The thing that must not be staged is the advisor step:** in a single chat context, splitting the five advisors across turns would let earlier ones anchor later ones and erode the independence that makes five distinct voices real. Step 4's internal draft → challenge → re-synthesis cycle is *sequential by design and runs inside the same turn* — the challenger must see the draft; that visibility is the mechanism, not a contamination. **What would reopen this decision** — record it so a future review does not re-litigate it from scratch, and does not leave it un-examined either: a measured compression signal in a real run; a rendered total approaching the specified ceiling; or the addition of a **third** mandatory examination to any single advisor slot. Absent one of those three, the verdict stands. *(v1.9 measurement, on a real run: rendered output ran 16% under the specified ceiling, with the Market Structure Analyst using 302 of its then-400 permitted words. The v1.9 load is projected to land ~10% under a ceiling that itself rises with the two revised advisor budgets.)*
- Independence is the load-bearing property of both the advisor step and the review step. Protect it in both: do not let one advisor's text condition the next, and do not let one review condition the next.
- The chairman synthesis is the highest-value adjudication step. Its reasoning *method* is specified in the Step 4a prompt (weigh the falsifiable claims, resolve conflicts, do not average, run both checks) — follow it. Run this skill with web search enabled and the IBKR connector available (read-only tools set to "always allow"); neither can be set from inside the skill.
- Under length pressure the protection order is: **the five advisor analyses → the chairman adjudication → the challenge round → the HTML report.** The **first** relief action is to generate the HTML report as an immediate follow-up turn rather than thinning any council content — the report is a rendering of the transcript, so deferring it costs presentation and not analysis. Only if that is insufficient does anything else give. (In practice runs have not approached this ceiling; through v1.8 the HTML rendered in-turn on every run. This is a named fallback, not a routine step.)
- Ground empirical claims (base rates, borrow, credit, options) in retrieved data where the tools exist; flag memory-vs-verified honestly rather than asserting false precision.

### Running on Claude Opus 5

All model-specific calibration lives here. A future recalibration should need to change nothing outside this section.

- **Thinking is on by default and `high` effort is the default.** Do not add depth exhortations ("reason at length," "take the space you need") to any prompt in this skill — they inflate output without improving adjudication. The reasoning *method* in Step 4a is the control that matters. Effort is a cost lever, not a quality dial to be turned up from inside the skill.
- **Do not add verification or self-recheck steps anywhere in this skill.** Opus 5 catches and fixes its own mistakes without being told to; added "double-check the resolution," "verify the challenge output," or "re-read before finalising" instructions compound with that behaviour, burn tokens, and do not improve results. The tempting place to add one is after Step 4c — do not.
- **The live-verification mandate at framing (Step 1, Hard Rule 1) is NOT self-verification and must never be removed as an Opus-5 anti-pattern.** It is empirical grounding of load-bearing facts against retrieval — the single highest-value demonstrated behaviour of this council. The Opus 5 guidance about removing verification steps concerns the model re-checking *its own output*, which is a different mechanic entirely. Preserve it, and preserve this note explaining why.
- **The Step 3 and Step 4b coverage checks are NOT self-verification and must never be removed as an Opus-5 anti-pattern.** They confirm that a *mandated examination ran* — that the insider-flow and technical-levels stamps are present and not perfunctory — which is a coverage checklist applied to delivered output by an agent other than the one that produced it. That is categorically distinct from a model re-checking its own work, which is what the prohibition above addresses. The distinction matters because the three audit layers in this skill (peer review, synopsis, challenge round) all operate on the bundle and **cannot retrieve**: a gap originating in what was never fetched is invisible to all of them, so the checks are worded as coverage confirmation rather than discovery. Preserve them, and preserve this note explaining why.
- **Written deliverables run long on Opus 5 by default.** Every word floor and ceiling in this skill is a constraint, not guidance. The chairman synthesis in particular is capped for the first time in this version precisely because it is the section most exposed to that drift.
- **Opus 5 delegates to sub-agents more readily.** Delegation here is permitted for one purpose only — the five advisors, one each, capped at five (Step 2). Never for verification, never for the peer review, never for the challenge round.
- **Opus 5 expands task scope more readily.** The scope fences in this skill are binding, not advisory: the cost-basis fence, computed marginal risk out of scope, the tax-note scope guard, and read-only tools with no order tool touched at any point.
