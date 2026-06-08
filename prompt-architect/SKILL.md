---
name: prompt-architect
description: Classifies a task against a 10-bucket taxonomy, decides output architecture (single-turn vs. chained multi-stage), and produces a technique-grounded v1 draft prompt. Verdicts are SINGLE-TURN, SINGLE-TURN (CEILING) with a strain note, or CHAIN producing a README + Project orchestrator + per-stage prompts. Use when you describe a non-trivial task but have no prompt written yet. Trigger phrases include "help me prompt this", "set up a prompt for", "draft a prompt for", "how should I ask Claude to", "architect a prompt for", "I'm about to start work on X — help me frame it". Also trigger for multi-step analytical tasks (research, knowledge document, design, decision support) where no draft exists. Do NOT trigger when a draft prompt already exists — that's prompt-optimizer. Do NOT trigger for tasks already covered by a dedicated domain-specific skill — route to that skill instead.
---

# Prompt Architect (State-of-the-Art Edition)

## CHANGELOG

**Validated for Opus 4.8 — June 2026.** Re-tested end to end against Claude Opus 4.8 with no changes required; the architecture-decision module, the 10-bucket taxonomy, and the technique stacks all hold. Published to the public repo at v2.

**v2 — May 2026.** Introduces architecture decision as a first-class step, in lockstep with the prompt-optimizer v4 upgrade.

