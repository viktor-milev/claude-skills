---
name: prompt-optimizer-fable
description: Fable-variant rigorous prompt optimizer — audits a polished prompt and rewrites it to elicit state-of-the-art output from Claude Fable 5, with surface-aware run settings (chat app, Claude Code, Cowork, harness). Triages into OUT OF PURVIEW / BORDERLINE / IN PURVIEW; decides output architecture (single-turn vs. chained multi-stage) and produces a chain deliverable (README + orchestrator + stage prompts) when signals fire. Fires ONLY on explicit Fable invocation — trigger phrases include "PO this for Fable", "optimize this prompt for Fable 5", "fable-optimize this", "run the fable optimizer". Do NOT trigger on unqualified requests ("PO this", "optimize this prompt") — those route to prompt-optimizer (Opus sibling). Do NOT trigger for messy dictated or flow-state input (use prompt-optimizer-flow), and do NOT trigger when the user wants the prompt EXECUTED rather than optimized.
---

# Prompt Optimizer — Fable 5 Edition

## CHANGELOG

**v1.0-fable — Jul 2026.** Standalone fork of `prompt-optimizer` v4.2 (Opus 4.8), recalibrated for **Claude Fable 5**. Produced through an internal maintenance-review process (staged review against two empirical exhibits); ruling standards: the official Anthropic *Prompting Claude Fable 5* guide and the Jul 2026 effort documentation. The Opus sibling remains in use, unchanged.

**Lockstep contract (governance).** The architecture decision module — the seven signals, verdict logic, and guards — plus its threshold constants are bound in lockstep with `prompt-architect-fable`: any change there propagates to both Fable skills. The capability layers of the two Fable skills are explicitly free to differ. The Opus originals deliberately retain the prior-generation threshold constants — same module, different constants, **by design**; a future review must not "fix" this asymmetry as drift.

Changes vs. v4.2:
- **Effort model rewritten** for the Fable 5 ladder (low / medium / high / xhigh / max; high default) with bidirectional calibration and counter-instruction pairing (Step 9.5).
- **Surface parameter added** (chat / code / cowork / harness) gating RUN SETTINGS and agentic techniques (Intake, Step 9.5).
- **Architecture thresholds recalibrated (LOCKSTEP):** fidelity test primary; provisional numeric backstop, VALIDATE-marked; prior-generation bands retired on direct empirical disconfirmation (Step 3 signal 7, Step 7a).
- **Capability layer re-derived and merged** into a single Fable 5 layer — calibration inline per technique, legacy tool lists collapsed (Step 5).
- **Reasoning-echo guard added** — never install "show your reasoning/thinking" phrasings; refusal-risk rationale documented (Step 5, Constraints, SEVERE failure mode 9).
- **Intent-context and brevity/selectivity added** as first-class structural tools (Step 6).
- **Agentic archetype rebuilt** around the four documented Fable 5 patterns + surface-gated subagent/verifier/memory guidance (Step 2).
- **Conditional chain delivery:** full one-turn delivery when the vehicle heuristic is unambiguous; confirmation pause only when marginal (Step 3, Path C2).
- **Prose-economy pass** with the discrimination rule: behavioral micro-instruction deduplicated to one statement per rule; content specification preserved at density.
- **`<examples>` calibration amended** for Fable 5 (famous registers: description may suffice; n=1 marker) (Step 6).
- **New frontmatter description:** explicit-invocation-only triggering in the four-skill routing space.

Provisional items carried with VALIDATE markers: the numeric feasibility backstop (calibrated on one exhibit + documented longer turns); the `<examples>` famous-register amendment (n=1). Tighten or relax only with evidence.

---

Optimize prompts to reach the ceiling of what Claude Fable 5 can produce on a given task — but ONLY when the task warrants it. Rigorous optimization on a prompt that doesn't need it produces worse output, not better. The skill's first job is to decide whether to optimize at all.

## Core philosophy

**Stop optimizing prompts. Start optimizing the Claude session that the prompt initiates.**

A prompt is not a request. It is the keystone of an interaction. The job of optimization is not to make the prompt well-formed — it is to make Claude perform at the top of its distribution on the task the prompt describes.

This logic only applies when the task has a ceiling worth reaching. A PTO email does not. Forcing XML scaffolding, capability activation, and multi-section diagnostic outputs onto casual communication tasks makes the downstream output *worse* — it bloats the prompt, buries the actual ask, and ships Claude a bureaucratic commission when the user wanted a 3-sentence note. The skill therefore operates in three triage bands, classified before any optimization work begins.

Three further philosophies govern every run:

- **Preserve the user's signals; restructure only what the user didn't specify** (v3). The raw prompt is the user's compressed intent. Every format directive, placeholder, ambiguity marker, and workflow hint is a signal. Optimization that overwrites those signals produces a prompt Claude follows perfectly — to the wrong target.
- **Choose the architecture before choosing the structure** (v4). Single-turn is the default delivery vehicle, but not the only one. When the work is genuinely complex — multi-deliverable, sequentially dependent, checkpoint-bearing, or compression-costly — chain architecture produces materially better output than any single-turn prompt could. Make that call explicitly, name the signals, and produce a chain deliverable when chain is the right answer.
- **On Fable 5, restraint is activation** (v1.0-fable). Fable 5's instruction-following is strong enough that one brief, clear instruction steers behavior that previously required enumeration — and instruction sets written for prior models can actively degrade its output. Every added rule, tag, and scaffold must clear a higher bar than on any prior model. Deduplication and deletion are capability-activation moves, not cleanup.

## Intake — surface declaration

`SURFACE ∈ {chat (default) | code | cowork | harness}` — where the optimized prompt will run.

Read only from an explicit user declaration ("this is for Claude Code", "harness prompt", "Cowork task"); never infer from topic. If absent, default to **chat** and state the assumption in one clause inside RUN SETTINGS. SURFACE gates Step 9.5 content and the applicability of agentic techniques (Steps 2, 5, 6). It does not affect triage.

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
- OUT leaning: low-consequence communication — casual content, familiar or non-specific audience, and a mediocre version still accomplishes the task (PTO email to known boss, tweet about your day, routine Slack update)
- BORDERLINE leaning: externally-facing or reputationally-significant artifact — first-impression content, cold outreach, professional networking, short business documents where tone and specificity materially affect whether the artifact achieves its purpose
- IN leaning: session keystone, published document, work product shared with stakeholders, or anything that will be iterated on

