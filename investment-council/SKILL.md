---
name: investment-council
description: Pressure-test an investment thesis, trade, position, or mispricing claim through a council of five orthogonal advisors — Bear, Bull, Base Rate Skeptic, Market Structure Analyst, Risk Manager — who analyze independently, peer-review anonymously, and return a verdict tuned to DECISION (take/size this position) vs ANALYSIS (is this asset mispriced). Trigger on "council this trade/position/thesis/investment," "investment council," "pressure-test/war-room/stress-test this position," and on capital-at-risk asks like "should I buy/sell/short X," "is X mispriced/cheap/a good trade," "what's wrong with this thesis," "sanity-check this trade," "help me size this." For single securities, funds, portfolios, and mispricing claims. NOT for macro commentary without a position, portfolio construction, or fund-manager due diligence.
---

# Investment Council

Five advisors calibrated for capital-at-risk decisions. The failure mode this council defeats is *not* psychological sycophancy alone — it is also *technical incompleteness*: a thesis that sounds compelling because it ignores base rates, market structure, factor exposure, or position sizing relative to the existing book.

This council is for institutional-grade thesis stress-testing — capital-at-risk decisions on a specific security, fund, portfolio, or mispricing claim. It is not for life, strategy, or business decisions, and not for fund-manager due diligence.

---

## CHANGELOG

**v1.7** — Peer-review readability + standalone-memo pass (adversarial QC audit, 2026-06-22). The governing principle: the council memo must **stand on its own** — a reader with neither the advisor responses nor the input research dossiers in front of them must still get the full picture. Edits confined to Step 3 (peer review), one line of the Step 4 synthesis prompt, the Step 5 output spec, and one Hard Rule. Council architecture — five advisors, blind independent review, randomized mapping, chairman synthesis, verdict tiers, both output files — untouched; still a single-run chat skill. Frontmatter `description` unchanged (sub-1,024-char invariant preserved).
- **Peer review is de-anonymized at presentation.** Generation stays blind (randomized A–E mapping preserved); in the *presented* transcript and HTML, Response A–E are replaced with the actual advisor names so the reader is never forced to hold a letter-to-advisor map in their head. Presentation-only — independence at generation is untouched.
- **Peer-review form norm (standalone-readability mandate).** Each review point must restate the claim it refers to, define every acronym at first use, and — critically — explain in plain English any fact or mechanic it leans on that lives only in the input dossiers. The trigger: a review line flagged "pledged shares" as a left-tail amplifier without ever stating what pledging is or why it bites, so a reader without the dossier could not evaluate it. Self-containment now takes priority over terseness; word target raised 200–250 → 250–350.
- **CIO-to-IC synopsis closes the peer review.** After the five reviews, a mandatory plain-English paragraph — no jargon, no acronyms, not reviewer- or letter-indexed — states what the cross-examination *collectively* established: the blind spots more than one reviewer flagged, the claim multiple reviewers independently elevated, and the single question the council left unresolved, in the register a CIO would use to brief an investment committee. Scoped to what the review established — explicitly **not** the chairman's verdict (no tier, no size, no recommendation).
- **Acronym discipline extended to the peer-review and synthesis prompts.** The advisor prompt already defined acronyms at first use; the review and synthesis prompts did not, which let "PW" (probability-weighted) reach the verdict undefined. Now enforced in all three.

