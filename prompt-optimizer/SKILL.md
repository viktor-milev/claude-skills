---
name: prompt-optimizer
description: Rigorous mode prompt optimizer — audits a polished prompt, decides output architecture (single-turn vs. chained multi-stage), and rewrites it to elicit state-of-the-art output from Claude. Triages every input into OUT OF PURVIEW / BORDERLINE / IN PURVIEW; rigorous optimization (architecture decision, 9-dim diagnostic, archetype detection, capability activation) applies ONLY to IN PURVIEW. When chain signals fire, produces a chain deliverable (README + Project orchestrator + per-stage prompts) instead of a single-turn prompt. Simple prompts return substantially unchanged with a triage note. Use when the user asks to optimize, audit, score, diagnose, sharpen, or level up a prompt; trigger phrases include "optimize this prompt", "PO this", "run this through the optimizer", "sharpen this prompt". For messy dictated or flow-state input, a separate flow-mode counterpart exists. Do NOT trigger when the user wants the prompt EXECUTED rather than optimized.
---

# Prompt Optimizer (State-of-the-Art Edition)

## CHANGELOG

**v4.1 — May 2026.** Three surgical calibration additions grounded in the Anthropic official prompting guide (and, where convergent, Ruben Hassid's chat-app prompt optimizer). No structural changes; no architecture decision changes; no lockstep coordination with prompt-architect required.

- **`<examples>` elevated to first-class structural tool (Step 6 Branch A).** v4's structural tools list included `<role>`, context-first ordering, `<constraints>`, motivated constraints, `<output_format>`, `<failure_modes>`, `<evaluation_criteria>`, domain-specific tags, and `[bracketed placeholders]` — but omitted `<examples>` as a first-class tool, despite Anthropic identifying examples as "one of the most reliable ways to steer Claude's output format, tone, and structure." Calibration guidance specifies when to add (Creative archetype voice anchoring, Iterative refinement consistency, format-preference cases) and when to skip (generic tasks, no signaled format preference). Addresses an underplayed lever surfaced in the v3 A/B test complex_creative loss (Howard Marks voice diluted by structural scaffolding).
- **Quote-grounding pattern for long-document tasks (Step 6 Branch A).** Added an explicit structural tool: for prompts loading ≥3K tokens of source material, instruct Claude to extract relevant passages into `<quotes>` tags before reasoning. Per Anthropic's long-context guidance, materially reduces drift and hallucination on document-grounded analytical tasks. Directly relevant to long-document analytical workflows (multi-document research synthesis, annual reports, long transcripts, dense source material that must be analyzed without drift).
- **Scope literalism added to 4.7 calibration note (Step 5).** v4's calibration note covered reasoning, anti-sycophancy, and research activation but omitted 4.7's literal-reading shift. Step 5.5 signal preservation handles most cases operationally; the calibration note now surfaces the principle during capability selection — particularly for keystone prompts where session-wide norms must apply consistently.

Item considered and rejected for v4.1: tag-framing preference rule (prefer `<evaluation_criteria>` over `<failure_modes>` when equally precise). Scored 40% against a ~60% threshold — operates at wrong altitude (tag name vs. content framing), already covered by the "tags earn their place" principle, no evidence basis. Logged for revisit only if a future A/B test surfaces tag-framing as a failure mode.

**v4 — May 2026.** Introduces architecture decision as a first-class step.

The v3 skill had a structural alignment problem: it optimized a proxy (the quality of a single-turn prompt) rather than the actual target (state-of-the-art Claude output on the user's task). For complex work, those objectives diverge — forcing a single-turn structure compresses output that would have been materially better delivered as a chain.

v4 fixes this by promoting the architecture decision from an implicit downstream constraint (the v3 feasibility pass could only flag overflow) to an explicit upstream decision (new Step 3, between Archetype Detection and Diagnostic). The decision drives branched behavior in Steps 4–7 and produces one of two distinct output formats:

- **Single-turn template** — the v3 output, refined. Includes a new SINGLE-TURN (CEILING) sub-band that makes the "fits but at the edge" cases visible to the user (previously silent).
- **Chain template** (new) — produces a README/setup guide, a Project orchestrator (custom instructions), and per-stage prompts as separate deliverables, mirroring the validated Project-orchestrated workflow pattern.

The architecture decision is governed by seven named signals (four primary, three amplifying) with explicit guards against over-recommending chain. The verdict, signals fired, and Project-vs-flat sub-decision are always named — the recommendation is falsifiable, not aesthetic.

**v3 — Apr 2026.** Fixes three real bugs surfaced by a 20-prompt A/B test (Apr 2026):

- **Signal preservation** (new Step 5.5, formerly 4.5). The single largest skill-level failure mode in v2 was *structural overwrite of raw-prompt signals*. The optimizer wrapped unfilled placeholders (`[CV]`, `[JD]`) as if material were present; converted ambiguous hints ("I have a draft to share") into locked workflows; and dropped explicit format directives ("both analyses side by side") during restructuring. Step 5.5 enumerates every signal in the raw prompt before any restructuring and requires each to be preserved or explicitly overridden with rationale.
- **Feasibility pass** (revised Step 7, formerly 6). The old step asked only "what could we add?" It never asked "what would crash the turn if we added it?" The revised step runs explicit checks — output budget, context-dependency honesty, user-format preservation, input-presence — and is allowed to *cut* the optimization rather than only extend it.
- **Calibrated capability activation** (Section 5, formerly 4). `[Confirmed]/[Inferred]` confidence tags now require explicit source material in the prompt. `<research_activation>` now explicitly degrades to "state what you don't know" when no search tool is present.

---

Optimize prompts to reach the ceiling of what Claude can produce on a given task — but ONLY when the task warrants it. Rigorous optimization on a prompt that doesn't need it produces worse output, not better. The skill's first job is to decide whether to optimize at all.

## Core philosophy

**Stop optimizing prompts. Start optimizing the Claude session that the prompt initiates.**

A prompt is not a request. It is the keystone of an interaction. The job of optimization is not to make the prompt well-formed — it is to make Claude perform at the top of its distribution on the task the prompt describes.

But this logic only applies when the task has a ceiling worth reaching. A PTO email does not. Forcing XML scaffolding, capability activation, and 6-section diagnostic outputs onto casual communication tasks makes the downstream output *worse*, not better — it bloats the prompt, buries the actual ask, and ships Claude a bureaucratic commission when the user wanted a 3-sentence note. This is a SEVERE failure mode (see Failure Modes section).

The skill therefore operates in three triage bands, classified before any optimization work begins.

A second equally important philosophy (v3): **preserve the user's signals; restructure only what the user didn't specify.** The raw prompt is the user's compressed intent. Every format directive, placeholder, ambiguity marker, and workflow hint is a signal. Optimization that overwrites those signals produces a prompt Claude follows perfectly — to the wrong target.

A third equally important philosophy (v4): **choose the architecture before choosing the structure.** Single-turn is the default delivery vehicle, but it is not the only one. When the work is genuinely complex — multi-deliverable, sequentially dependent, checkpoint-bearing, or compression-costly — chain architecture produces materially better output than any single-turn prompt could. The skill's job is to make that architecture call explicitly, name the signals that justified it, and produce a chain deliverable when chain is the right answer. Defaulting silently to single-turn on prompts that deserved a chain is an alignment failure between the skill's stated mission (state-of-the-art output) and its mechanical behavior (single-turn polish).

---

## Step 0 — TRIAGE (mandatory; runs before every other step)

Classify the input into one of three bands using the four tests below. State the verdict explicitly in one line before proceeding. The user may override (e.g., "treat as IN PURVIEW") if they disagree.

### The four triage tests

Run all four. Score each with OUT / BORDERLINE / IN leaning.

**Test 1 — Artifact type.** What is the downstream output?
- OUT leaning: email, text message, Slack message, tweet/post, caption, subject line, short note, simple list, simple lookup, simple formatting task, casual personal communication
- BORDERLINE leaning: article, blog post, cold outreach, summary of provided material, standard business document (meeting agenda, status update), recipe / single-workout plan, single-topic explainer
- IN leaning: report, memo, analysis, evaluation, research synthesis, strategic document, session keystone, multi-constraint creative work, agentic task spec, decision framework, published artifact intended for external stakeholders

**Test 2 — Raw prompt word count.**
- <30 words: OUT leaning
- 30–100 words: BORDERLINE leaning
- >100 words: IN leaning (content usually dominates, but size correlates)

**Test 3 — Analytical load.** Does the task require reasoning, decomposition, research, evaluation, or synthesis?
- None (pure generation, formatting, lookup): OUT leaning
- Light (single narrow judgment, e.g., "make this tone warmer"): BORDERLINE leaning
- Present (multi-step reasoning, tradeoff analysis, research integration, evaluation against criteria): IN leaning

**Test 4 — Consequentiality.** What is the downstream cost of a mediocre output?
- OUT leaning: Low-consequence communication — casual content, familiar or non-specific audience, and a mediocre version still accomplishes the task (PTO email to known boss, tweet about your day, text to a friend, routine Slack update)
- BORDERLINE leaning: Externally-facing or reputationally-significant artifact — first-impression content, cold outreach, professional networking, short business documents where tone and specificity materially affect whether the artifact achieves its purpose. The user sends it once, but getting it wrong has real professional cost.
- IN leaning: Session keystone, published document, work product shared with stakeholders, or anything that will be iterated on

### Verdict rules

- **3 or 4 OUT leanings → OUT OF PURVIEW.** Short-circuit. Do not run the full workflow.
- **3 or 4 IN leanings → IN PURVIEW.** Run the full workflow below.
- **Mixed or 3+ BORDERLINE leanings → BORDERLINE.** Run abbreviated workflow.
- **Override rule:** if analytical load is PRESENT (a real reasoning task), promote at least one band up regardless of other scores. "Should I take this job?" at 8 words is IN PURVIEW, not OUT.
- **Override rule:** if the user explicitly flags the prompt as a keystone or high-stakes ("this kicks off my session", "this goes to my CIO", "this is the prompt for my automated pipeline"), promote to IN PURVIEW.

### Output the verdict in one line

Before any further work, state:

> **TRIAGE: [OUT OF PURVIEW | BORDERLINE | IN PURVIEW]** — [one-sentence rationale citing the two or three tests that drove the call]

If the user disagrees, they can override in their next message. Proceed to the band-appropriate output path below.

---

## Band-specific output paths

### Path A — OUT OF PURVIEW output format

Return exactly this, and nothing else:

> **TRIAGE: OUT OF PURVIEW** — [rationale]
>
> **Verdict:** this prompt is already well-calibrated for its task. Rigorous optimization would over-engineer it and degrade the downstream output.
>
> **Surgical note (optional, only if genuinely missing):** [one sentence naming the single missing element, if any — typically a date, audience, or length spec. If nothing is missing, write "None — send as is."]
>
> That is the entire response. Do NOT add a diagnostic table, archetype detection, architecture decision, XML scaffolding, change log, feasibility check, or use case guidance. Doing so defeats the purpose of the triage.

**Self-check before finalizing Path A.** If your surgical note names 2 or more distinct gaps (e.g., "you need to specify X, Y, and Z"), the triage miscalled. A prompt with 2+ real gaps is BORDERLINE by the skill's own definition. Re-classify and route to Path B. Path A is only valid when the prompt is genuinely one-gap-or-less from ready.

### Path B — BORDERLINE output format

Return exactly three sections:

**SECTION 1 — TRIAGE & DIAGNOSIS**
One line of triage verdict. Then 2–4 bullets naming the specific gaps worth closing (typically: audience, output format, length, tone, one missing constraint). Do NOT run the full 9-dimension scoring or the architecture decision — they are theater at this band.

**SECTION 2 — OPTIMIZED PROMPT**
The original prompt with surgical additions — typically 1 to 3 added sentences or constraints, inline. NO XML scaffolding. NO capability activation layer. If the original works as a paragraph, it stays a paragraph. Copy-paste ready.

**SECTION 3 — CHANGE LOG**
Two to four bullets. Each names what changed and why, in plain language. No diagnostic dimension tags — they don't earn their place here.

No feasibility check, no use case guidance, no architecture decision. BORDERLINE outputs aren't the kind of thing that has either a ceiling or an architecture question.

### Path C — IN PURVIEW output format (the full rigorous treatment)

Proceed through Steps 1–9 below. The output format branches further based on Step 3's architecture decision: SINGLE-TURN template (Path C1) or CHAIN template (Path C2). See "IN PURVIEW output formats" at the end of this document.

---

## IN PURVIEW workflow (Steps 1–9)

This is the full rigorous treatment. Apply ONLY when Step 0 returned IN PURVIEW.

### 1. Intent extraction

Before changing anything, state in 2–3 sentences: what this prompt is trying to accomplish, who would use it, and what a successful output looks like. This is the north star — every optimization must serve this intent.

### 2. Archetype detection

Identify which archetype the prompt belongs to. Different archetypes require different optimization patterns:

- **Keystone prompt** — the first prompt in a new chat or project. Sets persistent context, working norms, and defines the relationship for the entire session. Needs the heaviest optimization treatment: role depth, working principles, output norms, escalation paths.
- **One-shot task** — a single self-contained request. Needs tight scope, clear output format, and constraints. Lighter optimization. (Note: most one-shot tasks are triaged out by Step 0. If you're here, the one-shot is genuinely complex.)
- **Iterative refinement** — a prompt designed to be run repeatedly with variable input (templates, generators, evaluators). Needs strong input placeholders, reusability, and consistency mechanisms.
- **Agentic task** — a prompt that initiates multi-step work involving tools, research, or extended reasoning. Needs explicit reasoning scaffolds, tool permission, failure recovery, and progress checkpoints.
- **Creative generation** — prompts for writing, ideation, or aesthetic output. Needs voice calibration, anti-generic constraints, and freedom-of-form preservation.

State the archetype explicitly. If the prompt straddles multiple, name the primary one and note the secondary.

Archetype is orthogonal to architecture (Step 3): a one-shot task can be single-turn or chain; an iterative refinement task can be single-turn or chain. Archetype is the cognitive nature of the work; architecture is the delivery vehicle.

### 3. Architecture decision (NEW in v4)

The skill's job is to produce state-of-the-art output from Claude — not to produce well-formed single-turn prompts. Those objectives diverge on complex work, where forcing a single-turn structure compresses output the user could otherwise have had.

This step decides the *output architecture* for the optimization: single-turn, single-turn (ceiling) (strained but fits), or chain (sequential stage prompts with handoffs and optional human-in-loop checkpoints). The decision drives downstream branching of Steps 4–7.

**Default:** single-turn. Chain has to earn it through named signals; ambiguity resolves toward single-turn.

#### Trigger signals

Seven signals. Primary signals (1–4) drive the verdict. Amplifiers (5–7) modulate within bands.

**Primary signals:**

1. **Multi-deliverable** — the prompt requests two or more distinct artifacts with materially different shapes (e.g., data sheet + analytical memo + scenario matrix). Multiple sections of one memo do not count; the deliverables must be genuinely different in form.

2. **Sequential dependency** — Phase B's quality materially depends on Phase A's substantive output, and Phase A is non-trivial (research, gathering, calculation). Pure structural sequencing ("intro then body then conclusion") does not count.

3. **Checkpoint value** — there exists at least one point between phases where human judgment materially improves the final output (methodology selection, scope decisions, data validation, comparable selection). If a "continue" between phases would only be ceremonial, this signal does not fire.

4. **Compression cost** — fitting the full work into one turn would force compression of sections that lose fidelity. A 3×2 scenario matrix compressed into six bullets is the canonical example.

**Amplifying signals:**

5. **Reusability premium** — the prompt will be re-run many times across different inputs (>5 expected runs). Chain infrastructure setup cost amortizes; single-use single-turn does not benefit from chain structure.

6. **Cognitive mode switching** — the work requires distinct cognitive modes that benefit from clean separation (research/gathering vs. synthesis vs. creative output vs. critique). Mixing modes in one turn produces worse output than running them sequentially.

7. **Volume overflow** — output genuinely exceeds single-turn budget (~3000–5000 words of dense work) even with disciplined compression. Honest assessment, not "we could try to fit it."

#### Verdict logic

Count the primary signals (1–4) that fire:

- **0 primary signals → SINGLE-TURN.** Default. Whether amplifiers fire is irrelevant; no structural reason to chain.
- **1 primary signal → SINGLE-TURN (CEILING).** The strain is acknowledged in the output. Proceed with single-turn but flag the specific strain visibly. The user can request chain in follow-up if they prefer.
- **2+ primary signals → CHAIN.** Name the signals fired. Proceed to chain architecture.

**Amplifier escalation rule:** if exactly 1 primary fires AND 2+ amplifiers fire, the verdict may escalate to CHAIN. The escalation must be explicitly noted ("escalated from SINGLE-TURN (CEILING) on amplifier weight").

**Reusability-only exception:** if only signal 5 (reusability premium) fires with no primaries, the verdict stays SINGLE-TURN. Recommend saving the prompt in the user's library — do not build chain infrastructure around a prompt that doesn't structurally need it.

#### Sub-decision when CHAIN: Project-orchestrated vs. flat-sequential

Two chain delivery vehicles:

- **Project-orchestrated** — one-time setup of Project custom instructions + knowledge-base stage files; ongoing runs trigger stages with short commands. Best for high-reuse workflows. Higher setup cost; near-zero per-run friction.
- **Flat-sequential** — standalone stage prompts the user pastes sequentially in the same chat. Best for one-shot complex tasks. Low setup cost; some per-run friction.

**Heuristic:** if signal 5 (reusability premium) fires, recommend Project-orchestrated. If not, recommend flat-sequential.

**Important:** the optimizer presents its heuristic recommendation but asks the user to confirm or switch. The chain deliverables are produced in the next turn after the user's choice. This is a deliberate two-turn split that mirrors the methodology checkpoint pattern in well-designed chains themselves.

#### Guards

- **Single-turn remains the default.** Chain must earn it through named signals. Ambiguity resolves to single-turn.
- **Smallest chain that does the job.** Three stages when three suffice; never five when three work. Treat extra stages as friction cost and require each one to justify its existence.
- **Name the signals.** The verdict is not "chain" — it is "chain because signals 1, 2, 3, and 4 fired." Without naming, the recommendation is unfalsifiable.
- **Respect friction costs.** Project-orchestrated has setup overhead; chain in general has cognitive overhead. Recommend the lightest architecture that fits the work.
- **Reusability alone does not chain.** If only signal 5 fires, recommend saving the prompt rather than building chain infrastructure around it.

#### Output of this step

State the verdict in this format:

> **ARCHITECTURE: [SINGLE-TURN | SINGLE-TURN (CEILING) | CHAIN]**
>
> Primary signals fired: [list, e.g., "1 (multi-deliverable), 4 (compression cost)"]. Amplifiers: [list, or "none"].
>
> [For SINGLE-TURN: brief one-line confirmation.]
>
> [For SINGLE-TURN (CEILING): brief note on the specific strain to be acknowledged in the output and what the user gives up by staying single-turn.]
>
> [For CHAIN: brief skeleton — number of stages, what each does, where checkpoints sit, Project-vs-flat heuristic recommendation phrased as an explicit question to the user. Then STOP and await user confirmation before proceeding to Steps 4–9. The chain deliverables are produced in the next turn.]

Carry the verdict into Steps 4–7. Step 4 (diagnostic) branches on architecture; Step 6 (optimization/rewrite) branches the deliverable itself; Step 7 (feasibility) runs different checks for chain vs. single-turn.

### 4. Diagnostic (9 dimensions, architecture-aware)

Score the original prompt on these nine dimensions (1–10 each). For any dimension scoring below 7, identify the specific deficiency:

**Structural dimensions (the foundation):**

- **Clarity** — Are instructions unambiguous? Could two different LLMs interpret this the same way?
- **Structure** — Is information organized logically with clear sections? Are steps sequenced by dependency?
- **Constraints** — Are boundaries defined? Does the prompt prevent common failure modes (filler, hedging, scope creep, generic output)?
- **Output format** — Is the expected response shape specified? Would the user know what "done well" looks like?
- **Specificity** — Are instructions concrete enough to act on? Could "improve X" be replaced with "do Y to achieve X"?

**Capability dimensions (the ceiling):**

- **Reasoning depth** — Does the prompt elicit analytical thinking or just surface-level completion? Are there explicit mechanisms forcing genuine reasoning (decomposition, comparison, evaluation, self-critique)?
- **Capability activation** — Does the prompt use Claude-specific techniques to reach deeper modes? Reasoning scaffolds, anti-sycophancy permissions, uncertainty flagging, research/search activation where appropriate?
- **Context economy** — Does the prompt use the context window strategically? No bloat, no redundancy, but also no false economy that strips needed context?
- **Calibration** — Does the prompt's complexity match the actual difficulty of the task? Heavy scaffolding on a trivial task is waste. Light scaffolding on a hard task is failure.

#### Architecture-aware mode (when Step 3 verdict is CHAIN)

When the architecture verdict is CHAIN, the 9 dimensions are evaluated **per-stage** (not on the raw prompt holistically), plus four additional **cross-chain dimensions** that don't exist for single-turn prompts:

- **Handoff cleanliness** — does each stage's output feed cleanly into the next stage's input? Are handoff blocks structured and substitution-ready?
- **Checkpoint design** — are human-in-loop checkpoints placed where judgment genuinely improves output, not ceremonially? Do they have clear continue/redirect mechanics?
- **Variable persistence** — are variables (target asset, comparable, claim-to-test, etc.) declared once and substituted reliably across stages? No re-prompting the user for the same variable mid-flow.
- **Orchestration robustness** — does the Project orchestrator (if Project-orchestrated) handle initialization, stage triggers, preconditions, overrides, and error states without leaking implementation details to the user?

Score these dimensions only when the architecture is CHAIN.

### 5. Capability activation layer

**4.7 calibration note.** Claude 4.7 has raised the floor on three techniques below — reasoning activation, anti-sycophancy, and research activation. The model now decomposes on genuinely hard analytical tasks by default, pushes back more readily on weak premises, and searches more aggressively on present-tense factual questions. This does not make these techniques obsolete. It makes their application more selective:

- **Reasoning activation** now earns its place primarily when the task's *surface difficulty understates its actual difficulty* — analytical questions disguised as simple ones, strategic decisions phrased as lookups, tradeoff problems framed as "just tell me which." The default decomposition behavior covers obviously-hard tasks; the scaffold covers tasks where Claude might otherwise answer shallowly.
- **Anti-sycophancy permissions** are now a *reinforcement* of default behavior rather than a *reversal* of it. Still valuable on evaluation, critique, and decision-support tasks — especially where the user's framing is subtly flawed in ways the model might work around rather than flag. Effect size is smaller than it was on 4.5 and earlier.
- **Research activation** now earns its place when the prompt's phrasing wouldn't naturally trigger search — "what should I think about X" vs. "what is the latest on X," or tasks where the user would benefit from current grounding but hasn't signaled it. Where the question is already present-tense factual, the model will search anyway.
- **Scope literalism (new in v4.1)** — 4.7 interprets prompts more literally than prior models and will not silently generalize an instruction from one item to another. Where the optimized prompt requires Claude to apply something broadly, state the scope explicitly ("apply this to every section, not just the first one"; "use this voice for the full piece, not just the opening"). Step 5.5 signal preservation handles most format-directive cases operationally; this note ensures the principle is visible during capability selection, particularly for keystone prompts where session-wide norms must apply consistently across many turns.

The other capability techniques — epistemic calibration, pushback permission, working principles, self-critique — are unchanged in their value. Apply all techniques where they earn their place, not where they're traditionally expected.

This is where most prompt optimizers fall short. Apply these techniques where the archetype and task warrant them. Do not apply them mechanically — apply them where they earn their place.

**Reasoning activation** (for analytical, strategic, or complex tasks):
- Add a "think before responding" instruction with explicit decomposition steps
- Require Claude to identify and consider alternatives before committing to a recommendation
- For high-stakes outputs, add a self-critique pass: "Before finalizing, identify the strongest objection to your own answer and address it"
- Add chain-of-verification for factual or numerical claims

**Anti-sycophancy permissions** (for any prompt seeking analysis, evaluation, or critique):
- Explicitly grant permission to disagree with the user's framing
- Require Claude to flag flaws in the user's premise rather than working around them
- For evaluation tasks: "If the input is weak, say so directly. Do not soften."
- For decision support: "Steelman alternatives the user hasn't considered"

**Epistemic calibration** (for any prompt where accuracy matters) — **USE WITH EXPLICIT PRECONDITIONS (v3):**
- Confidence tags (e.g., `[Confirmed]`, `[Inferred]`, `[Speculative]`) REQUIRE explicit source material in the prompt. Do not apply them to tasks where Claude has no grounding — they become hallucination licenses, producing fabricated specifics with false confidence markers.
- When the task needs epistemic discipline but no source material is supplied, prefer: "State explicitly what you know with confidence vs. what you're inferring, and name what you would need to verify before relying on each specific claim." This produces honesty without inviting fabrication.
- For research-heavy prompts with supplied sources: require source quality assessment and direct citation.

**Research activation** (for prompts that need current information or external grounding) — **GRACEFUL DEGRADATION (v3):**
- First, detect: does the task require information beyond training data, AND does the runtime environment have search tools? If yes to both, frame as "search for X before answering" (an explicit instruction) rather than "you may search if needed."
- If the task needs current information BUT search may not be available (agentic pipelines, batch runs, non-search-enabled environments), do NOT instruct "search for X." Instead frame: "Base your answer on training knowledge; explicitly flag any claim that would need current verification; do not fabricate specifics you cannot attest to." This keeps the prompt robust across runtime contexts.
- Detect ambiguity: if you cannot tell whether search is available in the runtime, default to the conservative (no-search) framing. A prompt that assumes search in a search-less environment fails loudly; the reverse degrades gracefully.

**Pushback and challenge** (for keystone prompts and decision-support tasks):
- Grant explicit permission to refuse the framing if it's flawed
- Require Claude to ask clarifying questions before proceeding when ambiguity is high-stakes
- For strategy prompts: require Claude to identify what would change its recommendation

**Working principles** (for keystone prompts):
- Establish persistent norms for the session: how to handle uncertainty, when to ask vs. assume, what counts as "done"
- Define the relationship: collaborator, critic, executor, advisor — they're different
- Set escalation paths: what to do when stuck, when to push back, when to deliver partial work

### 5.5. Signal preservation pass

**Before restructuring, enumerate every signal the raw prompt carries.** Signals are compressed intent. Structural expansion that overwrites them produces a prompt Claude executes perfectly to the wrong target.

Scan the raw prompt for all of the following. Log each one you find. Each must be either preserved verbatim or explicitly overridden with a rationale in the change log.

**a) Explicit format directives.** Phrases like "side by side", "in a table", "as a comparison", "bullet points only", "one paragraph", "1,500 words", "in the style of X". These are non-negotiable. If the user asked for a table, the optimized prompt asks for a table. If the user said "both analyses side by side," the optimized `<output_format>` must be "side-by-side structure" — not "Section A, then Section B, then a comparison section."

**b) Unfilled placeholders.** Tokens like `[CV]`, `[JD]`, `<DOCUMENT>`, `{INPUT}`, `[paste here]`, or any bracketed ALL-CAPS or clearly-templated token. These indicate the user plans to paste material the prompt cannot operate without. The optimized prompt MUST NOT wrap these in a `<materials>` tag or anywhere that implies material is already present — doing so causes Claude to fabricate imagined content in place of the missing input. Instead, either (i) include an explicit conditional — "If placeholders are not filled, ask the user to provide the material before proceeding" — or (ii) require the first action to be a check that material is present.

**c) Ambiguity hints.** Soft phrases like "I have a draft to share", "let me know what else you need", "happy to share more context", "I can send X if useful". These mark places where the user has NOT yet decided whether to include input or how the workflow will run. The optimizer MUST NOT convert these into locked workflows that require the user to supply the missing input before Claude can act. Preferred pattern: phrase the task so Claude can produce its best immediate output AND offer to incorporate the additional material when received.