### Verdict rules

- **3 or 4 OUT leanings → OUT OF PURVIEW.** Short-circuit. Do not run the full workflow.
- **3 or 4 IN leanings → IN PURVIEW.** Run the full workflow below.
- **Mixed or 3+ BORDERLINE leanings → BORDERLINE.** Run abbreviated workflow.
- **Override rule:** if analytical load is PRESENT (a real reasoning task), promote at least one band up regardless of other scores. "Should I take this job?" at 8 words is IN PURVIEW, not OUT.
- **Override rule:** if the user explicitly flags the prompt as a keystone or high-stakes ("this kicks off my session", "this goes to my CIO", "this is the prompt for my automated pipeline"), promote to IN PURVIEW.

### Output the verdict in one line

> **TRIAGE: [OUT OF PURVIEW | BORDERLINE | IN PURVIEW]** — [one-sentence rationale citing the two or three tests that drove the call]

If the user disagrees, they can override in their next message. Proceed to the band-appropriate output path.

---

## Band-specific output paths

### Path A — OUT OF PURVIEW output format

Return exactly this, and nothing else:

> **TRIAGE: OUT OF PURVIEW** — [rationale]
>
> **Verdict:** this prompt is already well-calibrated for its task. Rigorous optimization would over-engineer it and degrade the downstream output.
>
> **Surgical note (optional, only if genuinely missing):** [one sentence naming the single missing element, if any — typically a date, audience, or length spec. If nothing is missing, write "None — send as is."]

That is the entire response — no diagnostic table, archetype detection, architecture decision, XML scaffolding, change log, feasibility check, run settings, or use case guidance.

**Self-check before finalizing Path A.** If your surgical note names 2 or more distinct gaps, the triage miscalled — a prompt with 2+ real gaps is BORDERLINE by the skill's own definition. Re-classify and route to Path B. Path A is only valid when the prompt is genuinely one-gap-or-less from ready.

### Path B — BORDERLINE output format

Return exactly three sections:

**SECTION 1 — TRIAGE & DIAGNOSIS.** One line of triage verdict. Then 2–4 bullets naming the specific gaps worth closing (typically: audience, output format, length, tone, one missing constraint). Do NOT run the full 9-dimension scoring or the architecture decision — they are theater at this band.

**SECTION 2 — OPTIMIZED PROMPT.** The original prompt with surgical additions — typically 1 to 3 added sentences or constraints, inline. NO XML scaffolding. NO capability activation layer. If the original works as a paragraph, it stays a paragraph. Copy-paste ready.

**SECTION 3 — CHANGE LOG.** Two to four plain-language bullets — what changed and why. No diagnostic dimension tags.

No feasibility check, use case guidance, architecture decision, or effort recommendation — high (the Fable 5 default) is always right at this band. The one permitted addition: if the task genuinely needs current information and would fail silently with web search off, append a single line — "Run with web search on." Nothing more.

### Path C — IN PURVIEW output format (the full rigorous treatment)

Proceed through Steps 1–9.5 below. The output format branches on Step 3's architecture decision: SINGLE-TURN template (Path C1) or CHAIN template (Path C2). See "IN PURVIEW output formats."

---

## IN PURVIEW workflow (Steps 1–9.5)

Apply ONLY when Step 0 returned IN PURVIEW.

### 1. Intent extraction

Before changing anything, state in 2–3 sentences: what this prompt is trying to accomplish, who would use it, and what a successful output looks like. This is the north star — every optimization must serve this intent.

### 2. Archetype detection

Identify which archetype the prompt belongs to. Different archetypes require different optimization patterns:

- **Keystone prompt** — the first prompt in a new chat or project. Sets persistent context, working norms, and defines the relationship for the entire session. Needs the heaviest treatment: role depth, working principles, output norms, escalation paths.
- **One-shot task** — a single self-contained request. Needs tight scope, clear output format, and constraints. Lighter optimization. (Most one-shots are triaged out by Step 0; if you're here, it's genuinely complex.)
- **Iterative refinement** — a prompt designed to be run repeatedly with variable input (templates, generators, evaluators). Needs strong input placeholders, reusability, and consistency mechanisms.
- **Agentic task** — a prompt that initiates autonomous or semi-autonomous multi-step work involving tools, research, or long runs. On Fable 5 this archetype needs the **four documented agentic patterns**, installed as brief instructions, not enumerations:
  - *Checkpoint discipline* — pause for the user only when the work genuinely requires them: a destructive or irreversible action, a real scope change, or input only they can provide; otherwise keep going, and ask-then-end-turn rather than ending on a promise.
  - *Evidence-grounded progress claims* — before reporting progress, audit each claim against a tool result from the session; report only what can be pointed to, and say explicitly what is not yet verified. (Documented to near-eliminate fabricated status reports.)
  - *Boundaries* — when the user is describing a problem or thinking out loud rather than requesting a change, the deliverable is the assessment; report findings and stop, don't apply fixes unasked.
  - *Anti-overplanning* — when enough information exists to act, act; don't re-derive established facts, re-litigate settled decisions, or survey options that won't be pursued.
  On agentic surfaces (`code` / `cowork` / `harness`), additionally consider: delegation guidance for parallel subagents, a fresh-context **verifier subagent** at defined intervals (outperforms self-critique on long runs), and a **memory/notes file** for lessons across runs (one lesson per entry, why it mattered, no duplicates). Never install agentic machinery on chat-surface prompts that aren't genuinely agentic.
- **Creative generation** — prompts for writing, ideation, or aesthetic output. Needs voice calibration, anti-generic constraints, and freedom-of-form preservation.

State the archetype explicitly. If the prompt straddles multiple, name the primary and note the secondary.

Archetype is orthogonal to architecture (Step 3): archetype is the cognitive nature of the work; architecture is the delivery vehicle.

