---
name: business-decoder
description: Build a first-principles, plain-English understanding of any publicly listed company — the front-of-funnel lens applied BEFORE any valuation or thesis work. Covers what they do, how they make money, the need they serve, their customers, value-chain seat, ownership and control, the tailwind or headwind they ride, and whether it is a good, mediocre, or fragile business — translating ALL domain jargon (biology, physics, chemistry, engineering) into plain analogies a finance generalist gets. Trigger on "decode this business/company," "what does X actually do," "how does X make money," "understand this business," "is X a good business," "explain this company in plain English," "first-principles read on X," "I just heard about X." Industry-agnostic — biotech, photonics, industrials, software, anything. NOT for valuation, multiples, margins, or estimates (use a dedicated valuation skill); NOT for a buy/sell/short/mispricing view or sizing (use investment-council); NOT for fund or manager due diligence.
---

# Business Decoder

The first lens, applied before any numbers. Given any listed company — one you may be hearing about for the first time — this skill produces a plain-English memo that lets a finance-fluent generalist (think a CIO who is not a biologist, physicist, or engineer) *fundamentally understand the business from first principles*: what it does, how it makes money, the real need it serves, why it is or isn't a good business, and the structural force it rides.

It sits at the front of the research funnel — **upstream of `investment-council`** (which pressure-tests a thesis or trade) and **upstream of all valuation work** (a dedicated valuation skill). Its job is comprehension and a *worth-deeper-work?* steer, never a buy/sell call and never a valuation.

The defining quality bar is **de-jargoning**: every term from the underlying science or engineering is translated into a faithful plain-English analogy, with the precise term kept in a parenthetical so the reader can verify. Finance and strategy vocabulary is fair game; domain-technical vocabulary is not. This bar is hardest — and matters most — in deep-tech and biotech, so it is treated as a cross-cutting discipline, not a section. See `references/de-jargoning.md`.

---

## CHANGELOG