**d) Explicit prohibitions.** Phrases like "don't invent X", "work only with what I've given you", "no hallucination", "no fabrication", "cite only real sources". These are protective constraints the user is actively flagging. They must be preserved verbatim or strengthened — never softened, never dropped.

**e) Workflow hints.** Phrases that reveal the user's implicit operating mode: "first pass", "quick and dirty", "I'll iterate", "final version", "this ships today". These calibrate how much Claude should invest in the response. A "first pass" prompt should not get an exhaustive keystone treatment.

**f) Length/scope markers.** Word counts, slide counts, page budgets, "brief", "comprehensive", "quick". If the user specified a scope, that scope is the ceiling, not the floor.

**Output of this step:** a short bulleted list of the signals detected, each marked PRESERVED, STRENGTHENED, or OVERRIDDEN (with rationale). Carried forward into Step 6 and Step 8.

### 6. Optimization (architecture-branched)

Rewrite the prompt applying both structural techniques and capability activation. Use these tools where they earn their place — never decoratively. Every structural addition must be compatible with the Step 5.5 signal inventory; any tool that would override a preserved signal is disallowed.

**The deliverable from this step depends on the Step 3 architecture verdict.**

#### Branch A: SINGLE-TURN or SINGLE-TURN (CEILING)

Produce a single optimized prompt in a code block.

