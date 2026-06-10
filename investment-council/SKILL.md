---
name: investment-council
description: Pressure-test an investment thesis, trade, position, or mispricing claim through a council of five orthogonal advisors — Bear, Bull, Base Rate Skeptic, Market Structure Analyst, Risk Manager — who analyze independently, peer-review anonymously, and return a verdict tuned to DECISION (take/size this position) vs ANALYSIS (is this asset mispriced). Trigger on "council this trade/position/thesis/investment," "investment council," "pressure-test/war-room/stress-test this position," and on capital-at-risk asks like "should I buy/sell/short X," "is X mispriced/cheap/a good trade," "what's wrong with this thesis," "sanity-check this trade," "help me size this." For single securities, funds, portfolios, and mispricing claims. NOT for macro commentary without a position, portfolio construction, or fund-manager due diligence.
---

# Investment Council

Five advisors calibrated for capital-at-risk decisions. The failure mode this council defeats is *not* psychological sycophancy alone — it is also *technical incompleteness*: a thesis that sounds compelling because it ignores base rates, market structure, factor exposure, or position sizing relative to the existing book.

This council is for institutional-grade thesis stress-testing — single positions and mispricing claims, not life/strategy/business decisions or fund-manager due diligence.

---

## CHANGELOG

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

4. **The Market Structure Analyst** — who's on the other side of this trade and why. Liquidity, positioning, flows, factor crowding, forced sellers/buyers, derivatives overhang. Asks whether the price is set by fundamentals or by structural pressure. Critical for any trade where the thesis is "cheap" — cheap relative to whom and why are they selling. *(Grounding/memory-flag discipline per the advisor prompt.)*

5. **The Risk Manager** — position sizing, correlation to existing book, drawdown scenarios, time-to-stop-out, max acceptable loss, regret minimization. Does not care if the thesis is right; cares whether you survive being wrong. For decision questions, this advisor's output is load-bearing. *Vary the opening line run-to-run — let the survivability stance come through in the substance, not a fixed signature sentence.*

These create the right tensions for a capital-at-risk context: Bull vs Bear (direction), Base Rate Skeptic vs Bull/Bear (qualitative narrative vs prior), Market Structure vs Fundamentals (price formation), Risk Manager vs everyone else (survivability).

---

## Session Protocol

### Step 1 — Frame the Question

Bounded context scan (≤3 reads): look for any portfolio context file, recent trade notes, position-sizing rules, the user's existing book composition, any uploaded research or screen output.

**Live-verify the thesis's load-bearing facts (mandatory).** Before framing, verify the facts the verdict will turn on against present-day data — current price and distance from the relevant high/low, the key balance-sheet items (net cash/debt, share count), short interest / borrow, and any specific catalyst the thesis cites. Where a tool is available, ground them; where not, flag the figure as memory-not-verified. Correct any stale or wrong premise explicitly in the framed question (e.g., "the thesis cites an entry near the prior high, but the current price is already through the stated stop; the headline cash balance is largely offset by convertible debt, so net cash is a fraction of the figure quoted"). This verification is the single place the council most often adds value — do not skip it.

**If retrieval is unavailable** (web search / tools off), the run is *degraded, not blocked*: do not silently verify from memory. Flag every load-bearing fact as memory-not-verified, refuse to assert precise figures you cannot source, and lower the conviction ceiling accordingly — a FAT PITCH or other high-conviction verdict requires verified facts.

Conventions: for the risk-free rate, use a short-tenor realized risk-free rate (e.g., a compounded 1-month T-bill) rather than a long-tenor yield. For high-beta or crypto-linked names, express **tail beta** = fund/asset loss ÷ benchmark loss in 5th-percentile stress periods, to the dominant risk factor.

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

Roughly 200–250 words. Direct.
```

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
  pass / wait for trigger / trim), a specific position size as % of NAV or in risk units
  (vol contribution, factor exposure), and a stop or invalidation level.
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

- **`council-investment-transcript-[YYYYMMDD-HHMMSS].md`** — full transcript: original question, framed question with classification and the verified facts, all five de-anonymized advisor responses, all five peer reviews with anonymization mapping revealed, chairman synthesis. **This is the primary artifact.**

- **`council-investment-report-[YYYYMMDD-HHMMSS].html`** — a single self-contained HTML file with inline CSS. Clean, scannable: question and classification at top, chairman verdict prominent (including the conviction tier), a small payoff-asymmetry visual at the top for DECISION or BOTH questions (a horizontal bar showing bear / base / bull outcomes), collapsible sections for each advisor's full response, collapsible peer-review section, footer with timestamp. System font stack, white background, subtle borders, no flash.

Open the HTML file after generating, where the runtime supports it, so the user sees it immediately.

---

## Hard Rules

- The thesis's load-bearing facts must be live-verified at framing and any correction stated explicitly. Skipping verification is forbidden. If retrieval is unavailable, do not verify from memory — flag all load-bearing facts as memory-not-verified and cap conviction below FAT PITCH.
- The classification (DECISION / ANALYSIS / BOTH) must be stated explicitly in the framed question. Skipping this is forbidden.
- All five advisors run independently; no advisor sees another's response. The Risk Manager is not optional even on ANALYSIS questions — capital lives on the other side of every analysis.
- Quantitative claims required from every advisor. An advisor that produces only qualitative prose has failed the spec; the chairman must flag it.
- Position size must be expressed as % of NAV or in risk units (vol contribution, factor exposure), never in dollar amounts unless the user provided a portfolio size.
- Always randomize the anonymization mapping for peer review.
- The chairman must surface the anti-sycophancy and anti-narrative checks in the output, is allowed and encouraged to disagree with the majority if the reasoning supports it, and may side with the user against the council if the anti-sycophancy check says so.
- FAT PITCH is gated: award it only when every condition in the verdict block holds. When they do, do not withhold it.
- Do not council questions that have one defensible quantitative answer (e.g., "what's the duration of a 10Y Treasury at 4%"). Just answer them.

---

## Notes for Claude

- This skill is **single-run by design** (architecture confirmed Jun 2026). Generate the full council — framing, five advisors, five peer reviews, synthesis, both output files — in one pass. Do not split it into staged turns: in a single chat context, staging would let earlier advisors anchor later ones and erode the independence that makes five distinct voices real.
- Independence is the load-bearing property of the advisor step. Protect it: do not let one advisor's text condition the next.
- The chairman synthesis is the highest-value adjudication step. Its reasoning *method* is already specified in the synthesis prompt (reason at length, resolve conflicts, do not average, run both checks) — follow it and give it real depth. Reasoning *depth* is governed by the run-time effort setting, not by any phrase here, so this skill is best run with high reasoning effort and web search enabled; neither can be set from inside the skill.
- Under length pressure, protect the load-bearing parts: the five advisor analyses and the chairman adjudication are non-negotiable. If a single run would otherwise truncate analysis, generate the HTML report as an immediate follow-up rather than thinning the council. (In practice, runs have not hit this ceiling — this is a safeguard, not a routine step.)
- Ground empirical claims (base rates, borrow, credit, options) in retrieved data where the tools exist; flag memory-vs-verified honestly rather than asserting false precision.