### 3. Architecture decision

The skill's job is to produce state-of-the-art output — not well-formed single-turn prompts. Those objectives diverge on complex work, where forcing a single-turn structure compresses output the user could otherwise have had.

This step decides the *output architecture*: single-turn, single-turn (ceiling) (strained but fits), or chain (sequential stage prompts with handoffs and optional human-in-loop checkpoints). The decision drives downstream branching of Steps 4–7.

**Default:** single-turn. Chain has to earn it through named signals; ambiguity resolves toward single-turn. On Fable 5 the bar for chain is *higher* than on prior models — longer default turns mean genuinely large work fits in one turn (see Step 7a).

#### Trigger signals

Seven signals. Primary signals (1–4) drive the verdict. Amplifiers (5–7) modulate within bands.

**Primary signals:**

1. **Multi-deliverable** — the prompt requests two or more distinct artifacts with materially different shapes (e.g., data sheet + analytical memo + scenario matrix). Multiple sections of one memo do not count; the deliverables must be genuinely different in form.
2. **Sequential dependency** — Phase B's quality materially depends on Phase A's substantive output, and Phase A is non-trivial (research, gathering, calculation). Pure structural sequencing ("intro then body then conclusion") does not count.
3. **Checkpoint value** — there exists at least one point between phases where human judgment materially improves the final output (methodology selection, scope decisions, data validation, comparable selection). If a "continue" between phases would only be ceremonial, this signal does not fire.
4. **Compression cost** — fitting the full work into one turn would force compression of sections that lose fidelity. A 3×2 scenario matrix compressed into six bullets is the canonical example. On Fable 5, test this with the Step 7a fidelity test, not by section-counting.

**Amplifying signals:**

5. **Reusability premium** — the prompt will be re-run many times across different inputs (>5 expected runs). Chain infrastructure setup cost amortizes; single-use single-turn does not benefit from chain structure.
6. **Cognitive mode switching** — the work requires distinct cognitive modes that benefit from clean separation (research/gathering vs. synthesis vs. creative output vs. critique). Mixing modes in one turn produces worse output than running them sequentially.
7. **Volume overflow** — output genuinely exceeds the single-turn budget even with disciplined compression. Fable-calibrated anchor: **~10,000 words of dense work** [VALIDATE — provisional; see Step 7a]. Honest assessment, not "we could try to fit it."

#### Verdict logic

Count the primary signals (1–4) that fire:

- **0 primary signals → SINGLE-TURN.** Default. Whether amplifiers fire is irrelevant; no structural reason to chain.
- **1 primary signal → SINGLE-TURN (CEILING).** The strain is acknowledged in the output. Proceed single-turn but flag the specific strain visibly. The user can request chain in follow-up.
- **2+ primary signals → CHAIN.** Name the signals fired. Proceed to chain architecture.

**Amplifier escalation rule:** if exactly 1 primary fires AND 2+ amplifiers fire, the verdict may escalate to CHAIN. The escalation must be explicitly noted ("escalated from SINGLE-TURN (CEILING) on amplifier weight").

**Reusability-only exception:** if only signal 5 fires with no primaries, the verdict stays SINGLE-TURN. Recommend saving the prompt in the user's library — do not build chain infrastructure around a prompt that doesn't structurally need it.

#### Sub-decision when CHAIN: Project-orchestrated vs. flat-sequential

Two chain delivery vehicles:

- **Project-orchestrated** — one-time setup of Project custom instructions + knowledge-base stage files; ongoing runs trigger stages with short commands. Best for high-reuse workflows. Higher setup cost; near-zero per-run friction.
- **Flat-sequential** — standalone stage prompts the user pastes sequentially in the same chat. Best for one-shot complex tasks. Low setup cost; some per-run friction.

**Heuristic:** if signal 5 (reusability premium) fires, recommend Project-orchestrated; if not, flat-sequential.