**Structural tools:**
- `<role>` — define who Claude embodies and what expertise it brings. Be specific about depth (not "analyst" but "senior analyst with 15 years in X who has internalized Y framework")
- Concrete sub-instructions with defined outputs replacing vague imperatives
- **Context-first ordering** — when the prompt contains significant reference material (documents, data, examples, prior work), place that material BEFORE the task or question, not after. Claude processes the context first and encounters the instruction with full grounding loaded. Instruction-first ordering forces retroactive reinterpretation and measurably degrades output on long-context tasks. For short prompts with no reference material, ordering is irrelevant.
- `<constraints>` to prevent the prompt's most likely failure modes
- **Motivated constraints** — where a constraint's boundary is non-obvious or where misapplication is a common failure mode, state WHY the constraint exists, not just what it is. Claude applies constraints with rationale more intelligently at the edges than bare rules. Do not motivate every constraint — only those where the rationale changes how the edge cases should be handled.
- `<output_format>` if the response shape is unspecified. If the user DID specify a shape (Step 5.5, signal a), the format block echoes it — do not invent a different shape.
- `<failure_modes>` describing what bad output looks like
- `<evaluation_criteria>` defining what good output looks like
- `<examples>` (new in v4.1) — when the user has a preference about *how* the output should look (voice, tone, structure, format), include 2–4 worked examples in `<examples>` / `<example>` tags. Examples beat description for steering format; this is among the most reliable structural moves per Anthropic's official prompting guidance. Especially load-bearing for the **Creative archetype** (voice anchoring — addresses the v3 A/B-test complex_creative loss), **Iterative refinement** (consistency lock across runs), and any task where Step 5.5 signal (a) flagged a format preference that description alone can't fully resolve. Skip when the task is generic enough that examples would over-constrain, or when no format preference exists to anchor against. Examples must be relevant (mirror the actual use case), diverse (cover edge cases), and structured (each wrapped in `<example>`).
- **Quote-grounding for long-document tasks** (new in v4.1) — when the prompt loads ≥3K tokens of source material that Claude must analyze (annual reports, long transcripts, multi-document reference sets, dense source material that must be analyzed without drift), instruct Claude to first extract the relevant passages into `<quotes>` tags and ground subsequent reasoning in those quotes. Per Anthropic's long-context guidance, this materially reduces drift and hallucination on document-grounded analytical tasks. Distinct from context-first ordering, which is about *position*; quote-grounding is about *forcing extraction before reasoning*. Skip for short reference material where the extraction step costs more than the grounding benefit, or when context-first ordering alone is sufficient.
- Domain-specific tags where they add precision
- `[bracketed placeholders]` for variable content — ONLY where the user has actual variable content to substitute. Do not invent placeholders the raw prompt did not carry.

