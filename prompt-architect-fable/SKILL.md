---
name: prompt-architect-fable
description: Fable-variant prompt architect — classifies a task against a 10-bucket taxonomy with an execution-mode overlay (interactive vs. agentic), decides output architecture (single-turn vs. chained multi-stage), and produces a technique-grounded v1 draft prompt calibrated for Claude Fable 5, with surface-aware run settings (chat app, Claude Code, Cowork, harness). Verdicts are SINGLE-TURN, SINGLE-TURN (CEILING), or CHAIN (README + Project orchestrator + per-stage prompts). Fires ONLY on explicit Fable invocation — trigger phrases include "architect this for Fable 5", "help me prompt this for Fable", "draft a Fable 5 prompt for". Do NOT trigger on unqualified requests ("help me prompt this", "draft a prompt for") — those route to prompt-architect (Opus sibling). Do NOT trigger when a draft prompt already exists — that's prompt-optimizer-fable. Do NOT trigger for tasks covered by a dedicated domain skill — route to that skill instead.
---

# Prompt Architect — Fable 5 Edition

## CHANGELOG

**v1.0-fable — Jul 2026.** Standalone fork of `prompt-architect` v2.0 (calibrated Opus 4.7), recalibrated for **Claude Fable 5**. Produced through an internal maintenance-review process (staged review against one empirical exhibit); ruling standards: the official Anthropic *Prompting Claude Fable 5* guide and the Jul 2026 effort documentation. The Opus sibling remains in use, unchanged.

**Lockstep contract (governance).** The architecture decision module — seven signals, verdict logic, guards — plus its threshold constants is bound in lockstep with `prompt-optimizer-fable` and is inherited from it verbatim in this fork: any change there propagates to both Fable skills. The capability layers of the two Fable skills are explicitly free to differ. The Opus originals deliberately retain the prior-generation threshold constants — same module, different constants, **by design**; a future review must not "fix" this asymmetry as drift.

Changes vs. v2.0:
- **Technique library re-derived and extended:** #5 redefined as scoped reasoning within the prompt (extended-thinking budgets no longer exist as a control surface); #16 flagged surface-gated and unverified on Fable 5; **#19 intent-context** and **#20 brevity/selectivity** added; the agentic four-pack enters as the overlay's composite technique block.
- **Execution-mode overlay added:** a binary axis orthogonal to the 10 buckets — interactive (default) / agentic — that injects the Fable 5 agentic pattern set into any bucket's stack.
- **Architecture module recalibrated (LOCKSTEP, inherited):** fidelity test primary; provisional numeric backstop, VALIDATE-marked; prior-generation bands retired.
- **Surface parameter added** (chat / code / cowork / harness) gating run settings, #16, and overlay detection.
- **Run-settings element added** to both output formats (Fable effort ladder, high default, bidirectional; never inside the prompt).
- **New frontmatter description:** explicit-invocation-only triggering in the four-skill routing space; a stale private routing reference dropped; version string moved out of the description.
- **Claude-specific notes → Fable 5 notes,** per-claim re-derivation with evidence markers; prior-generation ceiling claim deleted; reasoning-echo guard added.
- **Worked examples rebuilt:** lithium and SWF examples recalibrated in place; third example swapped for an agentic-mode exemplar teaching the overlay.
- **Conditional chain delivery:** full one-turn delivery when the vehicle heuristic is unambiguous; confirmation pause only when marginal.
- **Prose-economy pass** with the discrimination rule: behavioral micro-instruction stated once; content specification (buckets, techniques, examples) preserved at density.

Provisional items carried with VALIDATE markers: the numeric feasibility backstop; the #7 famous-register amendment (n=1). Tighten or relax only with evidence.

---

## Purpose

Convert a described task into a technique-grounded v1 draft prompt for **Claude Fable 5** — or, when the task warrants it, into a technique-grounded *chain* of stage prompts plus a Project orchestrator. The skill does this by:

1. Classifying the task against the 10-bucket taxonomy, with the execution-mode overlay
2. Deciding the output architecture (single-turn vs. chain)
3. Applying the right technique stack from the Top-Tier Prompting for Professionals v2 framework, Fable-calibrated
4. Recommending surface-aware run settings for the draft

This skill is the *generative* counterpart to `prompt-optimizer-fable` (which is *evaluative*). Use the architect when you don't yet have a draft; use the optimizer when you do.

## Core philosophy

**Choose the architecture before choosing the structure.** Single-turn is the default delivery vehicle, but not the only one. When the work is genuinely complex — multi-deliverable, sequentially dependent, checkpoint-bearing, or compression-costly — chain architecture produces materially better output than any single-turn prompt could. The architect makes that call explicitly, names the signals, and produces a chain deliverable when chain is the right answer. Drafting a beautifully technique-grounded single-turn prompt for a task that deserved a chain is an alignment failure between the skill's mission (state-of-the-art output) and its mechanics (single-turn polish).

**On Fable 5, restraint is activation.** Fable 5's instruction-following is strong enough that one brief, clear instruction steers behavior that previously required enumeration — and instruction density inherited from prior-model habits can degrade its output. Every technique, rule, and scaffold in a v1 draft must clear a higher bar than on any prior model. The 5-technique cap is not a limitation on Fable 5; it is the point.

## When to run the architect vs. other skills

- **Architect first, then optimizer (full rigor path):** reserved for high-stakes prompts you will reuse or that anchor a critical decision. Architect produces v1; `prompt-optimizer-fable` audits and produces v2.
- **Architect alone:** most non-trivial tasks. The v1 draft is usually good enough to run directly.
- **Optimizer alone:** when a draft prompt already exists and needs auditing.
- **Dedicated skill instead of architect:** if the task maps to an existing dedicated domain skill, route to it — a dedicated skill already encodes the right technique stack for its domain. The architect is for tasks that don't fit a dedicated skill.