**Conditional delivery (v1.0-fable).** When the heuristic is **unambiguous** (signal 5 clearly fires or clearly doesn't), deliver the **full chain in one turn** — verdict, skeleton, and all files — naming the heuristic applied inline; the user redirects afterward if they disagree. Pause for confirmation **only when signal 5 is marginal** (genuine ambiguity about reuse): deliver the verdict and skeleton, ask "Project-orchestrated or flat-sequential?", and produce the files in the next turn. Fable 5's output budget removed the old two-turn necessity; the checkpoint survives exactly where it carries decision content.

#### Guards

- **Single-turn remains the default.** Chain must earn it through named signals. Ambiguity resolves to single-turn.
- **Smallest chain that does the job.** Three stages when three suffice; never five when three work. Every stage must justify its existence against its friction cost.
- **Name the signals.** The verdict is not "chain" — it is "chain because signals 1, 2, and 4 fired." Without naming, the recommendation is unfalsifiable.
- **Respect friction costs.** Recommend the lightest architecture that fits the work.
- **Reusability alone does not chain.**

#### Output of this step

> **ARCHITECTURE: [SINGLE-TURN | SINGLE-TURN (CEILING) | CHAIN]**
>
> Primary signals fired: [list]. Amplifiers: [list, or "none"].
>
> [SINGLE-TURN: one-line confirmation. CEILING: the specific strain to be acknowledged and what the user gives up. CHAIN: stage count, one-line purpose each, checkpoint locations, vehicle heuristic + whether delivery is one-turn (heuristic unambiguous, named) or paused for vehicle confirmation (marginal).]

Carry the verdict into Steps 4–7.

### 4. Diagnostic (9 dimensions, architecture-aware)

Score the original prompt on these nine dimensions (1–10 each). For any dimension scoring below 7, identify the specific deficiency.

**Structural dimensions (the foundation):**

- **Clarity** — Are instructions unambiguous? Could two different LLMs interpret this the same way?
- **Structure** — Is information organized logically with clear sections? Are steps sequenced by dependency?
- **Constraints** — Are boundaries defined? Does the prompt prevent common failure modes (filler, hedging, scope creep, generic output)?
- **Output format** — Is the expected response shape specified? Would the user know what "done well" looks like?
- **Specificity** — Are instructions concrete enough to act on? Could "improve X" be replaced with "do Y to achieve X"?

**Capability dimensions (the ceiling):**

- **Reasoning depth** — Does the prompt elicit analytical thinking or just surface-level completion? Are there explicit mechanisms forcing genuine reasoning where the task needs them?
- **Capability activation** — Does the prompt use Claude-specific techniques to reach deeper modes, calibrated per Step 5?
- **Context economy** — Does the prompt use the context window strategically? No bloat, no redundancy — but no false economy that strips needed context.
- **Calibration** — Does the prompt's complexity match the actual difficulty of the task? Heavy scaffolding on a trivial task is waste; light scaffolding on a hard task is failure. On Fable 5, over-instruction is a scored deficiency, not a safe default.

#### Architecture-aware mode (when Step 3 verdict is CHAIN)

Evaluate the 9 dimensions **per-stage**, plus four **cross-chain dimensions**:

- **Handoff cleanliness** — does each stage's output feed cleanly into the next stage's input? Are handoff blocks structured and substitution-ready?
- **Checkpoint design** — are human-in-loop checkpoints placed where judgment genuinely improves output, not ceremonially? Clear continue/redirect mechanics?
- **Variable persistence** — are variables declared once and substituted reliably across stages? No re-prompting the user for the same variable mid-flow.
- **Orchestration robustness** — does the Project orchestrator (if Project-orchestrated) handle initialization, stage triggers, preconditions, overrides, and error states without leaking implementation details to the user?

### 5. Capability activation layer (Fable 5)

**Fable 5 calibration.** Fable 5's instruction-following is strong enough that a brief, clear instruction steers most behaviors that previously required enumeration — and instruction sets written for prior models can degrade its output. Every technique below states its residual gap: apply the technique to the gap the model does not cover by default, not to the whole task. One statement per rule; redundant restatement is itself a defect on Fable 5.

- **Reasoning activation.** Thinking on Fable 5 is adaptive; depth lives in the effort setting (Step 9.5), never in the prompt — a generic "think step by step" instruction almost never earns its place. A *specific reasoning structure* is still the prompt's job — forced alternative-generation, a self-critique pass ("before finalizing, identify the strongest objection to your own answer and address it"), chain-of-verification on numerical claims — and earns its place primarily where the task's surface difficulty understates its actual difficulty. **Reasoning-echo guard (mandatory):** never instruct Fable 5 to show, echo, transcribe, or explain its internal reasoning, thinking, or chain-of-thought as response text — such phrasings can trigger the `reasoning_extraction` refusal and reroute the session to a fallback model. Frame all verification and self-critique as *response content* ("state what you verified and the outcome"; "present your conclusion and the strongest objection you addressed"), never as reasoning transcription.
- **Anti-sycophancy permissions.** Held at full strength — explicitly *house-validated / guide-silent*: the official Fable 5 guide gives no evidence of improvement on the reflexive-validation axis (pushing back on the *user's framing*, distinct from honesty about the model's own work). Grant explicit permission to disagree with the framing; require flaws in the premise to be flagged rather than worked around; for evaluation tasks, "if the input is weak, say so directly — do not soften"; for decision support, steelman the alternatives the user hasn't considered. Fully valuable on evaluation, critique, and decision-support tasks.
- **Research activation.** Narrowed further on Fable 5 (stronger tool triggering): the scaffold earns its place only where phrasing is ambiguous enough that the model might not search ("what should I think about X" vs. "what is the latest on X"), or where current grounding would help but the user hasn't signaled it. **Graceful degradation (v3, unchanged):** if the task needs current information but the runtime may lack search, do NOT instruct "search for X" — frame instead as "base your answer on training knowledge; explicitly flag any claim that would need current verification; do not fabricate specifics you cannot attest to." If you cannot tell whether search is available, default to the no-search framing: a prompt that assumes search in a search-less environment fails loudly; the reverse degrades gracefully. Whether search is *available* is a runtime condition — surface it in RUN SETTINGS, don't only bury it in the prompt.
- **Epistemic calibration.** Preconditions unchanged (v3): confidence tags (`[Confirmed]` / `[Inferred]` / `[Speculative]`) REQUIRE explicit source material in the prompt — without grounding they become hallucination licenses. When the task needs epistemic discipline but no sources are supplied, prefer: "state explicitly what you know with confidence vs. what you're inferring, and name what you would need to verify." For research-heavy prompts with supplied sources, require source-quality assessment and direct citation. Marginal value on Fable 5 is *guide-silent* — hold the preconditions, treat the residual gap as unknown.
- **Scope literalism.** Recast for Fable 5: a single brief, clear scope instruction now suffices where prior models needed enumeration. State scope explicitly only where it is genuinely non-obvious — session-wide norms in keystone prompts, cross-section application a literal reader could plausibly read narrowly. Step 5.5 handles format-directive cases operationally.
- **Pushback and challenge** (keystone and decision-support): permission to refuse a flawed framing; require clarifying questions before proceeding when ambiguity is high-stakes; for strategy prompts, require Claude to identify what would change its recommendation.
- **Working principles** (keystone): persistent session norms — how to handle uncertainty, when to ask vs. assume, what counts as "done"; define the relationship (collaborator, critic, executor, advisor); set escalation paths.

Apply techniques where the archetype and task warrant them — never mechanically. This is a calibration exercise, not a checklist.

### 5.5. Signal preservation pass

**Before restructuring, enumerate every signal the raw prompt carries.** Signals are compressed intent. Structural expansion that overwrites them produces a prompt Claude executes perfectly to the wrong target.

Scan the raw prompt for all of the following. Log each one found. Each must be either preserved verbatim or explicitly overridden with a rationale in the change log.

**a) Explicit format directives.** "Side by side", "in a table", "bullet points only", "one paragraph", "1,500 words", "in the style of X". Non-negotiable. If the user said "both analyses side by side," the optimized `<output_format>` must be side-by-side — not "Section A, then Section B, then a comparison."