**Capability tools:** as specified in Step 5.

For **SINGLE-TURN (CEILING)**, additionally include a one-paragraph strain note in the output (not in the prompt itself) acknowledging which signal fired, what compression was applied, and what the user gives up by staying single-turn. The user can then decide to escalate to chain in a follow-up if the tradeoff isn't worth it.

#### Branch B: CHAIN

Produce a chain deliverable mirroring the validated Project-orchestrated workflow pattern:

1. **README / setup guide** — one-time setup steps + recurring usage flow + maintenance notes
2. **Project orchestrator** (if Project-orchestrated) — the custom instructions block containing role, discipline, research protocol, methodological constraints, output conventions, AND the orchestration protocol that handles variable collection, stage triggers, preconditions, methodology checkpoints, variable overrides, and error states
3. **Stage prompts** — one file per stage, each containing task definition, execution sequence, output format with handoff block, and scope discipline. Variables referenced as `{VARIABLE_NAME}` substituted at runtime by the orchestrator.

For flat-sequential chains, omit the orchestrator and produce only the README plus the standalone stage prompts. The user pastes them sequentially in the same chat.

**Production guidance for chain deliverables:**

- Use the `create_file` and `present_files` tools to deliver each file separately when code execution is available. This mirrors how the user will store and upload them.
- If code execution is not available, produce each file as a clearly-labeled code block in chat with the filename as the block header.
- Each stage file references only the variables it needs. Don't pass `{CONSENSUS_CLAIM_TO_TEST}` into Stage 1 if Stage 1 doesn't use it.
- Handoff blocks at the end of each stage are structured, machine-readable, and substitution-ready for the next stage.
- The orchestrator handles variable collection at chat initialization (the user shouldn't have to declare variables in each stage trigger).
- The orchestrator handles methodology checkpoints and variable overrides explicitly.

Preserve the original's intent, voice, and core logic across all stages. Even at IN PURVIEW + CHAIN, do not pile on techniques that don't earn their place.

### 7. Feasibility pass (architecture-branched)

Run the checks below. Each can *reduce* the optimization, not just extend it.

#### Branch A: SINGLE-TURN / SINGLE-TURN (CEILING)

**a) Single-turn output budget.**
Count the distinct substantive sections the optimized `<output_format>` asks for. Count deep analytical asks (e.g., "evaluate against 5 criteria," "apply two frameworks in parallel," "produce a ranked list of 10 with rationale each").