## Intake — surface declaration

`SURFACE ∈ {chat (default) | code | cowork | harness}` — where the drafted prompt will run.

Read only from an explicit user declaration ("this is for Claude Code", "Cowork task", "harness prompt"); never infer from topic. If absent, default to **chat** and state the assumption in the run-settings element. SURFACE gates the run-settings content, technique #16, and contributes to execution-mode detection below. It does not affect classification.

---

## The 10-bucket taxonomy

Every task is classified against these ten buckets. A task usually fits 1–3 buckets; multi-bucket tasks union their technique stacks (see Step 3).

### Bucket 1 — Fact-grounded document analysis
Source material exists and is pasted by the user. The task is to extract, summarize, cross-reference, or analyze the pasted content.
- **Default stack**: #15 (context loading + delimitation) + #17 (CoVe for fact-dense) + #8 (uncertainty calibration)
- **Situational adds**: #3 (output shaping), #18 (framework invocation)
- **Example tasks**: loan agreement review, 10-K extraction, prospectus summarization, contract diligence

### Bucket 2 — Research and investigation
Source material does NOT yet exist. The model must retrieve it (web search, tools) or generate it from trained knowledge. The task is both information-gathering AND synthesis.
- **Default stack**: #4 (Socratic — MANDATORY scoping gate) + #8 (uncertainty calibration) + #15 (delimitation for retrieved sources) + #17 (CoVe for fact-dense parts) + #3 (output shaping) + #1 (success criteria)
- **Situational adds**: #10 (contrast pairing when researching one thing vs another), #13 (devil's advocate for contested topics)
- **Example tasks**: aluminium market analysis, cosmetics market sizing, economic relationship investigation, country/sector deep-dives
- **CRITICAL**: The Socratic gate is non-negotiable. Research tasks are dangerously underspecified by default. Skipping #4 produces generic wikipedia-grade output.

### Bucket 3 — Knowledge document / teaching artifact creation
The output serves a FUTURE reader — often the author themselves, later — who doesn't have the context of the current conversation. The task is to build a durable reference, class, or note.
- **Default stack**: #1 (success criteria — future-reader variant) + #10 (contrast pairing) + #3 (pedagogical output shaping) + #7 (worked-example anchoring for recurring series) + #18 (framework invocation if teaching a named framework) + #8 (uncertainty calibration)
- **Situational adds**: #15 (if building from pasted source material), #17 (if fact-dense)
- **Example tasks**: course or lecture notes, a Black-Litterman lesson, a named-framework reference document
- **Future-reader success criteria variant**: "A top-tier answer will let a reader who has never seen this topic understand the load-bearing intuition in under 10 minutes and retain enough precision to apply it to a new case."

### Bucket 4 — High-stakes decision with ambiguity
Low reversibility, framing may be wrong, real consequences. The task is to decide (or to avoid deciding badly).
- **Default stack**: #4 (Socratic) + #11 (pre-mortem) + #6 (anti-sycophancy licensing), then #9 (adversarial review) on the output
- **Situational adds**: #13 (devil's advocate), #8 (uncertainty calibration)
- **Example tasks**: position sizing on a new thesis, job application decisions, structural portfolio changes, tactical allocation shifts