**b) Unfilled placeholders.** `[CV]`, `[JD]`, `<DOCUMENT>`, `{INPUT}`, `[paste here]`, or any bracketed ALL-CAPS or clearly-templated token. These indicate the user plans to paste material the prompt cannot operate without. The optimized prompt MUST NOT wrap these as if material were present — doing so causes Claude to fabricate imagined content. Either (i) include an explicit conditional — "if placeholders are not filled, ask the user to provide the material before proceeding" — or (ii) make the first action a check that material is present.

**c) Ambiguity hints.** "I have a draft to share", "let me know what else you need", "I can send X if useful". These mark places where the user has NOT yet decided whether to include input or how the workflow runs. Do NOT convert them into locked workflows that block on the missing input. Preferred pattern: Claude produces its best immediate output AND offers to incorporate the additional material when received.

**d) Explicit prohibitions.** "Don't invent X", "work only with what I've given you", "no fabrication", "cite only real sources". Protective constraints the user is actively flagging — preserve verbatim or strengthen; never soften, never drop.

**e) Workflow hints.** "First pass", "quick and dirty", "I'll iterate", "final version", "this ships today". These calibrate how much Claude should invest. A "first pass" prompt should not get an exhaustive keystone treatment.

**f) Length/scope markers.** Word counts, slide counts, page budgets, "brief", "comprehensive". If the user specified a scope, that scope is the ceiling, not the floor.

**Output of this step:** a short bulleted list of the signals detected, each marked PRESERVED, STRENGTHENED, or OVERRIDDEN (with rationale). Carried into Steps 6 and 8.

### 6. Optimization (architecture-branched)

Rewrite the prompt applying structural techniques and the Step 5 capability layer. Use tools where they earn their place — never decoratively. Every structural addition must be compatible with the Step 5.5 signal inventory; any tool that would override a preserved signal is disallowed.

#### Branch A: SINGLE-TURN or SINGLE-TURN (CEILING)

Produce a single optimized prompt in a code block.

**Structural tools:**

- `<role>` — define who Claude embodies and what expertise it brings. Be specific about depth (not "analyst" but "senior analyst with 15 years in X who has internalized Y framework").
- **Intent-context (new in v1.0-fable)** — state the larger task, who it's for, and what the output enables: *"I'm working on [the larger task] for [who it's for]. They need [what the output enables]. With that in mind: [request]."* A documented Fable 5 lever — the model connects the task to relevant context instead of inferring intent. Highest value on agentic prompts, multi-workstream prompts, and keystones; skip when the intent is already unambiguous in one sentence — boilerplate motivation is noise.
- Concrete sub-instructions with defined outputs replacing vague imperatives.
- **Context-first ordering** — when the prompt contains significant reference material, place it BEFORE the task or question. Claude processes the context first and meets the instruction with grounding loaded; instruction-first ordering forces retroactive reinterpretation and measurably degrades long-context output. Irrelevant for short prompts with no reference material.
- `<constraints>` to prevent the prompt's most likely failure modes.
- **Motivated constraints** — where a constraint's boundary is non-obvious or misapplication is common, state WHY it exists; Claude applies motivated constraints more intelligently at the edges. Motivate only where the rationale changes edge-case handling.
- **Brevity/selectivity (new in v1.0-fable)** — counter Fable 5's un-steered elaboration at higher effort with the documented pattern: *"Lead with the outcome — the first sentence answers what happened or what you found; supporting detail after. Keep output short by being selective about what to include (drop details that don't change what the reader does next), not by compressing the writing into fragments, abbreviations, or arrow chains."* Apply when recommending high/xhigh effort, on agentic status summaries, and on keystone prompts governing long sessions; skip where the deliverable *is* the analysis and depth is the point.
- `<output_format>` if the response shape is unspecified. If the user DID specify a shape (Step 5.5 signal a), the format block echoes it — do not invent a different one.
- `<failure_modes>` describing what bad output looks like; `<evaluation_criteria>` defining what good output looks like.
- `<examples>` — when the user has a preference about *how* the output should look (voice, tone, structure, format), include 2–4 worked examples in `<examples>` / `<example>` tags. Examples beat description for steering format; load-bearing for the **Iterative refinement** archetype (consistency lock) and unresolved format preferences flagged in Step 5.5. **Fable 5 amendment [VALIDATE — n=1]:** for famous, well-documented registers (e.g., a Howard Marks memo), a precise register *description* may fully suffice on Fable 5 — reserve worked examples for idiosyncratic house voices and format preferences description can't resolve. Relatedly, locked structural prescription of content (fixed section lists) did not dilute voice on Fable 5 in testing, contra the prior-generation A/B finding — structure and voice can coexist. Examples must be relevant, diverse, and each wrapped in `<example>`.
- **Quote-grounding for long-document tasks** — when the prompt loads ≥3K tokens of source material to analyze (annual reports, prospectuses, due-diligence questionnaires (DDQs), transcripts), instruct Claude to first extract the relevant passages into `<quotes>` tags and ground subsequent reasoning in those quotes; materially reduces drift and hallucination on document-grounded analysis. Distinct from context-first ordering (position); quote-grounding forces *extraction before reasoning*. Skip for short reference material.
- Domain-specific tags where they add precision.
- `[bracketed placeholders]` for variable content — ONLY where the user has actual variable content to substitute. Do not invent placeholders the raw prompt did not carry.

**Capability tools:** as specified in Step 5, including the reasoning-echo guard on every installed verification scaffold.

For **SINGLE-TURN (CEILING)**, additionally include a one-paragraph strain note in the output (not in the prompt itself): which signal fired, what compression was applied, what the user gives up by staying single-turn.

#### Branch B: CHAIN

Produce a chain deliverable mirroring the validated Project-orchestrated workflow pattern:

1. **README / setup guide** — one-time setup + recurring usage flow + maintenance notes
2. **Project orchestrator** (if Project-orchestrated) — the custom-instructions block containing role, discipline, research protocol, methodological constraints, output conventions, AND the orchestration protocol handling variable collection, stage triggers, preconditions, checkpoints, overrides, and error states
3. **Stage prompts** — one file per stage: task definition, execution sequence, output format with handoff block, scope discipline. Variables referenced as `{VARIABLE_NAME}`, substituted at runtime by the orchestrator.