Thresholds (calibrated to Claude's ~8K-token single-turn ceiling on long-form outputs):
- **≤ 4 sections AND ≤ 2 deep analytical asks** → safe, proceed.
- **5–6 sections OR 3 deep analytical asks** → at risk. This is the SINGLE-TURN (CEILING) band. Compress: merge related sections, trim decorative scaffolding, or prioritize explicitly ("produce section 1 fully; sections 2–4 as concise notes"). Make the strain visible in the output.
- **> 6 sections OR > 3 deep analytical asks** → infeasible in one turn. This should not appear at this point — Step 3 should have called CHAIN. If you're seeing it, revisit Step 3.
- **Long-form creative with a word-count ceiling** (e.g., 1,500-word op-ed): do not add structural sections on top — Claude must spend its output budget on the actual piece, not on meta-sections about the piece.

**b) Context-dependency honesty.**
If the prompt depends on information Claude may not have, and the runtime may not have search:
- Replace "search for X" with "base on training knowledge; flag uncertainty explicitly; do not fabricate specifics you cannot attest to."
- If `[Confirmed]/[Inferred]` confidence tags were added in Section 5, verify they have source material to operate on. If not, remove them; they become hallucination licenses.
- If the prompt names specific entities and expects current intelligence about them (exec hires, earnings dates, partnership announcements), explicitly instruct: "If you lack verified current information about [entity], say so. Do not generate plausible-sounding specifics."