### Bucket 5 — Framework application to a specific case
A named framework exists (user's or external) and the task is to apply it to a specific input.
- **Default stack**: #18 (reference-anchored reasoning) + #15 (if source material is pasted) + #3 (structured output matching framework shape)
- **Situational adds**: #8 (uncertainty calibration), #14 (iterative refinement)
- **Example tasks**: applying a named performance-evaluation framework to a specific fund, a manager-assessment rubric to a specific manager, a structured scoring framework to a specific case

### Bucket 6 — Analytical synthesis / thesis work
Inputs already exist (in the user's own knowledge or in the conversation), need to be synthesized into a position, argument, or recommendation.
- **Default stack**: #1 (success criteria) + #5 (scoped reasoning) + #3 (output shaping)
- **Situational adds**: #6 (anti-sycophancy) + #9 (adversarial review) for defense-ready theses, #11 (pre-mortem), #13 (devil's advocate)
- **Example tasks**: "is sector X still attractive given Y", thesis defense memos, position reviews, cross-asset synthesis

### Bucket 7 — Recurring deliverable with house style
A format the user produces repeatedly with known conventions — memos, briefings, template fills.
- **Default stack**: #7 (worked-example anchoring) + #3 (output shaping) + #2 (contextual priming)
- **Situational adds**: #1 (success criteria), #14 (iterative refinement)
- **Example tasks**: briefing in a new domain replicating prior structure, a recurring course note in the established format

### Bucket 8 — Default well-scoped analytical work
Clear input, clear question, standard output. No special structural requirements.
- **Default stack**: #1 (success criteria) + #2 (contextual priming) + #3 (output shaping)
- **Situational adds**: whatever the specifics suggest
- **Example tasks**: "explain X", "compare A vs B", standard analytical questions

### Bucket 9 — Design / architecture work
Build a system, framework, workflow, or analytical infrastructure. Output is a structural solution to a multi-constraint problem.
- **Default stack**: #4 (Socratic — MANDATORY for constraint elicitation) + #11 (pre-mortem on the design) + #13 (devil's advocate between design choices) + #3 (structured output for the architecture) + #1 (success criteria)
- **Situational adds**: #18 (if building on an existing framework)
- **Example tasks**: a strategic-asset-allocation engine, new framework design, skill design, workflow infrastructure
- **Architecture note**: Bucket 9 tasks typically fire signals 2 (sequential dependency) and 3 (checkpoint value) and often warrant CHAIN. Run Step 2 normally; if CHAIN fires, deliver the chain.

### Bucket 10 — Persuasive writing / external communication
The output must move a specific reader toward a specific action or belief. Success is reader-centric, not analytical.
- **Default stack**: #2 (contextual priming — about the READER, not the author) + #7 (worked-example anchoring, per its Fable amendment) + #3 (output shaping to persuasive conventions) + #1 (success criteria — "what would make the reader say yes") + #14 (iterative refinement)
- **Situational adds**: #9 (adversarial review — "read this as the skeptical recipient")
- **Example tasks**: cover letters, client pitches, external memos, LinkedIn posts, investor communications
- **Reader-centric success criteria variant**: "A top-tier answer will make [specific reader] [specific action] within [timeframe]."
- **NOTE**: If a dedicated skill covers a specific external-communication type, route to it instead.

---

## The execution-mode overlay (new in v1.0-fable)

A binary axis **orthogonal to the buckets** — the bucket is the cognitive nature of the work; the mode is how the run executes:

- **Interactive (default)** — the user is present; the prompt initiates a normal conversational turn or short exchange.
- **Agentic** — the prompt initiates autonomous or semi-autonomous multi-step work: tool use, long runs, work continuing without the user watching.

**Detection rule:** explicit user statement beats surface lean. "Run it end to end", "I'll be away", "unattended", "overnight", "pipeline" → agentic. Surface lean: harness and Cowork lean agentic; chat leans interactive; **Claude Code is genuinely both** — a quick interactive fix or a multi-hour autonomous run. If the mode is unstated and the choice is consequential, ask (one line) rather than assume.

**Effect:** when agentic mode is active, the **Agentic execution block** (a composite technique — see the library, block AX) is injected into the unioned stack of *whatever bucket(s)* apply, at mandatory-for-the-task priority. It occupies one slot in the 5-cap; the union-and-cap discipline is unchanged. Agentic research is still Bucket 2; agentic framework application is still Bucket 5 — the overlay changes the technique stack, never the classification.

Never inject agentic machinery into interactive-mode drafts, regardless of surface.

---

## Workflow

### Step 1 — Classify (10-bucket + mode)

Identify which buckets apply. Most non-trivial tasks fit 1–3 buckets; do not force one bucket if multiple apply. Detect the execution mode per the overlay's detection rule. If classification is genuinely ambiguous between buckets whose stacks differ materially, run **clarification mode** (below) before Step 2.

State the bucket(s), the mode, and the considered-and-rejected alternatives explicitly. This is the anti-sycophancy guard — it forces the skill to defend its classification.

### Step 2 — Architecture decision (LOCKSTEP module — identical to prompt-optimizer-fable)

This step decides the *output architecture* for the v1 draft: single-turn, single-turn (ceiling) (strained but fits), or chain (sequential stage prompts with handoffs and optional human-in-loop checkpoints). The decision drives downstream branching of Steps 3–4.

**Default:** single-turn. Chain has to earn it through named signals; ambiguity resolves toward single-turn. On Fable 5 the bar for chain is *higher* than on prior models — longer default turns mean genuinely large work fits in one turn.

The seven signals are evaluated from the user's task description (no prompt exists yet — that's the architect's whole point).

#### Trigger signals

**Primary signals:**

1. **Multi-deliverable** — the task requires two or more distinct artifacts with materially different shapes (e.g., data sheet + analytical memo + scenario matrix). Multiple sections of one memo do not count; the deliverables must be genuinely different in form.
2. **Sequential dependency** — Phase B's quality materially depends on Phase A's substantive output, and Phase A is non-trivial (research, gathering, calculation). Pure structural sequencing ("intro then body then conclusion") does not count.
3. **Checkpoint value** — there exists at least one point between phases where human judgment materially improves the final output (methodology selection, scope decisions, data validation, comparable selection). A ceremonial "continue" does not fire this signal. Note: an agentic-mode run deliberately designed to proceed unattended weighs *against* this signal.
4. **Compression cost** — fitting the full work into one turn would force compression of sections that lose fidelity. Test with the fidelity question (below), not by section-counting.

**Amplifying signals:**

5. **Reusability premium** — the prompt (once drafted) will be re-run many times across different inputs (>5 expected runs).
6. **Cognitive mode switching** — the work requires distinct cognitive modes that benefit from clean separation (research vs. synthesis vs. creative output vs. critique).
7. **Volume overflow** — output genuinely exceeds the single-turn budget even with disciplined compression. Fable-calibrated anchor: **~10,000 words of dense work** [VALIDATE — provisional; see the backstop below].

**Fidelity test (primary gate for signals 4 and 7):** for each substantive section or deliverable the task implies, ask — would it lose substance if the full work were delivered in one turn? If no section would, single-turn is feasible regardless of count. **Provisional numeric backstop [VALIDATE — calibrated on one Fable 5 exhibit plus documented longer default turns]:** flag strain beyond ~12 substantive sections or ~5 deep analytical asks. The prior-generation bands (~3,000–5,000-word overflow) are retired. **LOCKSTEP:** these constants are bound to `prompt-optimizer-fable`; any change propagates to both Fable skills. The Opus siblings retain the old constants by design.

#### Verdict logic

- **0 primary signals → SINGLE-TURN.** Default; amplifiers alone are irrelevant.
- **1 primary signal → SINGLE-TURN (CEILING).** Proceed single-turn but flag the specific strain visibly.
- **2+ primary signals → CHAIN.** Name the signals fired.

**Amplifier escalation rule:** exactly 1 primary + 2+ amplifiers may escalate to CHAIN — noted explicitly ("escalated from SINGLE-TURN (CEILING) on amplifier weight").

**Reusability-only exception:** signal 5 alone never chains. Recommend saving the prompt to the library instead.

#### Sub-decision when CHAIN: Project-orchestrated vs. flat-sequential

- **Project-orchestrated** — one-time setup of Project custom instructions + knowledge-base stage files; runs trigger stages with short commands. Best for high-reuse workflows.
- **Flat-sequential** — standalone stage prompts pasted sequentially in one chat. Best for one-shot complex tasks.

**Heuristic:** signal 5 fires → Project-orchestrated; otherwise flat-sequential.

**Conditional delivery (v1.0-fable).** When the heuristic is **unambiguous**, deliver the **full chain in one turn** — verdict, skeleton, and all files — naming the heuristic applied inline; the user redirects afterward if they disagree. Pause for confirmation **only when signal 5 is marginal** (genuine ambiguity about reuse): deliver the verdict and skeleton, ask, and produce the files next turn. Fable 5's output budget removed the old two-turn necessity; the checkpoint survives exactly where it carries decision content.

#### Guards

- **Single-turn remains the default.** Chain must earn it through named signals; ambiguity resolves to single-turn.
- **Smallest chain that does the job.** Every stage must justify its existence against its friction cost.
- **Name the signals.** An unnamed verdict is unfalsifiable.
- **Respect friction costs.** Recommend the lightest architecture that fits the work.
- **Reusability alone does not chain.**

#### Output of this step

> **ARCHITECTURE: [SINGLE-TURN | SINGLE-TURN (CEILING) | CHAIN]**
>
> Primary signals fired: [list]. Amplifiers: [list, or "none"].
>
> [SINGLE-TURN: one-line confirmation. CEILING: the specific strain and what the user gives up. CHAIN: stage count, one-line purpose each, checkpoint locations, vehicle heuristic — and whether delivery is one-turn (heuristic unambiguous, named) or paused for vehicle confirmation (marginal).]

Carry the verdict into Step 3: SINGLE-TURN bands follow the standard union-and-cap flow; CHAIN runs technique selection *per stage*.

### Step 3 — Technique selection (architecture-aware)

#### Branch A: SINGLE-TURN / SINGLE-TURN (CEILING)

1. **Union the stacks** of all applicable buckets from Step 1; deduplicate. If agentic mode is active, inject block AX at mandatory-for-the-task priority.
2. **Apply the 5-technique cap.** Trim using this priority order:
   - **Mandatory-for-the-task** first (#18 if a framework exists, #15 if documents pasted, #4 if research or design, block AX if agentic mode)
   - **Mandatory-for-the-stakes** second (#4, #11 for high-stakes; #17 for catastrophic-on-error)
   - **Structural-for-the-output** third (#3, #1, #19 where intent is non-obvious)
   - **Situational** fourth (#6, #8, #9, #14, #20, etc.)
3. **List the rest** as "Available on request" — not in the draft, but visible to the user.

For SINGLE-TURN (CEILING), prepare a one-paragraph **strain note**: which signal fired, what compression the draft reflects, what the user gives up by staying single-turn.

#### Branch B: CHAIN

Per-stage technique selection. Each stage is itself a focused prompt — bucket-classified, technique-grounded, scope-disciplined.

1. **Decompose into stages.** Smallest chain that does the job; typical chains are 3–5 stages, named for the work they do.
2. **Per-stage bucket classification.** Each stage maps to one or more buckets (a research-then-synthesize chain: Stage 1 in Bucket 2, Stage 2 in Bucket 6). The mode overlay applies per stage — a chain can mix interactive and agentic stages.
3. **Per-stage technique stack (5-cap per stage).** Standard union-and-cap within each stage. Cross-chain coordination (#12) lives at the orchestrator level, not inside stages.
4. **Handoff contracts.** Each stage's output specifies a structured handoff block — variables, intermediate artifacts, methodology decisions — the next stage substitutes cleanly. No re-prompting the user for the same variable mid-flow.
5. **Checkpoint placement.** Human-in-loop checkpoints only where judgment materially improves output. No ceremonial checkpoints.

### Step 4 — Draft the deliverable (architecture-branched)

**Fable drafting rules (both branches).** Drafts steer with the brief clear instruction, not the enumerated list — if one sentence steers the behavior, one sentence is the correct amount. Never include instructions telling Fable 5 to show, echo, transcribe, or explain its internal reasoning or thinking as response text (`reasoning_extraction` refusal risk with session reroute); all verification and self-critique (#9, #17) is framed as *response content* — "state what you verified and the outcome," never "show your reasoning." Depth is never requested in the prompt — it lives in the run-settings effort recommendation.

#### Branch A: SINGLE-TURN / SINGLE-TURN (CEILING)

Produce a single v1 prompt in a code block, structured with the selected techniques' conventions (success criteria blocks, delimiters, Socratic instructions, intent-context opening where #19 was selected). Label it "v1 — technique-grounded draft." For CEILING, include the strain note *outside* the prompt.

#### Branch B: CHAIN

Produce a chain deliverable mirroring the validated Project-orchestrated pattern:

1. **README / setup guide** (`README-setup.md`) — one-time setup + recurring usage flow + maintenance notes.
2. **Project orchestrator** (`project-instructions.md`, if Project-orchestrated) — role, discipline, research protocol, methodological constraints, output conventions, AND the orchestration protocol handling variable collection, stage triggers, preconditions, checkpoints, overrides, and error states.
3. **Stage prompts** (`stage-1-[name].md`, …) — task definition, execution sequence, output format with handoff block, scope discipline. Variables as `{VARIABLE_NAME}`, substituted by the orchestrator.

For flat-sequential chains, omit the orchestrator. Production guidance: deliver each file via `create_file` + `present_files` when code execution is available, else labeled code blocks; each stage references only the variables it needs; handoff blocks are structured and substitution-ready; the orchestrator collects variables at initialization. Stage prompts destined for Fable 5 runtimes carry the Fable drafting rules above, including block AX for agentic stages.

Preserve the original task's intent and voice across all stages. Do not pile on techniques that don't earn their place.

### Step 5 — Run settings recommendation (surface-aware)

Operational metadata that ships *with* the draft but never inside it — effort and search are runtime controls, not prompt content. Anchor to level *semantics*; UI labels vary by client and evolve.

**EFFORT.** Fable 5 ladder: **low / medium / high / xhigh / max**, **high the default**. Calibration runs in both directions: lower effort often exceeds prior-generation xhigh and is right for routine work; higher effort on routine tasks causes over-deliberation and unrequested scope expansion. Recommend: **high** for standard IN-scope drafts; **xhigh** for heavy synthesis, multi-framework work, CEILING drafts, and long agentic runs; **max** rarely — the hardest correctness-critical prompts, flagged as diminishing returns; **medium** as a legitimate step-down for routine-but-real work. Chains: per stage — gather/research **high**; synthesis, evaluation, critique **xhigh**. When recommending high/xhigh on a scoped task, verify the draft carries a scope-discipline or #20 line so elevated effort deepens rather than expands the work.

**RESEARCH.** If the draft contains an in-prompt search instruction, say "run with web search on" — it fails silently otherwise. Recommend Research mode by name for multi-source synthesis. Omit when no external grounding is needed.

**SURFACE gating.** chat *(default — state the assumption if undeclared)*: effort + research lines. **code**: effort governs overall thoroughness — file reads, test runs, how far it pushes before checking in — not just thinking depth; note expected checkpoint behavior for agentic drafts. **cowork / harness**: effort as a configuration/API parameter per call (per stage for chains); asynchronous posture for long runs; verifier-subagent intervals and memory-file conventions only when block AX is in the draft.

Keep to ≤2 lines on chat single-turn outputs; surface-specific additions only where warranted.

---

## "I'm not sure which bucket" — clarification mode

If you cannot confidently identify at least one bucket (or you're torn between buckets whose technique stacks differ materially), DO NOT guess. Run a micro-Socratic clarification step:

- Return 2–4 targeted questions that would resolve the classification ambiguity, each mapping to a specific bucket distinction (e.g., "Is the source material in hand, or does it need to be gathered?" distinguishes Bucket 1 from Bucket 2 — and "will this run attended or unattended?" resolves the mode overlay).
- Rank the questions by how much they'd shift the classification.
- Do NOT draft the v1 prompt or run Step 2 until the user answers — architecture decisions depend on bucket clarity.
- This is the skill eating its own dogfood: technique #4 applied to its own ambiguity.

Only enter clarification mode when genuinely needed. If the task is clear, proceed to Step 2.

---

## Output formats

### Format A — SINGLE-TURN / SINGLE-TURN (CEILING)

The skill ALWAYS produces output in this exact six-part structure:

#### (a) Task classification
- Bucket(s) identified, one-line justification each; execution mode with the detection basis
- Considered-and-rejected buckets, with rationale (the anti-sycophancy guard)

#### (b) Architecture decision
- The Step 2 verdict, signals fired (named explicitly), and — for CEILING — the strain note

#### (c) Technique selection
- The 5 techniques (or fewer) selected, each with a one-line justification (block AX listed as one slot when active)
- Techniques cut by the cap, listed under "Available on request"

#### (d) v1 draft prompt
- The actual prompt, ready to copy and use, labeled "v1 — technique-grounded draft"

#### (e) Run settings
- Per Step 5, surface-aware, ≤2 lines on chat: effort level with a one-clause rationale; research line where applicable; surface assumption stated if defaulted

#### (f) Handoff note
One line telling the user what to do next:
- "Run directly" — the draft is ready
- "Optionally run prompt-optimizer-fable for final audit" — good draft; the optimizer adds rigor for high-stakes cases
- "Recommended: run prompt-optimizer-fable before executing" — high-stakes or reusable enough to justify the extra turn
- "Strain noted — consider escalating to CHAIN if the compression matters" — for CEILING

### Format B — CHAIN (conditional delivery)

**Delivery is conditional (Step 2):** vehicle heuristic unambiguous → **one-turn full delivery** of ALL sections, heuristic named inline ("Project-orchestrated — signal 5 fires clearly; redirect if you disagree"). Heuristic marginal → **two-turn delivery**: sections 1–4, stop for the vehicle answer, then sections 5–8.

**SECTION 1 — TASK CLASSIFICATION.** Buckets + mode per Step 1, one-line justifications, considered-and-rejected alternatives.

**SECTION 2 — ARCHITECTURE DECISION.** Verdict (CHAIN), primary signals and amplifiers named, chain-shape rationale. Falsifiable.

**SECTION 3 — CHAIN SKELETON.** Number of stages; one-sentence purpose each; per-stage bucket (and mode) preview; checkpoint locations and the decision each captures; handoff contracts.

**SECTION 4 — DELIVERY VEHICLE.** One-turn mode: the heuristic applied and named. Two-turn mode: "Recommended delivery: [Project-orchestrated | flat-sequential]. Confirm or switch?" — then stop.

**SECTION 5 — PER-STAGE TECHNIQUE STACKS.** For each stage: bucket + mode, the ≤5 techniques selected, "Available on request" leftovers.

**SECTION 6 — DELIVERABLES.** Each file via `create_file` (preferred when code execution is available) or labeled code block: `README-setup.md`; `project-instructions.md` (Project-orchestrated only); `stage-1-[name].md`, …. After producing the files, call `present_files` with all of them.

**SECTION 7 — RUN SETTINGS.** Per-stage effort and research per Step 5, surface-aware.

**SECTION 8 — HANDOFF NOTE.** Operational flow, expected output per stage, checkpoint locations, maintenance notes; whether running `prompt-optimizer-fable` on the orchestrator/stages is recommended for high-stakes use.

---

## The technique library (reference)

Full descriptions live in the Top-Tier Prompting for Professionals v2 document; Fable-specific calibrations are marked. Short reference:

1. **Task framing with explicit success criteria** — state the task, what top-decile looks like, what would make it fail
2. **Contextual priming** — who you are, what you're doing, what you've already decided
3. **Structured output shaping** — specify the shape (table columns, section names) before the task
4. **Socratic prompting** — ask the model to ask you the 5–8 questions that would most change its answer
5. **Scoped reasoning** *(redefined for Fable 5)* — scope the reasoning *within the prompt*: which alternatives to weigh, what to verify, where the analytical weight belongs. Reasoning depth itself is not a prompt instruction on Fable 5 — thinking is adaptive and depth lives in the effort setting (Step 5). Never draft "think harder/longer" language, and never draft reasoning-reproduction language (see the Fable drafting rules).
6. **Anti-sycophancy licensing** — grant specific permission to disagree, refuse, or propose alternatives
7. **Worked-example anchoring** — one high-quality example of the target output. **Fable amendment [VALIDATE — n=1]:** for famous, well-documented registers (e.g., a Howard Marks memo), a precise register *description* may fully suffice on Fable 5 — reserve examples for idiosyncratic house voices and format preferences description can't resolve; locked structural prescription did not dilute voice on Fable 5 in testing, contra the prior-generation finding.
8. **Explicit uncertainty calibration** — tag claims High/Medium/Low confidence inline (requires grounding to operate on)
9. **Adversarial review as a second pass** — critique in a fresh turn, not the generation turn
10. **Contrast pairing** — "explain X vs Y assuming I know Y"
11. **Pre-mortem framing** — "assume this failed in 18 months — walk back the causal chain"
12. **Decomposition with explicit handoffs** — break into steps, name what passes between them (orchestrator-level for chains)
13. **Devil's advocate dialectic** — stage a short exchange between two partisan positions
14. **Iterative refinement with explicit dissatisfaction** — flaw + target + preserve
15. **Deliberate context loading + multi-chunk delimitation** — paste source, delimit chunks explicitly, instruct "answer only from the material above"
16. **Response prefilling** *(surface-gated)* — API-style surfaces only, never chat; support on Fable 5 unverified [VALIDATE] — prefer #3 unless the surface is confirmed and the need is real
17. **Chain of Verification (CoVe)** — generate → extract claims → verify each → revise; framed as response content on Fable 5, never as reasoning display
18. **Reference-anchored reasoning** — invoke named frameworks explicitly
19. **Intent-context** *(new in v1.0-fable)* — state the larger task, who it's for, and what the output enables: "I'm working on [the larger task] for [who it's for]; they need [what the output enables]; with that in mind: [request]." A documented Fable 5 lever — the model connects the task to relevant context instead of inferring intent. Highest value on agentic, multi-workstream, and keystone drafts; skip when intent is unambiguous in one sentence — boilerplate motivation is noise.
20. **Brevity/selectivity** *(new in v1.0-fable)* — counter Fable 5's un-steered elaboration at higher effort: "Lead with the outcome — the first sentence answers what happened or what you found; supporting detail after. Keep output short by being selective about what to include, not by compressing the writing into fragments or arrow chains." Apply on high/xhigh-effort drafts for scoped tasks, agentic status summaries, and keystones; skip where the deliverable *is* the analysis and depth is the point.

**Block AX — Agentic execution block** *(the overlay's composite technique; one slot in the 5-cap)*. Installed as brief instructions, not enumerations:
- *Checkpoint discipline* — "Pause only when the work genuinely requires me: a destructive or irreversible action, a real scope change, or input only I can provide; otherwise keep going. If you hit one, ask and end the turn rather than ending on a promise."
- *Evidence-grounded progress claims* — "Before reporting progress, audit each claim against a tool result from this session; report only what you can point to, and say explicitly what is not yet verified." (Documented to near-eliminate fabricated status reports.)
- *Boundaries* — "When I'm describing a problem or thinking out loud, the deliverable is your assessment — report findings and stop; don't apply fixes until I ask."
- *Anti-overplanning* — "When you have enough information to act, act. Don't re-derive established facts or survey options you won't pursue."
- *Surface extras (code / cowork / harness only, where the run warrants):* delegation guidance for parallel subagents; a fresh-context **verifier subagent** at defined intervals (outperforms self-critique on long runs); a **memory/notes file** for lessons across runs (one lesson per entry, why it mattered, no duplicates).

---

## Worked example — Research task (Bucket 2, interactive) [SINGLE-TURN verdict]

**User input**: "I want to research the global market for lithium — I might write something up for my investment notes."

**Skill output**:

### (a) Task classification
- **Bucket 2 (Research and investigation)** — primary. No source material in hand; output requires retrieval and synthesis.
- **Bucket 3 (Knowledge document)** — secondary. "Investment notes" signals a future-reader artifact.
- **Mode: interactive** — conversational research turn; nothing indicates an unattended run.
- **Considered and rejected**: Bucket 6 (synthesis) — synthesis assumes inputs exist; here they must be gathered. Bucket 8 (default) — research without Socratic scoping produces generic output.

### (b) Architecture decision

**ARCHITECTURE: SINGLE-TURN.** Primary signals fired: none. Amplifiers: 5 (mild — future-reader artifact, but one-off not a template).

Research-and-write-up fits one turn when scope is bounded — which is what Socratic scoping does. No multi-deliverable need, no dependency the user must gate (the Socratic clarification *is* the gate), no compression cost at research-note length under the fidelity test.

### (c) Technique selection
1. **#4 Socratic** — mandatory for Bucket 2. "Research lithium" is dangerously underspecified.
2. **#15 Context loading with delimitation** — for retrieved sources; ground non-obvious claims in cited passages.
3. **#8 Uncertainty calibration** — research mixes facts and inferences; grounding exists (retrieved sources), so tagging is licensed.
4. **#3 Output shaping** — pedagogical structure (Bucket 3 influence).
5. **#1 Success criteria** — future-reader variant.

*Available on request*: #17 (CoVe on fact-dense sections), #10 (contrast pairing vs. another battery metal), #13 (devil's advocate on contested forecasts), #19 (intent-context — cut by the cap: the draft's opening line already carries the intent unambiguously).

### (d) v1 draft prompt — technique-grounded draft

> I'm building a research note on the global lithium market for my investment notes — a durable reference for future me, upstream of any trade decision.
>
> **Before you research or draft anything**, ask me the 5–7 questions whose answers would most change the shape of the output, ranked by how much they'd shift it. Interrogate at least: the angle (supply-side vs demand-side vs price mechanics vs geopolitical exposure vs investable thesis), the time horizon, the decision this note supports (if any), and the technical depth I want. Don't start researching until I've answered.
>
> Once I've answered:
>
> **Success criteria.** A top-tier note lets a reader (future me) who hasn't thought about lithium in 6 months reconstruct the load-bearing intuition in under 10 minutes with enough precision to act on it. A failing note is a generic sector overview I could have gotten from Wikipedia.
>
> **Research discipline.** Use web search. Wrap each retrieved source as `<source index="n">` with a brief identifier; cite the index inline for every non-obvious claim. Make no claims you cannot ground in a retrieved source or in widely-established structural facts about the industry.
>
> **Confidence tagging.** Tag non-trivial claims inline as [High], [Medium — worth verifying], or [Low — inference I'd want to check]. I specifically want to see where you're extrapolating vs. citing.
>
> **Output structure.** Sections: (1) one-paragraph orientation — what this market is and why it matters to an allocator; (2) supply side; (3) demand side; (4) price mechanics; (5) load-bearing uncertainties — the 3–4 things that would most change the investable view; (6) what would have to be true for this to matter to my book.
>
> Every sentence carries a fact, a mechanism, or a load-bearing uncertainty. No executive summary restating the note.

### (e) Run settings
*Effort: high (Fable default — retrieval-plus-synthesis at note scale, structural constraints binding, no need to escalate). Web search: on — the draft contains an in-prompt search instruction and fails silently without it. Surface: chat (assumed — none declared).*

### (f) Handoff note
*Optionally run prompt-optimizer-fable for final audit — this is a reusable research template and the rigor compounds across future notes.*

---

## Worked example — Design/architecture task (Bucket 9) [CHAIN verdict, one-turn delivery]

**User input**: "I want to design a systematic framework for evaluating sovereign wealth fund annual reports to extract their allocation shifts and strategic signaling. I'll need to apply it to ~15 funds over the next quarter."

**Skill output** (delivery is **one-turn**: the vehicle heuristic is unambiguous — signal 5 fires clearly at 15 planned runs — so all sections, files included, land in this single response):

### SECTION 1 — Task classification
- **Bucket 9 (Design / architecture work)** — primary. Build a framework, not apply one. Multi-constraint: repeatability, signal extraction, comparability.
- **Bucket 5 (Framework application)** — secondary. The downstream work applies the framework to specific SWFs.
- **Mode: interactive** — attended, checkpoint-bearing work; nothing indicates unattended runs.
- **Considered and rejected**: Bucket 3 — the framework will eventually live as a knowledge artifact, but the immediate task is design-then-application, not pedagogy.

### SECTION 2 — Architecture decision

**ARCHITECTURE: CHAIN.** Primary signals fired: 1 (multi-deliverable: framework design + per-fund application output), 2 (sequential dependency: cannot apply what hasn't been designed), 3 (checkpoint value: validate the framework before spending 15 fund-applications on it). Amplifiers: 5 (reusability — 15 expected runs), 6 (mode switching: design vs application).

### SECTION 3 — Chain skeleton

3-stage chain.
- **Stage 1: Framework design** (Bucket 9). Socratic constraint elicitation → pre-mortem → devil's advocate on tradeoffs → structured framework architecture. Handoff: the framework specification.
- **Stage 2: Framework application** (Bucket 5). Apply Stage 1's framework to one SWF annual report per run. Handoff: structured extraction per fund.
- **Stage 3: Cross-fund synthesis** (Bucket 6). After all funds, synthesize allocation-shift and signaling patterns across the cohort. Handoff: comparative memo.

Checkpoint after Stage 1: methodology validation — the wrong framework wastes 15 applications. No checkpoint between Stages 2 and 3.

### SECTION 4 — Delivery vehicle

**Project-orchestrated — applied, not asked:** signal 5 fires unambiguously (15 planned runs justify the one-time setup; the orchestrator handles `{FUND_NAME}` / `{ANNUAL_REPORT}` substitution, the methodology checkpoint, and stage triggers). Redirect to flat-sequential if you disagree.

### SECTIONS 5–8 (produced in this same turn)

Per-stage technique stacks (Stage 1: #4, #11, #13, #3, #1; Stage 2: #18, #15, #3, #8; Stage 3: #1, #5, #3, #13); the deliverable files (`README-setup.md`, `project-instructions.md`, `stage-1-framework-design.md`, `stage-2-framework-application.md`, `stage-3-cross-fund-synthesis.md`) via `create_file` + `present_files`; run settings (Stage 1 **high**, web search off; Stage 2 **high**, search off — material is pasted; Stage 3 **xhigh** — cross-cohort synthesis is the binding analytical load; surface: chat assumed); and the handoff note (run `prompt-optimizer-fable` on the orchestrator before first live use — 15 runs amortize the audit).

---

## Worked example — Agentic execution under the overlay (Bucket 5 + agentic mode) [SINGLE-TURN verdict]

**User input**: "In Claude Code: migrate all the event logs in my data-pipeline repo to the new schema and verify nothing breaks. I'll be away — run it end to end."

**Skill output**:

### (a) Task classification
- **Bucket 5 (Framework application)** — primary. The new schema is the framework; the task applies it across existing inputs.
- **Mode: agentic** — explicit statement ("run it end to end", "I'll be away") plus code surface. Detection basis: statement, confirmed by surface.
- **Considered and rejected**: Bucket 9 (design) — the schema already exists; nothing is being designed. Bucket 8 — the unattended multi-step execution is the defining feature, not the analytical question.

### (b) Architecture decision

**ARCHITECTURE: SINGLE-TURN.** Primary signals fired: none — one deliverable (the migrated, verified repo plus a report), no human checkpoint by explicit design (signal 3 weighs *against* firing on an unattended run), no compression cost under the fidelity test. Amplifiers: none. A long *run* is not a chain — the chain question is about the prompt architecture, not the runtime duration.

### (c) Technique selection
1. **Block AX (agentic execution)** — mandatory-for-the-task: the run is unattended.
2. **#1 Success criteria** — defines "done and verified" so the run has a checkable finish line.
3. **#19 Intent-context** — the migration's purpose shapes edge-case handling the user won't be present to adjudicate.
4. **#3 Output shaping** — the final report's shape, so the first message back is readable re-grounding, not a working thread.
5. **#20 Brevity/selectivity** — the final summary must lead with the outcome; xhigh effort will otherwise over-narrate.

*Available on request*: #17 (CoVe — partially subsumed by AX's grounded-progress line), #8 (uncertainty calibration).

### (d) v1 draft prompt — technique-grounded draft

> I'm migrating my data-pipeline repo to the new schema so future runs write to one consistent store — the old logs must survive intact because downstream jobs and dashboards depend on them. With that in mind: migrate all event logs under `/logs` to the new schema in `schema/events_v2.json`, then verify nothing breaks.
>
> **Done means:** every log parses under the new schema; the existing test suite runs green; no data loss (row counts and key fields reconciled pre/post).
>
> Work autonomously. Pause only if you hit something destructive or irreversible, a real scope change, or a question only I can answer — otherwise keep going; if you must stop, ask and end the turn rather than ending on a promise. Before reporting progress or completion, audit each claim against a tool result from this session — report only what you can point to, and say explicitly what is not yet verified. If a test fails, say so with the output. When you have enough information to act, act; don't re-derive what the repo already establishes.
>
> When finished, write the summary for someone who saw none of the work: lead with the outcome in one sentence, then what changed, what was verified and how, and anything needing my attention — each in plain language, no working shorthand.

### (e) Run settings
*Effort: xhigh — long-horizon agentic execution with self-verification is exactly its documented territory; on Claude Code, effort governs how far the run pushes (file reads, test runs, re-checks) before coming back, which is the behavior this task wants maximized. Web search: not needed. Surface: code (declared). If runs like this recur, add a lessons file per block AX's memory convention.*

### (f) Handoff note
*Run directly — for a repo-wide destructive-adjacent migration, optionally run prompt-optimizer-fable first; the verification spec is the part worth auditing.*

---

## Anti-sycophancy guard

The skill must never:
- Pick buckets that don't actually apply just to produce a longer technique stack
- Include every technique from the unioned stack without applying the 5-cap
- Skip the "considered and rejected" note in the classification section
- Guess at bucket classification (or execution mode, where consequential) when genuinely ambiguous — run clarification mode instead
- Produce a draft that doesn't actually use the techniques it claims to have selected
- Inject block AX into interactive-mode drafts, or dress a draft in agentic machinery to look sophisticated

## Architecture failure modes

1. **Silent single-turn default.** A SINGLE-TURN v1 draft when 2+ primary signals fired, without naming the signals or explaining why chain was rejected.
2. **Chain over-engineering.** A chain when 0 primaries fired (chain-as-sophistication-theater) — easier to commit on Fable 5, where the chain bar is higher; or a 5-stage chain when 3 suffice.
3. **Hidden strain.** A SINGLE-TURN (CEILING) output without the strain flagged. The middle band exists to make the silent ceiling visible.
4. **Unnamed architecture verdict.** A verdict without the specific signals that fired or didn't. Unfalsifiable verdicts are not permitted.
5. **Reasoning-echo installation (new in v1.0-fable).** A draft instructing Fable 5 to show/echo/transcribe its internal reasoning as response text — a production refusal risk (`reasoning_extraction`) with session reroute, not a style defect.

## Fable 5 notes

This skill is calibrated for Claude Fable 5. The technique stacks reflect that on Fable 5:

- Generic chain-of-thought is obsolete — holds more strongly than on any prior generation; use #5's scoped-reasoning form, and route depth through effort (Step 5), never through the prompt.
- "Show your reasoning / explain your thinking step by step" phrasings are not merely obsolete but refusal-risk (`reasoning_extraction`) — the drafting rules in Step 4 govern.
- Role prompting has diminishing returns; prefer #2 [house-validated / guide-silent — carried from prior-generation evidence].
- Persona granularity doesn't move analytical depth (reject this folklore).
- Anti-sycophancy works better as permission than instruction (#6), and is held at full strength [house-validated / guide-silent — no evidence of improvement on the reflexive-validation axis].
- Socratic prompting produces genuinely load-bearing questions [house-validated — unusually strong vs. other frontier models].
- Instruction-following is strong enough that one brief clear instruction replaces enumeration — prior-model instruction density in a draft is a defect, not diligence.
- The single-turn ceiling question is governed by the Step 2 fidelity test and its provisional backstop [VALIDATE], not by a fixed word count; the prior-generation ~3,000–5,000-word ceiling claim is retired.