**v1.6** — Two precision sharpenings from a backtest of an ADR (American Depositary Receipt) thesis run against the company's home-market listing (2026-06-21). Both close "named-but-not-delivered" / "asserted-without-stamp" slippage; neither changes architecture. Edits confined to the Position Context Block (two rows), one Hard Rule, and the DECISION verdict bullet. Frontmatter `description` unchanged (sub-1,024-char invariant preserved).
- **Short interest / borrow must be *stamped* even when immaterial.** The run correctly dismissed the name's borrow ("easy and cheap, no squeeze") but asserted it bare, with no grounding stamp. Now: outside the shorted/squeeze-prone class, no web-search attempt is required, but the borrow/SI characterization must carry a **stamp** — a number, or an explicit "immaterial / general-knowledge, not verified" tag. A bare "borrow is easy" is no longer acceptable. *(Deliberately worded as a stamp, not an attempt, so it does not trigger wasteful SI searches on every mega-cap.)*
- **The cost-basis tax note must be *delivered*, not merely named, on a TRIM/EXIT.** Both prior runs named "the tax-realization note" and never produced it. Now: on a trim/exit, state the **mechanical realized P&L** the action crystallizes (≈ (price − avg basis) × shares; flag that the exact figure depends on lot selection in IBKR) and whether it is a harvestable loss or a taxable gain. **Scope guard:** this is a realized-P&L flag plus a "check against your own tax treatment" pointer — NOT tax advice, NOT jurisdiction-specific rules, NOT wash-sale / holding-period / liability computation (cross-border treatment is the principal's to determine). The forward-sizing fence is unchanged.

**v1.5** — Two surgical findings from the first live run (a new-entry candidate, 2026-06-21). Edits confined to Step 1 (framing / Position Context Block), the Risk Manager advisor's data line, and the Hard Rules. Council architecture untouched; still a single-run chat skill. Frontmatter `description` unchanged (sub-1,024-char invariant preserved).
- **Short interest / borrow is now a must-attempt at framing** (Finding #1). The live run correctly *flagged* missing short interest but never *attempted* it, leaving the single most informative datapoint for a shorted name memory-not-verified. The connector does not expose SI/borrow, so web search (Ortex / Fintel / S3 / NASDAQ settlement) is the source; "unavailable" is now permitted only after a genuine attempt, never a silent memory-deferral. Added as a dedicated Position Context Block row routed to the Market Structure Analyst, and enforced as a Hard Rule.
- **Held/not-held read now explicitly surfaces whole-book overlap for the Risk Manager** (Finding #2). The positions call already returns the book; the run used it well (a put-as-partial-hedge read on an already-held thematic name, "you already own the theme"), but the spec under-described it. Codified: the read surfaces thematically/statistically adjacent holdings (names + weights) and routes them to the Risk Manager's correlation-to-existing-book mandate. **Qualitative** book-overlap is in scope; **computed** marginal risk (portfolio beta, MCR, quantitative concentration deltas) remains out of scope — the two out-of-scope statements were tightened to draw that line cleanly.

**v1.4** — Live-data grounding pass (IBKR connector). Surgical: edits confined to Step 1 (framing), the Market Structure advisor's data line, the DECISION verdict bullet, and the Hard Rules. Council architecture — five advisors, independence, peer review, synthesis, verdict structure, both output files — untouched. Remains a single-run chat skill (no Claude Code, no scheduling, no persisted state). Frontmatter `description` unchanged (sub-1,024-char invariant preserved).
- Price/52wk verification is now **connector-first**: `get_price_snapshot` for the live quote, 52wk range, prior close and distance-from-high (+ `get_price_history` for a trend read), each figure stamped real-time vs delayed. Web search is the price fallback only when the connector cannot resolve the instrument or the entitlement is absent, and remains the primary source for all non-price facts (catalyst, filings, borrow, short interest, news).
- Added a single normalized **Position Context Block**, built once at framing from read-only calls and appended to the framed question, so all five advisors read one common pre-digested baseline (independence off a shared digest, not five raw JSON reads). Carries: price + 52wk context; live options data (IV percentile, open interest, option volume) routed to the Market Structure Analyst; and a **held/not-held flag** with weight from one `get_account_positions` call, which auto-classifies new-entry vs existing-position.
- **Cost-basis / unrealized-P&L fence:** read only when held, and usable solely for the exit/trim tax-realization note and the thesis-invalidation check — never an input to forward sizing (disposition-effect guard, consistent with the anti-narrative check).
- **Book-level marginal risk (portfolio beta, MCR, correlation to book, concentration deltas) is explicitly out of scope** — a dedicated portfolio-impact pass, not this first-read skill. Sizing here is % of NAV; risk-unit sizing is deferred.
- Graceful degradation: with no IBKR connector present, price verification falls back to web search and the held-flag is skipped and noted — the skill still runs (publishable, account-agnostic).

**v1.3** — HTML report restyled to a dark, low-glare "Slate & Ember" theme (neutral graphite base, warm off-white text, a single warm-ember hairline accent — no blue, no pure black). Spec-only change to the Step 5 HTML output description; council architecture, advisor prompts, peer-review and verdict structure, and the Markdown transcript are all untouched. Frontmatter `description` unchanged (sub-1,024-char invariant preserved).

**v1.2** — QC-pass integration (adversarial audit). Closed a silent-failure gap and trimmed redundancy; architecture unchanged (single-run confirmed independently).
- Added a degraded-run rule: if live-verification cannot retrieve (search/tools off), flag all load-bearing facts as memory-not-verified and cap conviction below FAT PITCH rather than verifying from memory.
- Recalibrated the synthesis reasoning note for Opus 4.8: reasoning *method* is installed in the prompt; reasoning *depth* is a run-time setting, not an in-skill control.
- Added a length-pressure safeguard: protect advisor depth + synthesis; let the HTML report spill to an immediate follow-up rather than truncating analysis.
- Trimmed duplicate grounding/memory-flag instructions to the canonical advisor-prompt instance plus pointers.

**v1.1** — Capability-activation pass for Opus 4.8 (single-run architecture confirmed; staging rejected on evidence).
- Added mandatory live-verification of the thesis's load-bearing facts at framing (the council's highest-value demonstrated behavior, previously un-instructed).
- Reframed council convening as *independence in a single context* rather than literal parallel sub-agent spawning; real sub-agents now optional.
- Added a conviction-tiered verdict (fat pitch / favorable / fair / pass / avoid) with the fat-pitch tier gated on named conditions.
- Activated scoped extended reasoning at the chairman synthesis; made the anti-sycophancy / anti-narrative checks mandatory in the output.
- Empirical advisors (Base Rate Skeptic, Market Structure Analyst) now expected to ground figures in retrieved data and flag memory-vs-verified.
- Softened hard word caps to depth-targets; varied the Risk Manager opening; pruned orphaned workspace conventions; converted header to YAML frontmatter.

**v1.0** — Initial release.

---

## Trigger Conditions

**Mandatory triggers:** `council this trade`, `council this position`, `council this thesis`, `council this investment`, `investment council`, `pressure-test this trade`, `war room this thesis`, `stress-test this position`.

**Strong triggers** (when the question involves capital at risk, a security, a fund, a portfolio decision, or a mispricing claim): `should I buy/sell/short X`, `is X mispriced`, `is X a good trade`, `what's wrong with this thesis`, `sanity-check this trade`, `help me size this`.

**Do NOT trigger** on macro commentary without a specific position, on portfolio construction questions, or on fund manager evaluation.

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

4. **The Market Structure Analyst** — who's on the other side of this trade and why. Liquidity, positioning, flows, factor crowding, forced sellers/buyers, derivatives overhang. Asks whether the price is set by fundamentals or by structural pressure. Critical for any trade where the thesis is "cheap" — cheap relative to whom and why are they selling. When the Position Context Block carries live options data (IV percentile, open interest, option volume), read it directly rather than reasoning from memory — measured positioning, IV only and no Greeks. *(Grounding/memory-flag discipline per the advisor prompt.)*

5. **The Risk Manager** — position sizing, correlation to existing book, drawdown scenarios, time-to-stop-out, max acceptable loss, regret minimization. Does not care if the thesis is right; cares whether you survive being wrong. When the Position Context Block surfaces overlapping holdings, read them directly and argue concentration / correlation against the *actual* book (does this diversify or double down?) — qualitative overlap, not a computed portfolio beta. For decision questions, this advisor's output is load-bearing. *Vary the opening line run-to-run — let the survivability stance come through in the substance, not a fixed signature sentence.*

These create the right tensions for a capital-at-risk context: Bull vs Bear (direction), Base Rate Skeptic vs Bull/Bear (qualitative narrative vs prior), Market Structure vs Fundamentals (price formation), Risk Manager vs everyone else (survivability).

---

## Session Protocol

### Step 1 — Frame the Question

Bounded context scan (≤3 reads): look for any portfolio context file, recent trade notes, position-sizing rules, the user's existing book composition, any uploaded research or screen output.

**Live-verify the thesis's load-bearing facts (mandatory).** Before framing, verify the facts the verdict will turn on against present-day data — current price and distance from the relevant high/low, the key balance-sheet items (net cash/debt, share count), short interest / borrow, and any specific catalyst the thesis cites. Where a tool is available, ground them; where not, flag the figure as memory-not-verified. Correct any stale or wrong premise explicitly in the framed question (e.g., "thesis cites an entry near the prior high; the name now trades well below it, already through the stated stop; the headline cash balance is largely offset by convertible debt, leaving little net cash"). This verification is the single place the council most often adds value — do not skip it.

**Price facts are connector-first (IBKR).** Resolve the name with `search_contracts` (apply exact-symbol discipline), then take the live quote, 52-week range, prior close, and distance-from-high from `get_price_snapshot`, and a trend read from `get_price_history` (daily bars, `include_corporate_actions=true` so a split does not fake a gap, `outside_rth=false`). **Stamp every figure real-time vs delayed** — US-equity real-time is confirmed entitled; treat other asset classes as delayed / subscription-dependent until verified. Do NOT spend a web search on the name's price when the connector can serve it. Web search remains the primary source for all non-price facts — catalyst, filings, borrow / short interest, news — and is the price fallback only when the connector cannot resolve the instrument or the entitlement is absent (e.g., the published skill running without IBKR), in which case say so.

**Build the Position Context Block (mandatory).** Assemble one small, normalized fact table at framing and append it to the framed question, so all five advisors read a single common baseline (independence off a shared digest, not five separate JSON reads). Handle the connector landmines once, here, not five times. It carries, in plain rows with units and a per-figure real-time/delayed stamp:
- **Price & range** — live last, bid/ask, prior close, 52wk high/low, distance-from-high (from `get_price_snapshot`); a one-line trend note (from `get_price_history`).
- **Options surface** — live `implied_volatility_percentile` (a fraction, e.g. 0.94 = 94th pct), `option_open_interest`, `option_volume`, and underlying average option volume (from `get_price_snapshot` on the name). This row is routed to the Market Structure Analyst — measured derivatives positioning, not "the surface is probably…". Options data is **IV only; no Greeks** (compute from IV + spot + strike + T + r if a delta read is needed).
- **Short interest / borrow (positioning)** — *web-sourced; the connector does not expose this.* For shorted, squeeze-prone, or positioning-driven names, **attempt** current short interest (% float), borrow fee / availability, and days-to-cover at framing (Ortex / Fintel / S3 / NASDAQ settlement data, accepting the bi-weekly settlement lag). Routed to the Market Structure Analyst. "Unavailable" is acceptable **only after a genuine search attempt** and must be stated as such — never silently left memory-not-verified. (For a shorted name this is often the single most informative structural datapoint; do not skip the attempt.) **For every other name, no search is required — but the borrow/SI read must still carry a stamp** (a number, or an explicit "immaterial / general-knowledge, not verified" tag, e.g. "GC borrow, SI low — not verified to a number"). A bare "borrow is easy" assertion without a stamp is not acceptable.
- **Held / not-held flag + book overlap** — one read-only `get_account_positions` call returns the whole book. Use it two ways: (i) the **held/not-held flag** for the name auto-classifies the run as *new-entry* (not held) vs *existing-position* (held), with the user's stated direction resolving add vs trim vs exit — it stops the council from treating a name you already hold at, say, 3% as a clean new entry; (ii) surface the **existing holdings that overlap** the candidate thematically or statistically (names + weights) and route them to the Risk Manager's correlation-to-existing-book mandate (does this diversify or double down?). This is **qualitative** overlap from the positions already read — no extra calls, no price-history sweep; computed portfolio beta / MCR / concentration deltas remain a separate portfolio-impact pass.
- **Cost-basis fence** — if held, cost basis and unrealized P&L are read but **fenced**: they feed only the exit/trim tax-realization note and the thesis-invalidation check (is current price already through the stated stop?). They are **never** an input to forward sizing — embedded gain / loss must not drive size (disposition-effect guard). On a **TRIM or EXIT** decision, *deliver* the note rather than merely naming it: state the **mechanical realized P&L** the action crystallizes (≈ (price − avg basis) × shares transacted; flag that the exact figure depends on lot selection in IBKR) and whether it is a harvestable loss or a taxable gain. **Scope guard:** this is a realized-P&L flag plus a "check against your own tax treatment" pointer — NOT tax advice, NOT jurisdiction-specific rules, NOT wash-sale / holding-period / liability computation (cross-border treatment is the principal's to determine).

Every call in this block is read-only and "always allow" (`search_contracts`, `get_price_snapshot`, `get_price_history`, `get_account_positions`); no order tool is touched at any point in a council run. Book-level **computed** marginal risk — portfolio beta to a benchmark, marginal contribution to risk, quantitative correlation / concentration deltas — is **out of scope** for this first-read skill; it is a dedicated portfolio-impact pass run when you are seriously sizing. **Qualitative** book-overlap (which holdings the candidate concentrates against) is in scope and routed to the Risk Manager — see the held/not-held row.

**If retrieval is unavailable** (web search and the IBKR connector both off, or tools off), the run is *degraded, not blocked*: do not silently verify from memory. Flag every load-bearing fact as memory-not-verified, refuse to assert precise figures you cannot source, and lower the conviction ceiling accordingly — a FAT PITCH or other high-conviction verdict requires verified facts. If only the connector is absent (no IBKR), fall back to web search for price, skip the held/not-held flag, and note both — the council still runs.

Risk-free rate convention: use a consistent short-tenor risk-free rate as R_rf (the risk-free rate) — e.g., the compounded realized 1-month T-bill — and avoid mixing tenors within a single analysis. For high-beta or crypto-linked names, express **tail beta** = fund/asset loss ÷ benchmark loss in 5th-percentile stress periods, to the dominant risk factor.

Frame the question with: classification (DECISION / ANALYSIS / BOTH), the security or theme, the user's stated thesis, the verified present-day facts (with any corrections flagged), position sizing context if known, time horizon, and the relevant risk-free rate convention. Do not editorialize.

If the question is too vague to frame ("council this Tesla"), ask one clarifying question: *direction, horizon, sizing.* Then stop.

---

### Step 2 — Convene the Council (five independent advisors)

Generate all five advisor analyses from the *same framed question*, each one independent. **Independence — not literal simultaneity — is the goal.** No advisor conditions on, references, or is shown another advisor's response. If your runtime offers genuine parallel sub-agents, use them; if you are running in a single context, generate the five without letting an earlier advisor anchor a later one (do not summarize or carry one advisor's take into the next). Advisor identities are never revealed to each other.

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
- Acronym discipline: define every acronym at first use with a brief parenthetical
  (e.g., "FCF (free cash flow)").

Aim for roughly 200–400 words — depth over brevity. Do not truncate substantive analysis
to hit a count, and do not pad. No preamble.
```

---

### Step 3 — Anonymized Peer Review (five independent reviewers)

Collect all five responses. Relabel them Response A through E using a *randomized* mapping (do not preserve advisor order). Generate five reviews, each independent.

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

Roughly 250–350 words. Self-contained over terse — do not compress a point into shorthand a
reader without the responses could not decode, and do not pad.
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
found and why it matters. Keep it to ~120–180 words. This summarizes *what the review
established*; it is **not** the verdict and must not pre-empt the chairman — no conviction
tier, no position size, no recommendation (that is Step 4's job).

---

### Step 4 — Chairman Synthesis

The chairman receives: the original question, the framed question, all five de-anonymized advisor responses, all five peer reviews.

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

Before writing the verdict, reason carefully and at length through the full council: weigh
the strongest falsifiable claims against each other, resolve where advisors genuinely
conflict (do not average them), and decide which voice the evidence most supports. Take the
reasoning space you need — this adjudication is the highest-value step in the session. As
part of that reasoning, run the two checks below.

Then OPEN the synthesis with a brief "Internal checks run" note stating the outcome of both
(this is mandatory in the output, not optional):

1. Anti-sycophancy: did the council reflexively manufacture skepticism because being
   skeptical sounds smart? If the bull case actually clears the base rate bar and the
   structural story holds, say so. The user is an institutional investor, not a retail
   buyer who needs protecting from their own enthusiasm.
2. Anti-narrative: did the council rely on a compelling story rather than a quantitative
   bar? If the Bull and Bear are trading anecdotes while the Base Rate Skeptic is being
   ignored, weight the Base Rate Skeptic.

Acronym discipline: define every acronym at first use with a brief parenthetical (e.g.,
"probability-weighted (PW) fair value"); no bare initialisms anywhere in the verdict.

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
Who is on the other side. Whether the price is set by fundamentals or by forced flows /
positioning / factor crowding. Whether the borrow, options surface, and credit market
support or contradict the equity thesis.

## The Verdict
Lead with an explicit conviction tier: FAT PITCH / FAVORABLE / FAIR / PASS / AVOID.
Reserve FAT PITCH for the rare case where ALL of these hold: Bull and Bear converge on the
upside (or the downside is demonstrably capped), the base rate is supportive, there is a
structural floor (forced/anchored buyer, asset backing, balance-sheet support), the skew is
positive, AND the Risk Manager concurs that a *meaningful* survivable size is warranted (not
a token toe-hold). If any condition fails, the ceiling is FAVORABLE. Do not award FAT PITCH
to clear a low bar — but when the conditions are genuinely met, say so; a disciplined council
that can never reach high conviction is its own failure mode.

- For DECISION questions: the conviction tier, a clear recommendation (take it / add /
  pass / wait for trigger / trim), a specific position size as % of NAV, and a stop or
  invalidation level. (Marginal vol/factor contribution and book-level beta impact are
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
```

---

### Step 5 — Generate Outputs

Two files saved to the working directory:

- **`council-investment-transcript-[YYYYMMDD-HHMMSS].md`** — full transcript: original question, framed question with classification and the verified facts, all five de-anonymized advisor responses, all five peer reviews **presented with advisor names** (de-anonymized per Step 3 — letters replaced once the blind generation is complete), the **peer-review synopsis**, then the chairman synthesis. **This is the primary artifact.**

- **`council-investment-report-[YYYYMMDD-HHMMSS].html`** — a single self-contained HTML file with inline CSS. Clean, scannable: question and classification at top, chairman verdict prominent (including the conviction tier), a small payoff-asymmetry visual at the top for DECISION or BOTH questions (a horizontal bar showing bear / base / bull outcomes), collapsible sections for each advisor's full response, a collapsible peer-review section **that uses advisor names rather than letters**, the **peer-review synopsis rendered as a short, prominent, non-collapsed block** adjacent to that section (a partner skims it without expanding anything), footer with timestamp.

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
- The classification (DECISION / ANALYSIS / BOTH) must be stated explicitly in the framed question. Skipping this is forbidden.
- All five advisors run independently; no advisor sees another's response. The Risk Manager is not optional even on ANALYSIS questions — capital lives on the other side of every analysis.
- Quantitative claims required from every advisor. An advisor that produces only qualitative prose has failed the spec; the chairman must flag it.
- Position size must be expressed as % of NAV or in risk units (vol contribution, factor exposure), never in dollar amounts unless the user provided a portfolio size.
- Always randomize the anonymization mapping for peer review. Generation is blind; the mapping is revealed and the letters replaced with advisor names only in the *presented* output (transcript + HTML), never during generation.
- The chairman must surface the anti-sycophancy and anti-narrative checks in the output, is allowed and encouraged to disagree with the majority if the reasoning supports it, and may side with the user against the council if the anti-sycophancy check says so.
- FAT PITCH is gated: award it only when every condition in the verdict block holds. When they do, do not withhold it.
- Do not council questions that have one defensible quantitative answer (e.g., "what's the duration of a 10Y Treasury at 4%"). Just answer them.

---

## Notes for Claude

- This skill is **single-run by design** (architecture confirmed Jun 2026). Generate the full council — framing, five advisors, five peer reviews, synthesis, both output files — in one pass. Do not split it into staged turns: in a single chat context, staging would let earlier advisors anchor later ones and erode the independence that makes five distinct voices real.
- Independence is the load-bearing property of the advisor step. Protect it: do not let one advisor's text condition the next.
- The chairman synthesis is the highest-value adjudication step. Its reasoning *method* is already specified in the synthesis prompt (reason at length, resolve conflicts, do not average, run both checks) — follow it and give it real depth. Reasoning *depth* is governed by the run-time effort setting, not by any phrase here, so this skill is best run with high reasoning effort, web search enabled, and the IBKR connector available (read-only tools set to "always allow"); none of these can be set from inside the skill.
- Under length pressure, protect the load-bearing parts: the five advisor analyses and the chairman adjudication are non-negotiable. If a single run would otherwise truncate analysis, generate the HTML report as an immediate follow-up rather than thinning the council. (In practice, runs have not hit this ceiling — this is a safeguard, not a routine step.)
- Ground empirical claims (base rates, borrow, credit, options) in retrieved data where the tools exist; flag memory-vs-verified honestly rather than asserting false precision.