**c) User-format preservation.**
Re-read the raw prompt one more time. Find every format directive (Step 5.5 signal a). Verify each one is either reflected in the optimized `<output_format>` or explicitly overridden in the change log.

**d) Input-presence conditional.**
If Step 5.5 flagged unfilled placeholders, verify the optimized prompt contains the explicit conditional: "If [placeholders] are not filled with actual material, first ask the user to provide it before proceeding."

**Output of this step (Branch A):** a 2–4 bullet feasibility verdict. If any check failed, modify the optimized prompt before proceeding to the change log.

#### Branch B: CHAIN

Two checks run for chain deliverables.

**a) Per-stage feasibility.**
For each stage, apply the same single-turn budget check from Branch A above. Each stage individually must be feasible in one Claude turn. If a stage breaches the threshold, either split it into sub-stages or compress its output specification.

**b) Cross-chain feasibility.**
Two cross-chain checks:
- **Optimizer turn budget** — can the optimizer produce the full chain deliverable (README + orchestrator + N stages + diagnostic + use case guidance) in one of its own turns? For 3-stage chains, typically yes. For 4+ stage chains, typically no — in that case the optimizer should deliver the architecture decision and skeleton in turn 1, await user confirmation, and deliver the stage files across turn 2 (and possibly turn 3 for very large chains).
- **Handoff contract integrity** — each stage's handoff block must contain every variable the next stage needs. No gaps. No assumptions of prior-turn context that the next stage might not have.

**Output of this step (Branch B):** a per-stage feasibility table plus a cross-chain verdict.

### 8. Change log

For each substantive change, state: what was changed, why, and what failure mode it prevents or what quality dimension it improves. Map each change to either a diagnostic dimension, a capability activation technique, a preserved signal (Step 5.5), an architecture decision signal (Step 3), or a feasibility correction (Step 7). Specific, traceable changes only.

For chain deliverables, also include a brief architectural change log: which stages exist, what each does, why this decomposition rather than another, and where checkpoints sit.

### 9. Use case guidance

State when this prompt (or chain) is most effective, when it's not the right tool, and what complementary prompts (if any) it pair well with. For keystone prompts, also state what the user should expect from the session it initiates.

For SINGLE-TURN deliverables: state **what single-turn output to expect from this prompt.** If the optimized prompt is built to fit Claude's single-turn budget, say so. If it is built to span multiple turns, state the turn structure.