The v1 skill had the same structural alignment problem the optimizer v3 had: it optimized a proxy (a well-formed single-turn v1 draft) rather than the actual target (state-of-the-art Claude output on the user's task). For genuinely complex work, those objectives diverge — forcing a single-turn structure compresses output the user could have had as a chain. The v1 Bucket 9 "two-phase handling" was a narrow workaround for one specific case (design/architecture); v2 generalizes it into an explicit architecture decision that applies to every IN-PURVIEW task.

v2 adds a new Step 2 (Architecture decision, between Step 1 Classify and Step 3 Technique selection). The decision drives branched behavior in Steps 3–4 and produces one of two distinct output formats:

- **Single-turn template** — the v1 output, refined. Includes a new SINGLE-TURN (CEILING) sub-band that makes the "fits but at the edge" cases visible to the user (previously silent).
- **Chain template** (new) — produces a README/setup guide, a Project orchestrator (custom instructions), and per-stage prompts as separate deliverables, mirroring the validated Project-orchestrated workflow pattern. Each stage prompt is itself technique-grounded — the 10-bucket taxonomy classifies *each stage* independently.

The architecture decision module — seven signals, three-way verdict, Project-vs-flat sub-decision, five guards — is **identical** to the prompt-optimizer v4 module. Lockstep is deliberate: if signal 1 fires in the optimizer, it must fire on the equivalent task here. Drift between the two entry points would produce inconsistent recommendations for the same underlying task.

**What v1 retained, unchanged in v2:** the 10-bucket taxonomy, the 18-technique library, the 5-technique cap, the union-the-stacks logic, the clarification mode, and the four-part output format for single-turn outputs. The architect's core generative discipline survives — it is now wrapped in architecture awareness, not replaced by it.

---

## Purpose

Convert a described task into a technique-grounded v1 draft prompt — or, when the task warrants it, into a technique-grounded *chain* of stage prompts plus a Project orchestrator. The skill does this by:

1. Classifying the task against the 10-bucket taxonomy
2. Deciding the output architecture (single-turn vs. chain)
3. Applying the right technique stack from the Top-Tier Prompting for Professionals v2 framework

This skill is the *generative* counterpart to `prompt-optimizer` (which is *evaluative*). Use the architect when you don't yet have a draft; use the optimizer when you do.

## Core philosophy

**Choose the architecture before choosing the structure.** Single-turn is the default delivery vehicle, but it is not the only one. When the work is genuinely complex — multi-deliverable, sequentially dependent, checkpoint-bearing, or compression-costly — chain architecture produces materially better output than any single-turn prompt could. The architect's job is to make that architecture call explicitly, name the signals that justified it, and produce a chain deliverable when chain is the right answer. Drafting a beautifully technique-grounded single-turn prompt for a task that deserved a chain is an alignment failure between the skill's stated mission (state-of-the-art output) and its mechanical behavior (single-turn polish).

## When to run the architect vs. other skills

- **Architect first, then optimizer (full rigor path)**: reserved for high-stakes prompts you will reuse or that anchor a critical decision. Architect produces v1, optimizer audits and produces v2.
- **Architect alone**: most non-trivial tasks. The v1 draft is usually good enough to run directly.
- **Optimizer alone**: when a draft prompt already exists and just needs auditing.
- **Dedicated skill instead of architect**: if the task maps to an existing dedicated skill in your library, route to that skill — it already encodes the right technique stack for its narrow domain. The architect is for tasks that don't fit an existing dedicated skill.

---

## The 10-bucket taxonomy

Every task is classified against these ten buckets. A task usually fits 1–3 buckets; multi-bucket tasks union their technique stacks (see Step 3 Selection Logic below).

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
The output serves a FUTURE reader (usually you, weeks or months later) who doesn't have the context of the current conversation. The task is to build a durable reference, class, or note.
- **Default stack**: #1 (success criteria — future-reader variant) + #10 (contrast pairing) + #3 (pedagogical output shaping) + #7 (worked-example anchoring for recurring series) + #18 (framework invocation if teaching a named framework) + #8 (uncertainty calibration)
- **Situational adds**: #15 (if building from pasted source material), #17 (if fact-dense)
- **Example tasks**: structured course/lecture notes in an established numbering scheme, a teaching note on a named model like Black-Litterman, a reusable diligence-framework write-up
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
- **Example tasks**: apply a fund-evaluation framework to a fund, a valuation framework to a stock, a scoring rubric to a manager or vendor

### Bucket 6 — Analytical synthesis / thesis work
Inputs already exist (in your head or in the conversation), need to be synthesized into a position, argument, or recommendation.
- **Default stack**: #1 (success criteria) + #5 (extended thinking with scope guidance) + #3 (output shaping)
- **Situational adds**: #6 (anti-sycophancy) + #9 (adversarial review) for defense-ready theses, #11 (pre-mortem), #13 (devil's advocate)
- **Example tasks**: "is sector X still attractive given Y", thesis defense memos, position reviews, cross-asset synthesis

### Bucket 7 — Recurring deliverable with house style
A format you produce repeatedly with known conventions — recurring executive briefings, status memos, template fills.
- **Default stack**: #7 (worked-example anchoring) + #3 (output shaping) + #2 (contextual priming)
- **Situational adds**: #1 (success criteria), #14 (iterative refinement)
- **Example tasks**: a recurring executive briefing in a new domain, a course note in the established format, a briefing replicating prior structure

### Bucket 8 — Default well-scoped analytical work
Clear input, clear question, standard output. No special structural requirements.
- **Default stack**: #1 (success criteria) + #2 (contextual priming) + #3 (output shaping)
- **Situational adds**: whatever the specifics suggest
- **Example tasks**: "explain X", "compare A vs B", standard analytical questions

### Bucket 9 — Design / architecture work
Build a system, framework, workflow, or analytical infrastructure. Output is a structural solution to a multi-constraint problem.
- **Default stack**: #4 (Socratic — MANDATORY for constraint elicitation) + #11 (pre-mortem on the design) + #13 (devil's advocate between design choices) + #3 (structured output for the architecture) + #1 (success criteria)
- **Situational adds**: #18 (if building on an existing framework)
- **Example tasks**: a strategic asset-allocation engine, a new evaluation-framework design, skill design, workflow infrastructure
- **TWO-PHASE NOTE (v2)**: The v1 skill flagged Bucket 9 as "phase 1 then re-route phase 2." In v2, this is a special case of the general architecture decision in Step 2. Bucket 9 tasks typically fire signals 2 (sequential dependency) and 3 (checkpoint value), and often warrant CHAIN architecture rather than the v1 punt-on-phase-2 pattern. Run Step 2 normally; if CHAIN fires, deliver the full chain instead.

### Bucket 10 — Persuasive writing / external communication
The output must move a specific reader toward a specific action or belief. Success is reader-centric, not analytical.
- **Default stack**: #2 (contextual priming — about the READER, not you) + #7 (worked-example anchoring) + #3 (output shaping to persuasive conventions) + #1 (success criteria — "what would make the reader say yes") + #14 (iterative refinement)
- **Situational adds**: #9 (adversarial review — "read this as the skeptical recipient")
- **Example tasks**: cover letters, recruiter outreach, client pitches, external memos, LinkedIn posts, investor communications
- **Reader-centric success criteria variant**: "A top-tier answer will make [specific reader] [specific action] within [timeframe]."
- **NOTE**: If a dedicated skill covers the specific deliverable (e.g., a job-application skill), route to it instead.

---

## Workflow

### Step 1 — Classify (10-bucket)

Identify which buckets apply to the task. Most non-trivial tasks fit 1–3 buckets. Do not force a single bucket if multiple apply. If classification is genuinely ambiguous between buckets whose technique stacks differ materially, run **clarification mode** (see below) before proceeding to Step 2.

State the bucket(s) explicitly and the considered-and-rejected alternatives. This is the anti-sycophancy guard — it forces the skill to defend its classification.

### Step 2 — Architecture decision (NEW in v2)

The skill's job is to produce state-of-the-art output from Claude — not to produce well-formed single-turn v1 drafts. Those objectives diverge on complex work, where forcing a single-turn structure compresses output the user could otherwise have had.

This step decides the *output architecture* for the v1 draft: single-turn, single-turn (ceiling) (strained but fits), or chain (sequential stage prompts with handoffs and optional human-in-loop checkpoints). The decision drives downstream branching of Steps 3–4.

**Default:** single-turn. Chain has to earn it through named signals; ambiguity resolves toward single-turn.

The seven signals below are evaluated from the user's task description (no prompt exists yet — that's the architect's whole point). Read the task description carefully and flag which signals fire.

#### Trigger signals

Seven signals. Primary signals (1–4) drive the verdict. Amplifiers (5–7) modulate within bands.

**Primary signals:**

1. **Multi-deliverable** — the task requires two or more distinct artifacts with materially different shapes (e.g., data sheet + analytical memo + scenario matrix). Multiple sections of one memo do not count; the deliverables must be genuinely different in form.

2. **Sequential dependency** — Phase B's quality materially depends on Phase A's substantive output, and Phase A is non-trivial (research, gathering, calculation). Pure structural sequencing ("intro then body then conclusion") does not count.

3. **Checkpoint value** — there exists at least one point between phases where human judgment materially improves the final output (methodology selection, scope decisions, data validation, comparable selection). If a "continue" between phases would only be ceremonial, this signal does not fire.

4. **Compression cost** — fitting the full work into one turn would force compression of sections that lose fidelity. A 3×2 scenario matrix compressed into six bullets is the canonical example.

**Amplifying signals:**

5. **Reusability premium** — the prompt (once drafted) will be re-run many times across different inputs (>5 expected runs). Chain infrastructure setup cost amortizes; single-use single-turn does not benefit from chain structure.

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

**Important:** the architect presents its heuristic recommendation but asks the user to confirm or switch. The chain deliverables are produced in the next turn after the user's choice. This is a deliberate two-turn split that mirrors the methodology checkpoint pattern in well-designed chains themselves.

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
> [For CHAIN: brief skeleton — number of stages, what each does, where checkpoints sit, Project-vs-flat heuristic recommendation phrased as an explicit question to the user. Then STOP and await user confirmation before proceeding to Steps 3–4. The chain deliverables are produced in the next turn.]

Carry the verdict into Step 3. The technique selection branches on architecture: SINGLE-TURN and SINGLE-TURN (CEILING) follow the standard union-and-cap flow; CHAIN runs technique selection *per stage*.

### Step 3 — Technique selection (architecture-aware)

#### Branch A: SINGLE-TURN / SINGLE-TURN (CEILING)

The standard architect technique-selection flow:

1. **Union the stacks.** Combine the technique stacks of all applicable buckets from Step 1. Deduplicate.
2. **Apply the 5-technique cap.** The v1 draft includes a maximum of 5 techniques. Trim using this priority order:
   - **Mandatory-for-the-task** techniques first (e.g., #18 if framework exists, #15 if documents pasted, #4 if research or design)
   - **Mandatory-for-the-stakes** techniques second (#4, #11 for high-stakes; #17 for catastrophic-on-error)
   - **Structural-for-the-output** techniques third (#3, #1)
   - **Situational** techniques fourth (#6, #8, #9, #14, etc.)
3. **List the rest** as "Available on request" — not included in the draft, but visible to the user.

For SINGLE-TURN (CEILING), additionally prepare a one-paragraph **strain note** acknowledging which signal fired, what compression the v1 draft will reflect, and what the user gives up by staying single-turn. The user can decide to escalate to chain in a follow-up.

#### Branch B: CHAIN

Per-stage technique selection. Each stage is itself a focused prompt — bucket-classified, technique-grounded, scope-disciplined.

1. **Decompose into stages.** Smallest chain that does the job. Typical chains are 3–5 stages. Stage names should describe the work (e.g., "Stage 1: scoping & question elicitation", "Stage 2: research & retrieval", "Stage 3: synthesis & adversarial review").

2. **Per-stage bucket classification.** Each stage maps to one or more buckets. A research-then-synthesize chain might have Stage 1 in Bucket 2 (research) and Stage 2 in Bucket 6 (synthesis). The bucket call drives the per-stage technique stack.

3. **Per-stage technique stack (5-cap per stage).** Apply the standard union-and-cap logic *within each stage*. Cross-chain coordination techniques (#12 decomposition with explicit handoffs) live at the orchestrator level, not inside individual stages.

4. **Handoff contracts.** Each stage's output specifies a structured handoff block — variables, intermediate artifacts, methodology decisions — that the next stage substitutes in cleanly. No re-prompting the user for the same variable mid-flow.

5. **Checkpoint placement.** Place human-in-loop checkpoints only where judgment materially improves output (methodology selection, scope decisions, data validation, comparable selection). Do not place ceremonial checkpoints.

The deliverables for CHAIN architecture are produced in Step 4 Branch B (see below).

### Step 4 — Draft the deliverable (architecture-branched)

#### Branch A: SINGLE-TURN / SINGLE-TURN (CEILING)

Produce a single v1 prompt in a code block, structured with the selected techniques' conventions (success criteria blocks, delimiters, Socratic instructions, etc.). Label it "v1 — technique-grounded draft."

For SINGLE-TURN (CEILING), include the strain note from Step 3 Branch A *outside* the prompt itself (in the output, not in the prompt the user copies).

#### Branch B: CHAIN

Produce a chain deliverable mirroring the validated Project-orchestrated workflow pattern:

1. **README / setup guide** (`README-setup.md`) — one-time setup steps + recurring usage flow + maintenance notes.
2. **Project orchestrator** (`project-instructions.md`, if Project-orchestrated) — the custom instructions block containing role, discipline, research protocol, methodological constraints, output conventions, AND the orchestration protocol that handles variable collection, stage triggers, preconditions, methodology checkpoints, variable overrides, and error states.
3. **Stage prompts** (`stage-1-[name].md`, `stage-2-[name].md`, ...) — one file per stage, each containing task definition, execution sequence, output format with handoff block, and scope discipline. Variables referenced as `{VARIABLE_NAME}` substituted at runtime by the orchestrator.

For flat-sequential chains, omit the orchestrator and produce only the README plus the standalone stage prompts. The user pastes them sequentially in the same chat.

**Production guidance for chain deliverables:**

- Use the `create_file` and `present_files` tools to deliver each file separately when code execution is available. This mirrors how the user will store and upload them.
- If code execution is not available, produce each file as a clearly-labeled code block in chat with the filename as the block header.
- Each stage file references only the variables it needs. Don't pass `{CLAIM_TO_TEST}` into Stage 1 if Stage 1 doesn't use it.
- Handoff blocks at the end of each stage are structured, machine-readable, and substitution-ready for the next stage.
- The orchestrator handles variable collection at chat initialization (the user shouldn't have to declare variables in each stage trigger).
- The orchestrator handles methodology checkpoints and variable overrides explicitly — see a validated Project-orchestrated chain as the reference pattern.

Preserve the original task's intent and voice across all stages. Even at IN PURVIEW + CHAIN, do not pile on techniques that don't earn their place.

---

## "I'm not sure which bucket" — clarification mode

If after reading the task description you cannot confidently identify at least one bucket (or you're torn between buckets whose technique stacks would differ materially), DO NOT guess. Instead, run a micro-Socratic clarification step:

- Return 2–4 targeted questions that would resolve the classification ambiguity.
- Each question should map to a specific bucket distinction (e.g., "Is the source material already in hand, or does it need to be gathered?" distinguishes Bucket 1 from Bucket 2).
- Rank the questions by how much they'd shift the classification.
- Do NOT draft the v1 prompt or run Step 2 architecture decision until you answer. Architecture decisions depend on bucket clarity.
- This is the skill eating its own dogfood: using technique #4 to resolve its own classification ambiguity.

Only enter clarification mode when genuinely needed. If the task is clear and one or more buckets obviously apply, proceed to Step 2.

---

## Output formats

The skill produces one of two output formats, selected by the Step 2 architecture verdict.

### Format A — SINGLE-TURN / SINGLE-TURN (CEILING)

Use for ARCHITECTURE verdicts of SINGLE-TURN or SINGLE-TURN (CEILING). The skill ALWAYS produces output in this exact five-part structure:

#### (a) Task classification
- Bucket(s) identified, with a one-line justification for each
- Considered-and-rejected buckets, with rationale (the anti-sycophancy guard)

#### (b) Architecture decision
- The Step 2 verdict, signals fired (named explicitly), and — for SINGLE-TURN (CEILING) — the strain note explaining what the user gives up by staying single-turn

#### (c) Technique selection
- The 5 techniques (or fewer) selected for the v1 draft, each with a one-line justification
- Techniques available from the unioned stack but cut by the 5-technique cap, listed under "Available on request"

#### (d) v1 draft prompt
- The actual prompt, ready to copy and use
- Structured with the selected techniques' conventions (success criteria blocks, delimiters, Socratic instructions, etc.)
- Labeled "v1 — technique-grounded draft"

#### (e) Handoff note
One line telling you what to do next:
- "Run directly" — the draft is ready, no further work needed
- "Optionally run prompt-optimizer for final audit" — draft is good but optimizer could add rigor for high-stakes cases
- "Recommended: run prompt-optimizer before executing" — high-stakes or reusable enough to justify the extra turn
- "Strain noted — consider escalating to CHAIN if the compression matters" — for SINGLE-TURN (CEILING)

### Format B — CHAIN (two-turn delivery)

Use for ARCHITECTURE verdicts of CHAIN.

**This is a two-turn deliverable.** Turn 1 delivers the architecture decision and skeleton, asking the user to confirm Project-orchestrated vs. flat-sequential. Turn 2 (after the user's choice) delivers the full chain.

#### Turn 1 — Architecture & skeleton

Return exactly these sections:

**SECTION 1 — TASK CLASSIFICATION**
Bucket(s) identified per Step 1, with a one-line justification each and considered-and-rejected alternatives.

**SECTION 2 — ARCHITECTURE DECISION**
The Step 2 verdict (CHAIN), primary signals fired (named explicitly), amplifiers (named), and the chain shape rationale. Make clear which signals justified the chain — the recommendation must be falsifiable.

**SECTION 3 — CHAIN SKELETON**
- Number of stages
- One-sentence purpose of each stage
- Per-stage bucket classification (preview)
- Where the human-in-loop checkpoints sit and what decision each one captures
- Handoff contracts (what each stage passes to the next)

**SECTION 4 — DELIVERY VEHICLE QUESTION**
State the heuristic recommendation (Project-orchestrated or flat-sequential) and the reasoning. Ask the user explicitly:

> "Recommended delivery: [Project-orchestrated | flat-sequential]. Confirm or switch?"

Then stop. Do not produce the full chain deliverables until the user has answered.

#### Turn 2 — Full chain deliverable

After the user confirms or switches, deliver the remaining sections.

**SECTION 5 — PER-STAGE TECHNIQUE STACKS**
For each stage: bucket classification, the 5 (or fewer) techniques selected, "Available on request" leftovers.

**SECTION 6 — DELIVERABLES**
Each file as a separate `create_file` call (preferred when code execution is available) or a clearly-labeled code block (fallback):

- `README-setup.md` — one-time setup + recurring usage flow + maintenance notes
- `project-instructions.md` — the orchestrator (Project custom instructions) — included only for Project-orchestrated chains
- `stage-1-[name].md`, `stage-2-[name].md`, ... — one per stage

After producing the files, call `present_files` with all of them.

**SECTION 7 — HANDOFF NOTE**
Operational flow for the chain, expected output per stage, checkpoint locations, and maintenance notes. Indicate whether running prompt-optimizer on the orchestrator/stages is recommended for high-stakes use.

---

## The 18 techniques (reference)

Full descriptions live in the Top-Tier Prompting for Professionals v2 document. Short reference:

1. **Task framing with explicit success criteria** — state the task, what top-decile looks like, what would make it fail
2. **Contextual priming** — who you are, what you're doing, what you've already decided
3. **Structured output shaping** — specify the shape (table columns, section names) before the task
4. **Socratic prompting** — ask the model to ask you the 5–8 questions that would most change its answer
5. **Extended thinking with scope guidance** — scope the reasoning, don't just trigger it
6. **Anti-sycophancy licensing** — grant specific permission to disagree, refuse, or propose alternatives
7. **Worked-example anchoring** — one high-quality example of the target output
8. **Explicit uncertainty calibration** — tag claims High/Medium/Low confidence inline
9. **Adversarial review as a second pass** — critique in a fresh turn, not the generation turn
10. **Contrast pairing** — "explain X vs Y assuming I know Y"
11. **Pre-mortem framing** — "assume this failed in 18 months — walk back the causal chain"
12. **Decomposition with explicit handoffs** — break into steps, name what passes between them (orchestrator-level for chains)
13. **Devil's advocate dialectic** — stage a short exchange between two partisan positions
14. **Iterative refinement with explicit dissatisfaction** — flaw + target + preserve
15. **Deliberate context loading + multi-chunk delimitation** — paste source, delimit chunks explicitly, instruct "answer only from the material above"
16. **Response prefilling** — API/Claude Code only; force first tokens
17. **Chain of Verification (CoVe)** — generate → extract claims → verify each → revise
18. **Reference-anchored reasoning** — invoke named frameworks explicitly

---

## Worked example — Research task (Bucket 2) [SINGLE-TURN verdict]

**User input**: "I want to research the global market for lithium — I might write something up for my investment notes."

**Skill output**:

### (a) Task classification
- **Bucket 2 (Research and investigation)** — primary. No source material in hand; output requires retrieval and synthesis.
- **Bucket 3 (Knowledge document)** — secondary. The phrase "investment notes" signals a future-reader artifact, not a one-off analytical answer.
- **Considered and rejected**: Bucket 6 (synthesis) — rejected because synthesis assumes inputs exist; here they must be gathered first. Bucket 8 (default) — rejected because research without Socratic scoping produces generic output.

### (b) Architecture decision

**ARCHITECTURE: SINGLE-TURN.** Primary signals fired: none. Amplifiers: 5 (mild — future-reader artifact, but one-off not a template).

Research-and-write-up tasks fit in one turn when scope is appropriately bounded (which is what Socratic scoping does). No multi-deliverable need, no sequential dependency the user needs to gate (the Socratic clarification *is* the gate), no checkpoint value beyond it, no compression cost at typical research-note length. Single-turn it is.

### (c) Technique selection
1. **#4 Socratic prompting** — mandatory for Bucket 2. "Research lithium" is dangerously underspecified (supply/demand/price/geopolitics/investment angle? time horizon? for whom?).
2. **#15 Context loading with delimitation** — for retrieved sources; instruct the model to quote specific passages for non-obvious claims.
3. **#8 Uncertainty calibration** — research outputs mix facts and inferences; tagging confidence is load-bearing.
4. **#3 Output shaping** — pedagogical structure because the output is also a knowledge artifact (Bucket 3 influence).
5. **#1 Success criteria** — future-reader variant because of the Bucket 3 overlap.

*Available on request*: #17 (CoVe on fact-dense sections), #10 (contrast pairing if the note should compare lithium to another battery metal), #13 (devil's advocate on contested forecasts).

### (d) v1 draft prompt — technique-grounded draft

> I want to build a research note on the global lithium market that will live in my investment notes for future reference.
>
> **Before you research or draft anything**, ask me the 5–7 questions whose answers would most change the shape of the output. Rank them by how much they'd shift the final note. Specifically interrogate: (a) the angle — supply-side dynamics vs demand-side vs price mechanics vs geopolitical exposure vs investable thesis, (b) the time horizon I care about, (c) the decision this note supports (if any), (d) what I already know vs what's new to me, (e) the level of technical depth I want. Do not start researching until I've answered.
>
> Once I've answered, proceed as follows:
>
> **Success criteria.** A top-tier note will let a reader (future me) who hasn't thought about lithium in 6 months reconstruct the load-bearing intuition in under 10 minutes and still have enough precision to act on it. A failing note will be a generic sector overview with no load-bearing claims I couldn't have gotten from a Wikipedia summary.
>
> **Research discipline.** Use web search to retrieve sources. Wrap each retrieved source as `<source index="n">` with a brief identifier. In the final note, for every non-obvious claim, cite the source index inline. Do not make claims you cannot ground in a retrieved source or in widely-established structural facts about the industry.
>
> **Confidence tagging.** Tag every non-trivial claim inline as [High — I'd stake the note on this], [Medium — directionally right, worth verifying], or [Low — inference I'd want to check]. I specifically want to see where you're extrapolating vs citing.
>
> **Output structure.** Produce the note with these sections: (1) One-paragraph orientation — what this market actually is and why it matters for an allocator. (2) Supply side — key producers, concentration, reserve geography, production technology. (3) Demand side — end uses, growth vectors, substitution risk. (4) Price mechanics — how lithium is priced, who sets the price, volatility drivers. (5) Load-bearing uncertainties — the 3–4 things that would most change the investable view. (6) What would have to be true for this to matter to my book.
>
> Keep filler out. Every sentence should carry a fact, a mechanism, or a load-bearing uncertainty. Do not include executive summaries or conclusions that restate the note.

### (e) Handoff note
*Optionally run prompt-optimizer for final audit — this is a reusable research prompt template and the rigor would compound across future research notes.*

---

## Worked example — Design/architecture task (Bucket 9) [CHAIN verdict]

**User input**: "I want to design a systematic framework for evaluating sovereign wealth fund annual reports to extract their allocation shifts and strategic signaling. I'll need to apply it to ~15 funds over the next quarter."

**Skill output** — **Turn 1**:

### SECTION 1 — Task classification
- **Bucket 9 (Design / architecture work)** — primary. The task is to build a framework, not apply one. Multi-constraint: repeatability, signal extraction, comparability across funds.
- **Bucket 5 (Framework application)** — secondary. The downstream work is applying the framework to specific SWFs.
- **Considered and rejected**: Bucket 3 (knowledge document) — the framework will eventually live as a knowledge artifact, but the immediate task is design then application, not pedagogy.

### SECTION 2 — Architecture decision

**ARCHITECTURE: CHAIN.** Primary signals fired: 1 (multi-deliverable: framework design + application output per fund), 2 (sequential dependency: cannot apply what hasn't been designed; design depends on constraint elicitation), 3 (checkpoint value: after framework design, before applying to 15 funds — wrong framework wastes 15 fund-applications). Amplifiers: 5 (reusability — 15 expected runs), 6 (cognitive mode switching: design vs application are distinct modes).

Three primary signals + two amplifiers is unambiguous CHAIN territory. The v1 Bucket 9 "phase 1 then re-route phase 2" pattern is exactly the failure mode this architecture decision was built to replace.

### SECTION 3 — Chain skeleton

3-stage chain.

- **Stage 1: Framework design** (Bucket 9). Socratic constraint elicitation → pre-mortem → devil's advocate on design tradeoffs → structured output of the framework architecture. Handoff: the framework specification.
- **Stage 2: Framework instance application** (Bucket 5). Apply the framework from Stage 1's handoff to one specific SWF's annual report. Handoff: structured extraction output per fund.
- **Stage 3: Cross-fund synthesis** (Bucket 6). After applying Stage 2 to all funds in scope, synthesize allocation-shift patterns and strategic-signaling patterns across the cohort. Handoff: comparative memo.

Human-in-loop checkpoint after Stage 1: methodology validation (does the framework actually capture what the user wanted before being run 15 times). No checkpoint between Stages 2 and 3 — Stage 3 runs once on the full cohort.

### SECTION 4 — Delivery vehicle

**Recommended delivery: Project-orchestrated.** Signal 5 (reusability) fires strongly — 15 fund-applications justify the one-time setup cost. Orchestrator handles variable substitution (`{FUND_NAME}`, `{ANNUAL_REPORT}`), methodology checkpoint between Stages 1 and 2, and stage triggers from short user commands.

Recommended delivery: **Project-orchestrated**. Confirm or switch?

---

(Turn 2 would then produce `README-setup.md`, `project-instructions.md`, `stage-1-framework-design.md`, `stage-2-framework-application.md`, `stage-3-cross-fund-synthesis.md` via `create_file` + `present_files`.)

---

## Worked example — Clarification mode (ambiguous bucket)

**User input**: "I need to put together something on Rheinmetall."

**Skill output** (clarification mode, no draft yet, no Step 2 yet):

### Task classification — needs clarification
The task is unclear between at least four buckets and the technique stacks would differ materially. Before drafting (or running the architecture decision — which depends on bucket clarity), I need to resolve:

1. **What kind of output?** (most load-bearing) — (i) an investment thesis memo I'll defend internally (Bucket 6), (ii) a research note for future reference (Buckets 2+3), (iii) a decision on a specific trade (Bucket 4), (iv) a lesson on defence sector dynamics using Rheinmetall as the case (Bucket 3), (v) external communication (Bucket 10)?
2. **Is source material in hand?** (distinguishes 1 vs 2) — do you have the 10-K, sell-side notes, prior valuation work, or is this starting from zero?
3. **Is this a one-off or a template?** (signals Bucket 7 + amplifier signal 5) — if you're going to produce equivalents for Leonardo, BAE, Saab afterwards, both the framework invocation and the architecture decision change.
4. **Is there a specific decision horizon?** (signals Bucket 4) — are you about to buy, or is this upstream of that?

Answer any or all of these and I'll classify, run the architecture decision, and draft.

---

## Anti-sycophancy guard

The skill must never:
- Pick buckets that don't actually apply just to produce a longer technique stack
- Include every technique from the unioned stack without applying the 5-cap
- Skip the "considered and rejected" note in the output's classification section
- Guess at bucket classification when the task is genuinely ambiguous — run clarification mode instead
- Produce a draft that doesn't actually use the techniques it claims to have selected

## Architecture failure modes (NEW in v2)

The architecture decision introduces three new failure modes the skill must avoid:

1. **Silent single-turn default.** Producing a SINGLE-TURN v1 draft when 2+ primary chain signals fired, without naming the signals or explaining why chain was rejected. This is the v1 ceiling the architecture decision exists to break.

2. **Chain over-engineering.** Producing a chain when 0 primary signals fired (chain-as-sophistication-theater). Single-turn is the default; chain must earn it. Also: producing a 5-stage chain when 3 stages would suffice. The smallest chain that does the job is the right chain.

3. **Hidden strain.** Producing a SINGLE-TURN (CEILING) output without flagging the strain to the user. The whole point of the middle band is making the silent ceiling visible.

4. **Unnamed architecture verdict.** Producing a chain (or refusing to chain) without naming the specific signals that fired or didn't. The verdict must be falsifiable.

## Claude-specific notes

This skill is calibrated for Claude Opus 4.8. The technique stacks reflect the fact that on Opus:
- Generic chain-of-thought is obsolete (use #5 instead)
- Role prompting has diminishing returns (use #2 instead)
- Persona granularity doesn't move analytical depth (reject this folklore)
- Anti-sycophancy works better as permission than instruction (#6)
- Socratic prompting produces genuinely load-bearing questions (unusually strong vs other frontier models)
- Extended reasoning is default — scoping it matters more than triggering it (#5)
- Single-turn output ceiling is ~3000–5000 words of dense work; anything beyond should be CHAIN