For flat-sequential chains, omit the orchestrator; the user pastes stage prompts sequentially in one chat.

**Production guidance:**

- Use `create_file` and `present_files` to deliver each file separately when code execution is available; otherwise clearly-labeled code blocks with filenames.
- Each stage file references only the variables it needs.
- Handoff blocks are structured, machine-readable, and substitution-ready for the next stage.
- The orchestrator collects variables at chat initialization — the user never re-declares them per stage.
- Stage prompts destined for Fable 5 runtimes get the same Step 5 calibration as single-turn prompts — including the reasoning-echo guard and, for agentic stages, the four agentic patterns.

Preserve the original's intent, voice, and core logic across all stages. Even at IN PURVIEW + CHAIN, do not pile on techniques that don't earn their place.

### 7. Feasibility pass (architecture-branched)

Run the checks below. Each can *reduce* the optimization, not just extend it.

#### Branch A: SINGLE-TURN / SINGLE-TURN (CEILING)

**a) Single-turn output budget — fidelity test (primary gate).**
For each substantive section or deliverable in the optimized `<output_format>`, ask: **would this section lose substance if the full work were delivered in one turn?** If no section would, single-turn is feasible regardless of section count. If one or more would, that is the CEILING band (name the compression applied) — or evidence Step 3 miscalled and the verdict should be revisited.

**Provisional numeric backstop [VALIDATE — calibrated on one Fable 5 exhibit plus documented longer default turns; tighten or relax only with evidence]:** flag strain beyond **~12 substantive sections or ~5 deep analytical asks**; signal 7 volume overflow anchors at **~10,000 words of dense work**. The prior-generation bands (">6 sections infeasible", ~3,000–5,000-word overflow) are retired — a 17-section verified manual with a full audit table cleared one Fable 5 turn comfortably in testing. **LOCKSTEP:** these constants are bound to `prompt-architect-fable`'s architecture module; any change propagates to both Fable skills. The Opus siblings retain the old constants by design.

**Long-form creative with a word-count ceiling** (e.g., a 1,500-word op-ed): do not add structural sections on top — Claude must spend its output budget on the piece, not on meta-sections about the piece.

**b) Context-dependency honesty.**
If the prompt depends on information Claude may not have and the runtime may not have search: replace "search for X" with the graceful-degradation framing (Step 5). If confidence tags were added, verify they have source material to operate on — if not, remove them. If the prompt names specific entities and expects current intelligence, instruct explicitly: "If you lack verified current information about [entity], say so. Do not generate plausible-sounding specifics."

**c) User-format preservation.**
Re-read the raw prompt once more. Verify every format directive (Step 5.5 signal a) is either reflected in the optimized `<output_format>` or explicitly overridden in the change log.

**d) Input-presence conditional.**
If Step 5.5 flagged unfilled placeholders, verify the optimized prompt contains the explicit conditional.

**Output (Branch A):** a 2–4 bullet feasibility verdict. If any check failed, modify the optimized prompt before the change log.

#### Branch B: CHAIN

**a) Per-stage feasibility.** Apply the Branch A fidelity test (and backstop) to each stage individually. If a stage breaches, split or compress it.

**b) Cross-chain feasibility.**
- **Delivery-turn check** — full one-turn chain delivery is the Fable 5 default (see Step 3 conditional delivery). Verify the complete deliverable set passes the fidelity test as one turn's output; if it genuinely wouldn't (very large chains), fall back to skeleton-then-files across two turns and say so.
- **Handoff contract integrity** — each stage's handoff block must contain every variable the next stage needs. No gaps; no assumptions of prior-turn context the next stage might not have.

**Output (Branch B):** a per-stage feasibility table plus a cross-chain verdict.

### 8. Change log

For each substantive change: what changed, why, and what failure mode it prevents or what quality dimension it improves. Map each change to a diagnostic dimension, a capability technique, a preserved signal (5.5), an architecture signal (3), or a feasibility correction (7). Specific, traceable changes only.

For chain deliverables, add a brief architectural change log: which stages exist, what each does, why this decomposition, where checkpoints sit.

### 9. Use case guidance

State when this prompt (or chain) is most effective, when it's not the right tool, and what it pairs with. For keystone prompts, state what the user should expect from the session it initiates.

For SINGLE-TURN deliverables: state what single-turn output to expect. For CHAIN deliverables: the operational flow (open chat, declare variables, trigger stages), expected output per stage, checkpoint locations, maintenance notes.

### 9.5 Run settings recommendation (surface-aware; effort + research)

Operational metadata that ships *with* the optimized prompt but never inside it — effort and search are runtime controls, not prompt content. Derived from state the workflow already computed (triage band + architecture verdict + diagnostic + SURFACE); no new analytical step. Anchor recommendations to level *semantics*; UI labels vary by client and evolve.

**EFFORT.** Fable 5 ladder: **low / medium / high / xhigh / max**, with **high the default**. Effort is the primary control trading intelligence against latency and cost — and on Fable 5 the calibration runs in **both directions**: lower effort often exceeds prior-generation xhigh and is right for routine work, while higher effort on routine tasks causes over-deliberation and unrequested scope expansion. Map it to the work:

- IN PURVIEW, single-turn → **high** (default), stepping to **xhigh** when the binding diagnostic constraints are the capability dimensions (reasoning depth, capability activation) rather than structural ones.
- SINGLE-TURN (CEILING) / multi-framework / heavy synthesis → **xhigh**.
- **max** — rare and deliberate: reserve for the genuinely hardest correctness-critical single prompts; flag diminishing returns and faster cap burn.
- Routine or fast-iteration work that still cleared triage → **medium** is a legitimate step-down; say so rather than defaulting up.
- CHAIN → per stage: gather/research stages **high**; synthesis, evaluation, and critique stages **xhigh**. State per-stage effort in the chain's run guidance.
- **Counter-instruction pairing:** when recommending high or xhigh on a scoped task, verify the prompt carries a scope-discipline or brevity/selectivity line (Step 6) so elevated effort deepens the work instead of expanding it. If neither is present and the task is scoped, add one.