For CHAIN deliverables: state the operational flow (open chat, type X, answer Y, trigger s1 / s2 / s3), the expected output of each stage, where the methodology checkpoint sits, and any maintenance notes for editing stages independently.

---

## IN PURVIEW output formats

The IN PURVIEW path produces one of two distinct output formats, selected by the Step 3 architecture verdict.

### Path C1 — SINGLE-TURN output format

Use for ARCHITECTURE verdicts of SINGLE-TURN or SINGLE-TURN (CEILING).

Return exactly these sections (the triage line plus the analytical sections):

**TRIAGE: IN PURVIEW** — [one-sentence rationale]

**SECTION 1 — INTENT & ARCHETYPE**
The intent statement (2–3 sentences) and the detected archetype with brief rationale.

**SECTION 2 — ARCHITECTURE DECISION**
The Step 3 verdict, signals fired (named explicitly), and — for SINGLE-TURN (CEILING) — the strain note explaining what the user gives up by staying single-turn.

**SECTION 3 — DIAGNOSTIC**
The nine-dimension scoring, presented as a clean table or structured list, with specific deficiency notes for any dimension below 7. Group by structural vs. capability dimensions.

**SECTION 4 — SIGNAL INVENTORY**
A bulleted list of signals detected in the raw prompt per Step 5.5, each marked PRESERVED, STRENGTHENED, or OVERRIDDEN (with rationale).

**SECTION 5 — OPTIMIZED PROMPT**
The full rewritten prompt in a code block. Must be copy-paste ready and self-contained.

**SECTION 6 — FEASIBILITY VERDICT**
The feasibility checks from Step 7 Branch A and their verdicts. Explicit statement of single-turn feasibility.

**SECTION 7 — CHANGE LOG**
A concise list of changes, each tagged with the diagnostic dimension, capability technique, preserved signal, architecture signal, or feasibility correction it addresses.

**SECTION 8 — USE CASE GUIDANCE**
When to use this prompt, when not to, what it pairs with, what single-turn output to expect, and (for keystone prompts) what to expect from the session.

### Path C2 — CHAIN output format

Use for ARCHITECTURE verdicts of CHAIN.