**v1.3** — Surgical patch from an independent adversarial QC audit (no spine, template, or Numbers-Line-core changes; description still frozen sub-1,024). Tightened the **mode/framing interaction**: the mode recommendation is a non-blocking statement the user may override, and the one permitted framing question is *identity only*. Fixed the **"dated-source flags" dangling reference** (dated per-fact sourcing lives in the dossier/working notes; the memo summarizes provenance in section 10). Added a **banner-from-section-10-first** ordering note. Added a **non-listed-entity branch** to Step 0. Disambiguated the **Phase-2 ingest phrase** ("ingest this dossier" / "here's the research", retiring the overloaded "go deep with this"). Extended the share rule with a **market-indeterminate carve-out** (revenue-bearing but undefined denominator → name the indeterminacy, don't manufacture a number). Clarified the honest-bear cap as **one *tight* paragraph (density may scale, length stays capped)** — a deliberate scope boundary, not expanded. Deferred by deliberate call: Standard-vs-Deep rationale wording (low-confidence, weak counter-evidence), §9-verdict-as-reasoning-point (verdict is downstream of the §8 bear, already a scoped point), and the "section 0" label (cosmetic).

**v1.2** — Maintenance review (single-run architecture reaffirmed; no redesign). Added a **silent terminal de-jargon verification pass** at the end of Step 3 (run the de-jargon test over every translated term before finalizing). Replaced the soft "reasoning effort" aside with **scoped extended-reasoning** bound to the two judgment points where value concentrates (faithful translation under uncertainty; the honest-bear adjudication). **Consolidated** the "character-not-magnitude" and "market-structure / estimated-share" rules to single canonical statements (in The Numbers Line) with cross-references — requirements unchanged; the v1.1 pre-revenue carve-out and the paste-ready Phase-1 brief left intact. **Reframed length** as complexity-scaled guidance (tight for a well-covered large-cap; longer expected for contested/obscure deep-tech) rather than a fixed page cap; "30-Second Version must stand alone" retained. **Trimmed** the in-body Industry-Lenses summary to a pointer + lens list (full detail stays in the reference file). Added **output-hygiene rules**: strip the HTML skeleton's authoring comment on inject (memo body appears once); no raw source labels in memo prose. Added an explicit **WARNING-banner threshold** (raise it whenever section 10 will flag any *load-bearing* fact as unverified). Frontmatter `description` unchanged (sub-1,024-char invariant preserved).

**v1.1** — Added competitive **market structure** (monopoly / duopoly / oligopoly / fragmented) and the company's **estimated market share** (rounded to ~10% increments) as an explicit, stressed element of section 6 and the Phase-1 brief. Classified both as allowed structural facts under the numbers line, with a pre-revenue carve-out (characterize the competitive *field* when no end-market exists yet). Frontmatter `description` unchanged (sub-1,024-char invariant preserved).

**v1.0** — Initial release. Single skill, two internal modes (Deep / Standard). Phase-1 Research Brief lives inside the skill. Twelve-section memo spine, "character not magnitude" numbers line, "Field Notes" light HTML theme on demand.

---

## Trigger Conditions

**Mandatory triggers:** `decode this business`, `decode this company`, `business decoder`, `what does X actually do`, `how does X make money`, `understand this business`, `explain this company in plain English`, `first-principles read on X`, `break down what X does`.

**Strong triggers** (when the intent is comprehension of a *named listed company*, with no thesis or valuation attached): `is X a good business`, `I just heard about X — what are they`, `is this a real business`, `what problem does X solve`, `who are X's customers`, `where does X sit in the value chain`, `what is X, in plain terms`.

**Do NOT trigger** — route elsewhere:
- A buy/sell/short call, a mispricing claim, position sizing, "is X a good *trade*," "should I buy X," "what's wrong with this thesis" → `investment-council`.
- Valuation, multiples, margins, growth rates, returns on capital, price targets, comps, discounted-cash-flow, tearsheets, consensus estimates, earnings models → a dedicated valuation skill.
- Fund or asset-manager due diligence, performance/risk metrics → a fund-evaluation skill.
- A Bloomberg beta/regression screenshot → a dedicated beta/regression reader.

The clean line: this skill answers **"is this a real and good *business*, and what is it?"** It hands off the moment the question becomes **"is this a good *trade*, and what's it worth?"**

---

## The Numbers Line (read before writing)

This is comprehension, not valuation — but "is it a good business?" is inseparable from the *character* of its economics. So the line is **character, not magnitude**. **This section is the canonical definition of what is allowed and what is banned; the Hard Rules and memo template reference it rather than restating it.**

- **Allowed** — qualitative economic character: recurring vs one-off vs project-based vs binary-milestone revenue; price-maker vs price-taker; mission-critical vs discretionary spend; razor/blade; toll-booth vs commodity; and **competitive market structure** (monopoly / duopoly / oligopoly / fragmented many-player). And **structural facts**: revenue mix %, customer/supplier/product/geographic concentration %, segment split, rough scale (e.g. "~$2bn revenue, ~70% from one product line"), and the company's **estimated market share of its relevant market**, rounded to ~10% increments (an estimate is fine — "roughly a third of the market," "~40% share" — precision is not the point).
- **Pre-revenue carve-out:** if the company has no end-market yet (e.g. a clinical-stage biotech with no product), end-market share is *not yet applicable* — say so, and instead characterize the competitive **field** of the technology/platform (how many serious players chase the same approach, and whether this name is a front-runner or one of a crowd).
- **Market-indeterminate carve-out:** if the company *has* revenue but its relevant market is itself undefined or contested — the denominator can't be cleanly drawn, common on exactly the obscure deep-tech names Deep mode exists for — do not manufacture a precise share. Give the most defensible rough bucket only if one genuinely exists, name *which* market you are measuring, and flag the indeterminacy as an open question in section 10 (and the banner). The mandate is to *address* the market, never to invent a number to satisfy it.
- **Banned** — any number that feeds a valuation: margins, multiples, growth rates, returns on capital, free-cash-flow figures, price targets, "cheap/expensive." If you catch yourself reaching for one, stop — that belongs to a downstream skill.

If the analysis genuinely cannot proceed without a magnitude, name the gap in the memo's open-questions section rather than supplying the number.

---

## Mode Selection

One skill, two modes. Decide at framing; state which mode you're in.

- **Deep mode** — the default for a *fresh, obscure, or technically hard* name (any deep-tech, biotech, photonics, or specialist industrial; any company you'd otherwise only be able to describe from its own deck). The skill emits a **Phase-1 Research Brief** — a paste-ready directive — then stops. The user runs it with the **Research** feature on (which a skill cannot toggle itself — say so plainly), and re-invokes business-decoder with the returned dossier for Phase 2.
- **Standard mode** — the fast path for a *well-known, well-covered* name, or whenever the user says "quick," "fast," or "just do a pass." One invocation: do intensive inline retrieval (searches scaled to complexity) and write the memo directly.
- **Phase-2 ingest** — if the user has *already* supplied a research dossier (or pastes Research output, or says "ingest this dossier" / "here's the research"), skip straight to writing the memo from it.

Default lean: for non-trivial or hard names, recommend Deep mode (emit the Phase-1 brief) and name Standard as the quick alternative the user can choose instead — offered as a statement, not a question that blocks. Never force two turns on an easy large-cap.

---

## Session Protocol

### Step 0 — Frame

Identify the company and **resolve its identity**: legal name, ticker(s), the specific listing (an American Depositary Receipt versus the local line, the parent versus a listed subsidiary). If identity is genuinely ambiguous — dual listings, a name collision, a very recent merger/spin, or the user gave only a vague descriptor — ask **one** clarifying question, then stop. Otherwise proceed. If X turns out **not to be a publicly listed entity** (a private company, a division, a product, or a protocol — phrasings like "what does X do" and "I just heard about X" will sometimes land on these), say so plainly and either proceed on the available non-filing sources while flagging the absent annual-filing tier in the WARNING banner, or, if it falls outside the skill's listed-company scope, recommend the more appropriate route. Never invent a listing.

Classify the **industry lens** and read the matching section of `references/industry-lenses.md` for what to emphasize and what jargon will need translating. If no lens fits cleanly, apply the universal spine with first-principles judgment — the spine always holds; the lens only shifts emphasis.

Select the **mode** per the Mode Selection logic above. State the chosen mode as a recommendation, not a blocking question — on a hard name that means emitting the Phase-1 brief and stopping; the user overrides simply by asking for the other mode. The only question that may halt framing is the identity question above.

### Step 1 — Deep mode only: emit the Phase-1 Research Brief

Produce the brief below, paste-ready, then stop and hand control back. Do not start analyzing.

```
PHASE-1 RESEARCH BRIEF — business-decoder
Company: [name / ticker / listing]
Run this with the Research feature ON, then paste the dossier back into a new
business-decoder turn (say "ingest this dossier").

Gather, with a dated source for each fact, enough to answer:
- In one plain sentence a non-expert grasps: what does this company sell, and to whom?
- How does revenue actually arrive (subscription / one-off / project / aftermarket /
  regulatory milestone)? Who writes the cheque?
- What need or problem does the customer hire them for? Is it mission-critical or
  discretionary? What would the customer do if this company vanished?
- Where do they sit in the value chain, and who holds the power in that chain?
- What is the competitive market structure (monopoly / duopoly / oligopoly / fragmented),
  who are the main rivals, and roughly what share of its relevant market does the company
  hold (an estimate rounded to ~10% is fine)? If pre-revenue, characterize the competitive
  field of the technology instead.
- Who owns and controls the company (founder/key person, family, state/SWF, PE,
  diffuse ETF/index)? Control mechanisms (dual-class, golden share)?
- What structural tailwind or headwind are they tied to — and does it actually flow
  through to THEIR revenue, or are they merely adjacent to the story?
- Where is the business concentrated or fragile (one customer, one product, one
  geography, one regulator, key-person dependence)?

Source priority (capture provenance for each):
1. Latest annual filing — Business section (e.g. Form 10-K Item 1) AND Risk Factors
   (Item 1A), or the local-regulator equivalent. Risk Factors especially: the one
   place a company is legally forced to say what could go wrong.
2. Latest investor presentation; homepage; product and pricing pages.
3. Latest earnings or investor-day TRANSCRIPT (management under analyst questioning
   is often clearer than the deck).
4. At least one INDEPENDENT / ADVERSARIAL read — competitor framing, sell-side
   initiation, trade press, a short report if one exists. Flag it as such.
5. For deep-tech/biotech: a layperson explainer of the underlying science, used to
   UNDERSTAND only — never reproduced.
6. Catch-all, situation-specific: clinical-trial registries, regulator actions,
   patents, ownership/13F holdings, litigation, named suppliers/customers, job
   postings (an underrated tell for what's actually being built and sold).

Return a sourced dossier. Note anything you could NOT find.
```

### Step 2 — Ingest sources and live-verify

Whether from a Phase-1 dossier (Deep) or inline retrieval (Standard): assemble the sources in priority order, and **live-verify the load-bearing facts** — what they sell, how revenue arrives, ownership/control, the concentration figures, the tailwind linkage. Treat company sources as the company's *claims*, not facts; weight the Risk Factors section and any independent source against the self-narrative.

This skill is **retrieval-dependent**. If retrieval is unavailable or sources can't be obtained in full, the run is *degraded, not blocked*: proceed, but flag every unverified load-bearing fact as memory-not-verified and raise the WARNING banner (Step 4). The adversarial read is **best-effort** — if none could be found, say so in the banner rather than inventing one.

### Step 3 — Write the memo

Use the exact template in **The Memo** below. Apply the de-jargoning discipline throughout (`references/de-jargoning.md`).

**Length scales with the name.** A well-covered large-cap should land tight (≈1.5 pages); a contested, obscure, or technically hard deep-tech name will — and should — run longer (2–3+ pages). Write to the complexity of the company, not to a fixed page count: never pad, never truncate. The 30-Second Version is mandatory and must be genuinely sufficient on its own.

**Spend reasoning where the value concentrates.** Engage real reasoning effort narrowly, at exactly two points: (a) the faithful translation of any term whose plain-English analogy involves genuine uncertainty, and (b) the honest-bear adjudication in section 8. Do not spread effort evenly across the memo — these two are where the craft and the judgment live.

**Before finalizing, run a silent verification pass over every translated term.** Apply the de-jargon test from `references/de-jargoning.md`: could a smart generalist who read *only* that sentence (a) state what the company sells and (b) not be embarrassed saying it in front of a domain expert? Rewrite any term that fails. This is a check you perform internally, not visible chain-of-thought in the memo — and it must preserve "faithful first, vivid second" rather than flattening vividness into caution.

### Step 4 — Outputs

- **Primary:** a plain-Markdown memo, fileable directly into any Markdown vault. This is always produced.
- **On demand only:** a self-contained HTML report in the **"Field Notes"** theme — generate it when the user asks for HTML / a report / something shareable. Read `references/field-notes-theme.md` and use the bundled `assets/field-notes.html` skeleton; inject the memo content, change no CSS. **After injecting, strip the skeleton's authoring/usage comment so it does not ship in the delivered file, and confirm the memo body appears exactly once** (the skeleton's instructions and any example content must not survive into the delivered file). Save both files to the working directory and present them.

If sources were incomplete, the memo (and HTML) open with a one-line **WARNING** banner naming what's unverified or missing. **Threshold:** raise the banner whenever section 10 will flag any *load-bearing* fact — what they sell, how revenue arrives, ownership/control, a concentration figure, the tailwind linkage, or a claim the verdict rests on — as unverified. This is not only for a wholesale retrieval failure: a well-sourced run with one unverified load-bearing fact still gets a banner. Tie it strictly to load-bearing facts; do not banner cosmetic gaps. Because the banner sits at the top but summarizes section 10, settle section 10's verification list first (draft it silently), then write the banner from it — never guess the banner mid-memo.

---

## The Memo

ALWAYS use this structure. Headers are fixed; prose underneath is plain English.

```
# Business Decoder — [Company] ([Ticker], [Listing])
*Decoded [date] · Mode: [Deep / Standard] · Lens: [industry]*

> ⚠️ WARNING — [only if sources incomplete: name what could not be verified or sourced]

## The 30-Second Version
[3–5 plain sentences (a few more only if the story genuinely needs it): what they sell
and to whom; how they make money; who pays and why; and a one-line "this looks like a
[good / mediocre / fragile / too-hard-to-judge] business because …". Must stand alone.
End with the anchor: "In one line, this is the [X] of [Y]."]

## 1. What they actually do
[Plain English, fully de-jargoned. Every domain term translated faithfully, vivid
second, with the precise term in parentheses. A generalist who reads only this section
should be able to say what the company sells without embarrassment in front of an expert.]

## 2. How they make money
[The SHAPE of revenue, qualitatively: subscription / one-off / project / aftermarket
razor-blade / binary milestone. Who writes the cheque, for what, how often.
(Character, not magnitude — structural facts allowed, no valuation numbers; see The Numbers Line.)]

## 3. The need they serve & the problem they solve
[What job the customer hires them for. Need or want? Mission-critical or discretionary?
The vanish test: what happens to the customer if this company disappears on Friday.]

## 4. Customers & where they sit in the value chain
[Customer type. Then the power question: upstream/downstream is necessary but the real
issue is who captures the value — toll booth or toll payer, bottleneck or commodity.]

## 5. Whose company is this? — ownership & control
[Owner TYPE: founder/key-person, family, state/SWF, PE roll-up, or diffuse ETF/index.
Control mechanism (dual-class, super-voting, golden share). One line on the
opportunity/risk that type brings — patient capital, key-person risk, related-party
risk, state-agenda risk, takeover-ability, or nobody-minding-the-store. Light governance
only; percentages and control mechanisms are structural facts, no valuation.]

## 6. Market structure, share & why they win (or don't)
[FIRST, set the competitive arena: is this a monopoly, a duopoly, a tight oligopoly of a
few names, or a fragmented field of many? Name the main rivals. Then give the company's
ESTIMATED share of its relevant market, rounded to ~10% (an estimate is expected — "roughly
a third," "~40%," "low-single-digit share of a fragmented market"); say which market you're
measuring. If pre-revenue, apply the carve-out (see The Numbers Line): end-market share is
not yet applicable — characterize the competitive FIELD of the technology/platform instead
(how many serious players chase the same approach, and whether this name is a front-runner
or one of a crowd). If the relevant market is itself indeterminate, name the indeterminacy
(per The Numbers Line's market-indeterminate carve-out) rather than forcing a number.
THEN why they win: against named rivals AND against the do-nothing / status-quo alternative
— often the real competitor. Switching costs. Leader / fast-follower / me-too. And why this
is even a standalone company rather than something done in-house or absorbed up/down the chain.]

## 7. The tailwind (or headwind) they ride
[The structural force. Then the exposure-quality test: does it actually flow through to
THEIR revenue, or are they merely adjacent to the story (anti-"AI-washing")? Durable
secular trend or a cycle? And the demand character: cyclical / secular / counter-cyclical.]

## 8. What must stay true — and where it's fragile
[The load-bearing assumptions of the BUSINESS (not a trade thesis): concentration of
customer / supplier / product / geography / single regulator; single points of failure;
key-person dependence. Then the honest bear in ONE tight paragraph (it may run dense for a
contested name, but it stays a single concentrated paragraph — the full bear belongs to
investment-council): where is the company's own
framing doing work the facts don't support? This is the structural defence against
laundering the investor deck.]

## 9. Verdict — is this a genuinely interesting business?
[A qualitative characterization with the WHY: good / mediocre / fragile / too-hard-to-
judge. Then the handoff steer, explicitly NOT a buy/sell call:
→ worth valuation work / → worth running through investment-council /
→ watch, with a named trigger to revisit / → pass for now.]

## 10. Open questions & what I couldn't verify
[Honest gaps a domain-expert call or a further filing would resolve. Provenance flags:
which statements are sourced, which are the translator's interpretation, and which
analogies involved a judgment call that an expert should sanity-check.]
```

(Sections 0–10 above; section 0 is the header/WARNING block.)

---

## Sourcing Priority (the six tiers)

Used in both modes — exhaustively in Deep (via Research), proportionately in Standard (inline search).

1. **Latest annual filing — Business + Risk Factors** (Form 10-K Item 1 and Item 1A, or local equivalent: German *Geschäftsbericht*, UK Annual Report, Japanese securities report). Item 1 is the company's own plainish description; Item 1A is the legally forced confession of what could go wrong — the most valuable anti-brochure source.
2. **Investor presentation, homepage, product & pricing pages** — the self-narrative and the raw material for de-jargoning.
3. **Latest earnings / investor-day transcript** — management in their own words, pushed by analysts.
4. **One independent / adversarial source** — sell-side initiation, trade press, competitor framing, short report. Best-effort; flag as such.
5. **Layperson science/tech explainer** — deep-tech/biotech only, to *understand* the mechanism for faithful translation. Never reproduced.
6. **Catch-all, situation-specific** — clinical-trial registries, regulator actions, patents, ownership/13F data, litigation, named suppliers/customers, job postings.

---

## Industry Lenses

The spine is universal; the *emphasis* shifts by industry. At framing, read the matching section of `references/industry-lenses.md`: **Biotech / pharma · Hardware / deep-tech · Software / platforms · Financials / cyclicals · Consumer / services.** If none fits cleanly, apply the spine directly with first-principles judgment.

---

## Hard Rules

- **De-jargon everything technical.** No sentence may leave a biology/physics/chemistry/engineering term untranslated. Faithful first, vivid second; precise term in parentheses immediately after (this also satisfies the house rule to define every acronym at first use). A vivid-but-wrong analogy is worse than honest jargon — preserve the mechanism that matters for the *business*.
- **Always place the company in its market.** State the competitive structure (monopoly / duopoly / oligopoly / fragmented) and an estimated market share (~10% buckets), applying the carve-outs where they fit. These are structural facts, not banned magnitudes — see **The Numbers Line** for the full definition (including the pre-revenue and market-indeterminate carve-outs that cover cases where a clean share figure can't be drawn). Never skip *addressing* the market; "is it a good business?" is unanswerable without knowing whether they own the market or fight forty rivals for scraps.
- **Character, not magnitude.** Never quote or estimate a valuation number (margins, multiples, growth, returns on capital, price targets). Structural facts are allowed — see **The Numbers Line**.
- **No buy/sell/mispricing view, no position size, no valuation.** The verdict is a *business* characterization plus a handoff steer. The moment it becomes a trade or a price, it belongs to `investment-council` or a valuation skill.
- **Treat company sources as claims, not facts.** Weight Risk Factors and any independent source against the self-narrative. The honest-bear paragraph is mandatory; the adversarial *source* is best-effort.
- **Keep source attribution out of the prose.** Dated, per-fact sourcing lives in the Phase-1 dossier (Deep) or your working notes (Standard); the memo summarizes provenance in section 10 (what's sourced vs the translator's interpretation vs a judgment call) and never drops a raw source label — a bare site or report name — into a memo sentence.
- **Retrieval-dependent, degrade honestly.** If sources can't be fully obtained, raise the WARNING banner (per the Step 4 threshold) and flag unverified facts as memory-not-verified rather than asserting false confidence.
- **Length scales with the name.** Tight for a well-covered large-cap; longer for a contested or obscure deep-tech name. Write to the complexity; never pad, never truncate. The 30-Second Version must stand alone.
- **One question maximum at framing** — the identity question — and only when company identity is genuinely ambiguous. Otherwise proceed. The mode recommendation is a statement the user may override, not a question that counts against this budget.

---

## Notes for Claude

- The whole point is *microscope-level understanding in accessible language* — depth and plain-spokenness at once. Do not trade one for the other. The reader is sophisticated about markets and strategy and naïve only about the domain science; pitch every translation to exactly that person.
- The largest failure mode is **laundering the investor deck** — producing a fluent paraphrase of the company's own marketing. The Risk Factors read, the independent source, and the honest-bear paragraph exist specifically to defeat it. If the memo reads like something the company's investor-relations team would happily publish, it has failed.
- The second failure mode is a **vivid analogy that's subtly wrong**. The parenthetical precise term and the provenance flags in section 10 are the safety net — they let the reader (or a later expert call) catch a translation that drifted. When a translation required a real judgment call, flag it; don't hide the seam. (The silent de-jargon verification pass at Step 3 is the active check against this.)
- Deep mode's split exists because breadth wins on the hard names and inline retrieval tends to stop early once there's "enough to write something." Standard mode exists so an easy large-cap doesn't cost two turns. Route on the *name*, not reflexively.
- Best run with web search enabled (or the **Research** feature, for Deep mode); neither setting can be toggled from inside the skill, so recommend them when relevant. Where to spend reasoning effort is specified at Step 3 — narrowly, at the translation and the honest-bear judgment.
- This skill ends where conviction begins. Its last act is a clean baton-pass, never a verdict on the trade.