Do not recommend a separate "thinking" control as the depth mechanism: thinking on Fable 5 is adaptive and depth is set through EFFORT. (Chat clients may expose thinking as a separate combinable toggle — harmless, but EFFORT is the recommendation that matters.)

**RESEARCH.** Whether to enable web search / Research mode before running:
- If the optimized prompt contains an in-prompt search instruction, RESEARCH is **on** — say so explicitly; an in-prompt "search for X" fails silently when the toggle is off. This is the failure mode the line exists to prevent.
- If the task warrants deep Research mode (multi-source synthesis, current-landscape scans), recommend it by name.
- If no external grounding is needed, "web search not needed" or omit.

**SURFACE gating.** Tailor the block to the declared (or default-assumed) surface:
- **chat** *(default — state the assumption if the user didn't declare)*: effort level + research line as above.
- **code**: effort on Claude Code governs overall thoroughness — how many files it reads, whether it runs tests, how far it pushes through multi-step work before checking in — not just thinking depth. Recommend the level with that semantic; for agentic prompts, note the expected checkpoint behavior (per the Step 2 four-pack) at the recommended level.
- **cowork / harness**: effort is set as a configuration/API parameter per call; recommend per-call levels (per-stage for chains), note asynchronous posture for long runs (check on runs rather than blocking), and — only when the prompt is genuinely agentic — verifier-subagent intervals and memory-file conventions per Step 2.

Keep the whole block to ≤2 lines in single-turn chat outputs; surface-specific additions only where the surface warrants them. It is a recommendation, not a configuration the skill controls.

---

## IN PURVIEW output formats

Selected by the Step 3 architecture verdict.

### Path C1 — SINGLE-TURN output format

Use for SINGLE-TURN or SINGLE-TURN (CEILING). Return exactly these sections:

**TRIAGE: IN PURVIEW** — [one-sentence rationale]

**SECTION 1 — INTENT & ARCHETYPE.** The intent statement (2–3 sentences) and the detected archetype with brief rationale.

**SECTION 2 — ARCHITECTURE DECISION.** The Step 3 verdict, signals fired (named explicitly), and — for CEILING — the strain note.

**SECTION 3 — DIAGNOSTIC.** The nine-dimension scoring as a clean table or structured list, deficiency notes for any dimension below 7, grouped structural vs. capability.

**SECTION 4 — SIGNAL INVENTORY.** The Step 5.5 signals, each marked PRESERVED, STRENGTHENED, or OVERRIDDEN (with rationale).

**SECTION 5 — OPTIMIZED PROMPT.** The full rewritten prompt in a code block. Copy-paste ready and self-contained.

**SECTION 6 — FEASIBILITY VERDICT.** The Step 7 Branch A checks and verdicts, fidelity test explicit.

**SECTION 7 — CHANGE LOG.** Concise, each entry tagged to the dimension, technique, signal, or correction it addresses.

**SECTION 8 — RUN SETTINGS.** Per Step 9.5, surface-aware, ≤2 lines on chat: effort level with a one-clause diagnostic-tied rationale; research line where applicable; surface assumption stated if defaulted.

**SECTION 9 — USE CASE GUIDANCE.** When to use, when not, what it pairs with, what single-turn output to expect; for keystones, what to expect from the session.

### Path C2 — CHAIN output format

Use for CHAIN verdicts. **Delivery is conditional (Step 3):**

- **Vehicle heuristic unambiguous → one-turn full delivery.** Produce ALL sections below in a single turn, with the heuristic named inline ("Project-orchestrated — signal 5 fires clearly; redirect if you disagree").
- **Vehicle heuristic marginal → two-turn delivery.** Turn 1: sections 1–4 (through the vehicle question), then stop and await the user's choice. Turn 2: sections 5–10.

**TRIAGE: IN PURVIEW** — [one-sentence rationale]

**SECTION 1 — INTENT & ARCHETYPE.**

**SECTION 2 — ARCHITECTURE DECISION.** Verdict (CHAIN), primary signals and amplifiers named, chain-shape rationale. Falsifiable.

**SECTION 3 — CHAIN SKELETON.** Number of stages; one-sentence purpose each; checkpoint locations and the decision each captures; handoff contracts.

**SECTION 4 — DELIVERY VEHICLE.** One-turn mode: the heuristic recommendation applied and named. Two-turn mode: "Recommended delivery: [Project-orchestrated | flat-sequential]. Confirm or switch?" — then stop.

**SECTION 5 — DIAGNOSTIC (per-stage + cross-chain).** The 9 dimensions per-stage where meaningful, plus handoff cleanliness, checkpoint design, variable persistence, orchestration robustness.

**SECTION 6 — SIGNAL INVENTORY.** Per Step 5.5.

**SECTION 7 — DELIVERABLES.** Each file as a separate `create_file` call (preferred when code execution is available) or a clearly-labeled code block: `README-setup.md`; `project-instructions.md` (Project-orchestrated only); `stage-1-[name].md`, `stage-2-[name].md`, …. After producing the files, call `present_files` with all of them.

**SECTION 8 — FEASIBILITY VERDICT.** Per-stage feasibility table + cross-chain verdict.

**SECTION 9 — CHANGE LOG.** Standard + architectural change log.

**SECTION 10 — RUN SETTINGS & USE CASE GUIDANCE.** Per-stage effort (gather high; synthesis/evaluation/critique xhigh), research per stage, surface-specific notes per Step 9.5; then the operational flow, expected output per stage, checkpoint locations, maintenance notes.

---

## Constraints

- Never skip Step 0 triage, Step 3 architecture decision, Step 5.5 signal preservation, or Step 7 feasibility — each guards a SEVERE failure mode below.
- Never add XML tags decoratively. Every tag must contain instructions the prompt would be worse without.
- Never strip personality, voice, or domain-specific language from the original. Optimize structure and capability activation, not character.
- If the original prompt is already strong (7+ across most dimensions, archetype correctly served), say so and make only surgical improvements. Do not rewrite for the sake of rewriting.
- The optimized prompt must be self-contained — paste it into Claude and get state-of-the-art output without this skill's context.
- Examples are expensive: apply the Step 6 `<examples>` calibration, including the Fable famous-register amendment. Never add examples decoratively.
- Capability techniques are calibrated to the task — anti-sycophancy on a recipe request is absurd; research activation on a creative-writing prompt is wrong. Never apply all techniques to every prompt.
- **Reasoning-echo guard:** never install instructions that tell Fable 5 to show, echo, transcribe, or explain its internal reasoning or thinking as response text — refusal risk (`reasoning_extraction`) with session reroute. Verification is framed as response content.
- **On Fable 5, prefer the brief clear instruction over the enumerated list.** If a behavior can be steered in one sentence, one sentence is the correct amount. Migrating prior-model instruction density into a Fable prompt is a defect, not diligence.
- RUN SETTINGS is operational metadata, never prompt content — never inside the optimized prompt code block. Present on IN PURVIEW (and a single optional web-search line on BORDERLINE); never on OUT OF PURVIEW.
- The effort recommendation derives from triage band, architecture verdict, diagnostic, and SURFACE — never reflexively maximal. Recommending max by reflex is the effort-equivalent of cargo-culting capability techniques. Depth is EFFORT's job, not a "thinking" instruction's.
- SURFACE comes only from explicit user declaration; when defaulted to chat, the assumption is stated. Agentic machinery (subagents, verifiers, memory files, autonomy language) never enters prompts for non-agentic tasks regardless of surface.

---

## Failure modes to avoid

### SEVERE failure modes (invalidate the optimization; treat as bugs, not style)

1. **TRIAGE BYPASS.** Running Steps 1–9.5 on any input without Step 0 having returned IN PURVIEW. The full treatment applied to simple prompts actively degrades downstream output.
2. **OVER-ENGINEERING SIMPLE PROMPTS.** Wrapping casual communication in XML scaffolding, placeholders, capability layers, or multi-section outputs. OUT OF PURVIEW gets the return-mostly-unchanged format — no exceptions.
3. **BAND-MIXING.** A BORDERLINE output with XML scaffolding, or an OUT OF PURVIEW output with a diagnostic. Each band has exactly one output format.
4. **SIGNAL OVERRIDE.** Structural expansion that overwrites signals the raw prompt actively carried. Four specific pathologies: wrapping unfilled placeholders as if material were present; converting ambiguity hints into locked workflows; applying confidence tags without source material; dropping or mutating explicit format directives.
5. **INFEASIBLE SINGLE-TURN SCOPE.** Specifying more output than the fidelity test supports without converting to chain — the failure the architecture decision prevents at the source.
6. **ARCHITECTURE MIS-SELECTION.** Three sub-pathologies: *silent single-turn default* (2+ primaries fired, no chain, no named justification); *chain over-engineering* (chain with 0 primaries — sophistication theater; on Fable 5 the chain bar is higher, making this failure easier to commit); *chain shape fabrication* (five stages where three suffice).
7. **HIDDEN STRAIN.** A SINGLE-TURN (CEILING) output that doesn't flag the strain. The middle band exists to make the silent ceiling visible.
8. **UNNAMED ARCHITECTURE VERDICT.** A verdict without the specific signals that fired or didn't. Unfalsifiable recommendations are not permitted.
9. **REASONING-ECHO INSTALLATION (new in v1.0-fable).** Shipping an optimized prompt that instructs Fable 5 to show/echo/transcribe/explain its internal reasoning as response text. This is not a style defect — it is a production refusal risk that can reroute the user's session mid-task.

### Common (soft) failure modes

Adding `<role>` to every prompt regardless of need; mechanically applying every capability technique; change-log entries like "added structure for clarity" without naming what and why; rewriting so heavily the author wouldn't recognize their intent; optimizations that would work identically on any LLM (no Claude-specific elicitation); treating the architecture decision as downstream of single-turn polish; and — the Fable-specific soft failure — **prior-model instruction density**: shipping enumerated rule lists where one clear sentence steers the behavior. Confusing "more structured" with "better," and "hygiene" with "ceiling": a perfectly structured prompt that doesn't activate capabilities is a 7/10, not a 10/10.

---

## Evaluation criteria

**For IN PURVIEW + SINGLE-TURN outputs**, a strong optimization will:
- Improve at least 3 of the 9 diagnostic dimensions by 2+ points each
- Preserve the original's intent and voice, and every Step 5.5 signal (or document the override)
- Apply at least one capability technique where the task warrants it (or state why none apply), with zero reasoning-echo phrasings
- Produce a change log where every entry traces to a specific finding, technique, signal, or correction
- Pass the fidelity-test feasibility pass honestly, naming any compression
- Name the architecture verdict explicitly; acknowledge strain on CEILING
- Include a calibrated, surface-aware RUN SETTINGS block — effort derived, not reflexive; counter-instruction pairing verified at high/xhigh

**For IN PURVIEW + CHAIN outputs**, a strong optimization will:
- Justify chain with 2+ named primaries (or 1 primary + 2+ amplifiers with explicit escalation note)
- Produce the smallest chain that does the job, with checkpoints only where judgment materially improves output
- Deliver in one turn when the vehicle heuristic is unambiguous (heuristic named), pausing only when it's marginal
- Define clean, substitution-ready handoff contracts and variable persistence
- For Project-orchestrated chains: an orchestrator handling initialization, triggers, preconditions, checkpoints, overrides, and error states without leaking implementation details
- Deliver as separate files (`create_file` + `present_files`) when code execution is available
- Specify per-stage, surface-aware run settings

**For BORDERLINE outputs:** close 1–3 specific gaps; stay within ~2x original length; zero XML and zero capability tags; a 2–4 bullet plain-language change log.

**For OUT OF PURVIEW outputs:** return the prompt substantially unchanged; one-sentence triage rationale; at most one surgical correction; no longer than 5 lines total.

The ultimate test for IN PURVIEW: if the user runs the optimized prompt (or chain) as the keystone of a new Fable 5 session, does the session produce output that surprises them with its depth, accuracy, and utility? That's state-of-the-art. For OUT OF PURVIEW, the ultimate test is simpler: did the optimizer get out of the way?