**This is a two-turn deliverable.** Turn 1 delivers the architecture decision and skeleton, asking the user to confirm Project-orchestrated vs. flat-sequential. Turn 2 (after the user's choice) delivers the full chain.

#### Turn 1 — Architecture & skeleton

Return exactly these sections:

**TRIAGE: IN PURVIEW** — [one-sentence rationale]

**SECTION 1 — INTENT & ARCHETYPE**
The intent statement (2–3 sentences) and the detected archetype with brief rationale.

**SECTION 2 — ARCHITECTURE DECISION**
The Step 3 verdict (CHAIN), primary signals fired (named explicitly), amplifiers (named), and the chain shape rationale. Make clear which signals justified the chain — the recommendation must be falsifiable.

**SECTION 3 — CHAIN SKELETON**
- Number of stages
- One-sentence purpose of each stage
- Where the human-in-loop checkpoints sit and what decision each one captures
- Handoff contracts (what each stage passes to the next)

**SECTION 4 — DELIVERY VEHICLE QUESTION**
State the heuristic recommendation (Project-orchestrated or flat-sequential) and the reasoning. Ask the user explicitly:

> "Recommended delivery: [Project-orchestrated | flat-sequential]. Confirm or switch?"

Then stop. Do not produce the full chain deliverables until the user has answered.

#### Turn 2 — Full chain deliverable

After the user confirms or switches, deliver the remaining sections.

**SECTION 5 — DIAGNOSTIC (per-stage + cross-chain)**
The 9 dimensions evaluated per-stage where meaningful, plus the 4 cross-chain dimensions (handoff cleanliness, checkpoint design, variable persistence, orchestration robustness).

**SECTION 6 — SIGNAL INVENTORY**
A bulleted list of signals detected in the raw prompt per Step 5.5, each marked PRESERVED, STRENGTHENED, or OVERRIDDEN.

**SECTION 7 — DELIVERABLES**
Each file as a separate `create_file` call (preferred when code execution is available) or a clearly-labeled code block (fallback):

- `README-setup.md` — one-time setup + recurring usage flow + maintenance notes
- `project-instructions.md` — the orchestrator (Project custom instructions) — included only for Project-orchestrated chains
- `stage-1-[name].md`, `stage-2-[name].md`, ... — one per stage

After producing the files, call `present_files` with all of them.

**SECTION 8 — FEASIBILITY VERDICT**
Per-stage feasibility table + cross-chain verdict (Step 7 Branch B).

**SECTION 9 — CHANGE LOG**
The standard change log plus the architectural change log (Step 8).

**SECTION 10 — USE CASE GUIDANCE**
Operational flow for the chain, expected output per stage, checkpoint locations, and maintenance notes.

---

## Constraints

- Never skip Step 0 triage. The 9-dim diagnostic does not run on OUT OF PURVIEW inputs.
- Never skip Step 3 architecture decision. Defaulting silently to single-turn on prompts that deserved a chain is the alignment failure v4 was built to correct.
- Never skip Step 5.5 signal preservation. Structural expansion that overwrites raw-prompt signals is a v3 SEVERE failure mode.
- Never skip Step 7 feasibility pass. A prompt that over-specifies the turn is a broken prompt no matter how beautifully scaffolded.
- Never add XML tags decoratively. Every tag must contain instructions the prompt would be worse without.
- Never strip personality, voice, or domain-specific language from the original. Optimize structure and capability activation, not character.
- If the original prompt is already strong (scores 7+ across most dimensions and the archetype is correctly served), say so and make only surgical improvements. Do not rewrite for the sake of rewriting.
- The optimized prompt must be self-contained — a user should be able to paste it into Claude and get state-of-the-art output without needing this skill's context.
- Do not add examples decoratively — only where they earn their place per the Step 6 Branch A `<examples>` calibration (Creative archetype voice anchoring, Iterative refinement consistency, or unresolved format preferences flagged in Step 5.5). Examples are expensive in tokens; the calibration in Step 6 is the governing rule.
- Capability activation techniques must be calibrated to the task. Anti-sycophancy permission on a recipe request is absurd. Research activation on a creative writing prompt is wrong. Apply techniques where they earn their place.
- Never apply all capability techniques to every prompt. The skill is not a checklist — it's a calibration exercise.
- Never apply XML scaffolding OR capability activation layer to BORDERLINE or OUT OF PURVIEW outputs. They are not eligible for these techniques by design.
- Never wrap unfilled placeholders as if they are filled material. Never convert ambiguous user hints into locked workflows.
- Never produce a chain when the signals don't justify it. Single-turn is the default; chain has to earn it.
- Never produce a single-turn prompt when 2+ primary chain signals fired, even if a single-turn prompt is technically deliverable.
- Always name the architecture signals that fired. An unnamed verdict is unfalsifiable.

---

## Failure modes to avoid

### SEVERE failure modes (invalidate the optimization; treat as bugs, not style)

1. **TRIAGE BYPASS.** Running Steps 1–9 on any input without Step 0 having returned IN PURVIEW. The 9-dim diagnostic is not free and not harmless — applied to simple prompts, it actively degrades downstream Claude output.

2. **OVER-ENGINEERING SIMPLE PROMPTS.** Wrapping casual communication tasks in XML scaffolding, bracketed placeholders, capability activation layers, or 6-section outputs. If the triage called OUT OF PURVIEW, the output path is the 3-line return-mostly-unchanged format — no exceptions.

3. **BAND-MIXING.** Producing a BORDERLINE output with XML scaffolding, or an OUT OF PURVIEW output with a 9-dim diagnostic. Each band has exactly one output format.

4. **SIGNAL OVERRIDE.** Structural expansion that overwrites signals the raw prompt actively carried. Four specific pathologies:
   - Wrapping unfilled placeholders (`[CV]`, `[JD]`) as if material were present
   - Converting ambiguity hints into locked workflows
   - Applying `[Confirmed]/[Inferred]` confidence tags to tasks without source material
   - Dropping or mutating explicit format directives

5. **INFEASIBLE SINGLE-TURN SCOPE.** Specifying more output sections or analytical depth than Claude can deliver in one turn, without converting to a chain. This is the failure mode the architecture decision in v4 was built to prevent at the source rather than catch downstream.

6. **ARCHITECTURE MIS-SELECTION (new in v4).** Three sub-pathologies:
   - **Silent single-turn default** — producing a single-turn deliverable when 2+ primary chain signals fired, without naming the signals or explaining why chain was rejected. This is the v3 ceiling the architecture decision exists to break.
   - **Chain over-engineering** — producing a chain when 0 primary signals fired (chain-as-sophistication-theater). Single-turn is the default; chain must earn it.
   - **Chain shape fabrication** — producing a 5-stage chain when 3 stages would suffice, or splitting a stage just to inflate the chain count. The smallest chain that does the job is the right chain.

7. **HIDDEN STRAIN (new in v4).** Producing a SINGLE-TURN (CEILING) output without flagging the strain to the user. The whole point of the middle band is making the silent ceiling visible. Output that compresses without acknowledging the compression is back to v3's failure mode.

8. **UNNAMED ARCHITECTURE VERDICT (new in v4).** Producing a chain (or refusing to chain) without naming the specific signals that fired or didn't. The verdict must be falsifiable — the user needs to be able to push back on individual signal assessments.

### Common (soft) failure modes

**BAD OUTPUT looks like:** adding a `<role>` tag to every prompt regardless of need, wrapping simple instructions in XML without improving them, mechanically applying every capability activation technique to every prompt, producing a change log that says "added structure for clarity" without specifying what structure and what clarity, rewriting a prompt so heavily that the original author wouldn't recognize their intent, producing optimizations that work on any LLM rather than specifically activating Claude's strengths, or producing a chain architecture without explicit named-signal justification.

**The most common failures:**
1. Confusing "more structured" with "better." A well-written paragraph can outperform a poorly conceived XML scaffold.
2. Confusing "hygiene" with "ceiling." A perfectly structured prompt that doesn't activate Claude's capabilities is a 7/10, not a 10/10.
3. Cargo-culting capability techniques onto prompts that don't need them. The skill is calibration, not maximalism.
4. Producing optimizations that would work identically on GPT-4, Gemini, and Claude — that means no Claude-specific elicitation is happening.
5. **(new in v4)** Treating the architecture decision as a downstream consequence of single-turn polish rather than as the first-class upstream choice it should be.

---

## Evaluation criteria

**For IN PURVIEW + SINGLE-TURN outputs**, a strong optimization will:
- Improve at least 3 of the 9 diagnostic dimensions by 2+ points each
- Preserve the original's intent and voice
- Preserve every signal flagged in Step 5.5, or explicitly document the override
- Apply at least one capability activation technique where the task warrants it (or explicitly state why none apply)
- Produce a change log where every entry traces to a specific diagnostic finding, capability technique, preserved signal, architecture signal, or feasibility correction
- Result in a prompt that produces measurably better Claude output than the original on the same input
- Pass the feasibility pass honestly — explicit single-turn or chain framing, no budget-blind scaffolding
- Name the architecture verdict explicitly and (for SINGLE-TURN (CEILING)) acknowledge the strain

**For IN PURVIEW + CHAIN outputs**, a strong optimization will:
- Justify chain selection with 2+ named primary signals (or 1 primary + 2+ amplifiers with explicit escalation note)
- Produce the smallest chain that does the job
- Place human-in-loop checkpoints only where judgment materially improves output
- Define clean, structured, substitution-ready handoff contracts between stages
- Specify variable persistence so the user never re-declares the same variable mid-flow
- For Project-orchestrated chains: orchestrator handles initialization, stage triggers, preconditions, methodology checkpoints, variable overrides, and error states without leaking implementation details to the user
- Deliver as separate files (`create_file` + `present_files`) when code execution is available
- Result in a workflow where the user runs the chain with minimum cognitive overhead per execution

**For BORDERLINE outputs**, a strong optimization will:
- Close 1–3 specific gaps the original left open
- Preserve length proportionality — the optimized prompt should not be more than ~2x the original's length
- Contain zero XML scaffolding and zero capability activation tags
- Produce a change log of 2–4 plain-language bullets

**For OUT OF PURVIEW outputs**, a strong optimization will:
- Return the prompt substantially unchanged
- State the triage rationale in one sentence
- Add at most one surgical correction if something is genuinely missing (typically a date or audience)
- Be no longer than 5 lines total

The ultimate test for IN PURVIEW: if the user runs the optimized prompt (or chain) as the keystone of a new Claude session, does the session produce output that surprises them with its depth, accuracy, and utility? That's state-of-the-art. For OUT OF PURVIEW, the ultimate test is simpler: did the optimizer get out of the way?

