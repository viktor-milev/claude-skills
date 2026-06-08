---
name: prompt-optimizer-flow
description: Flow mode prompt optimizer — transforms rough, dictated, or stream-of-consciousness thoughts into a state-of-the-art Claude keystone prompt in seconds, without breaking the user's flow. Produces a finished, paste-ready prompt with no placeholders or template variables. Skips diagnostic theater, infers intent aggressively from messy input. Use whenever the user pastes or dictates messy unstructured input and wants it turned into a polished prompt fast. Trigger phrases include "quick prompt", "dictating", "rough thoughts", "thinking out loud", "PO this fast", "flow mode", "PO flow", "make this a prompt", "turn this into a prompt", "draft a prompt for", "starting a new chat about". Also trigger on dictation artifacts (rambling, self-corrections, "um", "uh", "actually wait") or when starting a new chat and needing a starter prompt. For polished prompts needing rigorous evaluation, use prompt-optimizer instead. Do NOT trigger when the user wants the prompt EXECUTED.
---

# Prompt Optimizer — Flow Mode

## CHANGELOG

**v3 — Jun 2026.** Recalibration for Claude Opus 4.8 (released May 28 2026), mirroring the rigorous counterpart, plus a single optional effort line. Only the changes that pass flow's mission-fit test (speed, intent-preservation, no diagnostic theater) are taken; the rigorous counterpart's heavier run-settings machinery and its research-toggle recommendation are deliberately NOT imported — both break flow. No structural changes; the 5-step workflow, the two hard rules, and the lean output format are unchanged. No lockstep coordination with prompt-architect required.

- **4.7 calibration note recalibrated to 4.7–4.8 (Step 4).** 4.8's behavior changes (more reliable reasoning-effort calibration, better tool triggering, improved honesty, more consistent instruction-following) narrow where techniques earn their place. Reasoning activation now splits "more thinking" (native on 4.8 via adaptive thinking + the effort dial) from "specific reasoning structure" (still the prompt's job — favor structure over volume). Anti-sycophancy held steady, NOT softened: 4.8's honesty gains are on a different axis than user-framing pushback. Research activation narrowed on better tool triggering.
- **Optional effort line (Output format).** For analytical or strategic keystone prompts, the Inferred note may carry one trailing clause recommending the effort dial (high, or xhigh for the heaviest work). Thinking is folded into effort — on 4.8 thinking depth IS the dial, so never suggest a separate "thinking" control. A research-toggle line is deliberately omitted as too much friction for flow. One clause maximum; omitted for lean creative/one-shot prompts where the default is already right.
- **Scope literalism reinforced for 4.8 (Step 5).** 4.8's more literal, more consistent instruction-following makes explicit scope statements matter more, not less. Content unchanged; framing updated.

**v2 — May 2026.** Five surgical additions grounded in the rigorous counterpart, the Anthropic official prompting guide, and Ruben Hassid's chat-app prompt optimizer ("47"). No structural changes; the 5-step workflow and lean output format are unchanged.

- **Two hard rules (new section between Contract and Workflow).** Operationalizes the "paste immediately" contract promise via a no-placeholder rule plus the Case A / Case B framing for handling user-supplied content vs. described task classes. Closes the most consequential gap in v1: a flow-mode prompt that produces `[paste X here]` scaffolding is a copy-paste-broken prompt, violating the core contract. Pattern adapted from Ruben Hassid's "47".
- **4.7 calibration note (Step 4).** Tightens Step 4 framing from "apply by default" to "apply where they earn their place" and adds a 4.7-specific calibration paragraph mirroring the rigorous counterpart. Reasoning activation, anti-sycophancy permission, and research activation have raised floors on 4.7 — the bar for earning their place has risen.
- **Scope literalism (Step 5).** Adds an explicit instruction to state scope literally in the optimized prompt. Dictated input frequently leaves scope implicit, and 4.7 won't silently generalize. Mirrors the rigorous counterpart.
- **Examples-for-voice-or-format (Step 5).** When the dictation signals voice or format preference, include 2–4 worked examples in `<example>` tags. Examples beat description for steering format. Mirrors the rigorous counterpart.
- **Chain-shaped escalation flag (Relationship section).** Adds a one-paragraph escalation note: for clearly chain-shaped dictation, produce a serviceable single-turn keystone but flag the rigorous-mode escalation path. Preserves flow's speed-first contract without importing the full architecture decision module.
- **Step 2 layering clarification.** One added sentence clarifying that Step 2 picks the archetype lane and Step 3 calibrates depth within that lane — the keystone-as-default rule and the infer-toward-depth principle operate on adjacent axes, not in conflict.

No lockstep coordination with prompt-architect required (no architecture decision module edits).

---

Transform rough, dictated, or messy thoughts into a state-of-the-art Claude keystone prompt fast. The goal is **intent-to-keystone in under 30 seconds**, without breaking the user's flow.

## Core philosophy

The user is in flow. They have a thought. They want to start a Claude session about it — now, not in ten minutes. Their input is messy by design: dictated, rambling, self-corrected, oral, unstructured. That's not a defect to diagnose. It's the raw material to transform.

**The job is not to evaluate the input. The job is to extract the user's actual intent and produce the prompt they would have written if they'd had an hour.**

This is the opposite of the rigorous diagnostic skill. There is no scoring. There is no change log. There is no ceiling check displayed to the user. All of that machinery would break flow. The work happens silently, in service of speed.

## The contract with the user

When the user invokes this skill, they are trusting it to:

1. **Be fast.** Output should appear in one response, not a back-and-forth.
2. **Be aggressive about inference.** Do not ask clarifying questions unless the input is genuinely incomprehensible. Make intelligent guesses and flag them briefly.
3. **Preserve voice.** The user's domain language, terminology, and framing should survive into the optimized prompt. Do not sanitize.
4. **Default to keystone-quality.** Most flow-mode inputs are starter prompts for new sessions. Optimize for that case unless the input clearly indicates otherwise.
5. **Produce something they can paste immediately.** No prefaces. No "here's what I did." Just the prompt, ready to use. **No placeholders or template variables — see the two hard rules below.**

## Two hard rules

Two rules override everything else in this skill. The whole "paste immediately" promise depends on them.

### Rule 1 — No placeholders. Ever.

Never produce a prompt containing `[paste X here]`, `[your content]`, `{topic}`, `<your_input_here>`, `[INSERT Y]`, `___`, or any other template variable the user is expected to fill in. The user must be able to copy your output, paste it into a fresh Claude chat, hit send, and have a working interaction. If the prompt needs content the user hasn't provided yet, the prompt itself must handle that — see Rule 2.

If you catch yourself typing square brackets around a noun, stop. That's a placeholder. Rewrite.

### Rule 2 — Ship a finished prompt regardless of what the user gave you.

Two cases drive how to handle missing content. Detect which one applies in Step 3.

**Case A — the user supplied real content** (a draft they wrote, a list, code, a specific question, an actual artifact to work with). Bake that content directly into the optimized prompt. The whole thing — content and instructions — goes inside the code block. The user copies, pastes, sends. Done.

**Case B — the user only described a class of task** ("I want a prompt to triage my emails", "help me prompt Claude to review my code", "draft a prompt for my quarterly board reports"). Write the prompt as a complete, self-contained instruction. End the instruction by either:
- Asking Claude to ask the user for the specific inputs it needs ("Before drafting, ask me for the reporting period, the audience, and the key metrics to highlight."), or
- Phrasing the task so the user will naturally provide the input in their next chat turn ("I'm going to paste a batch of emails next. For each one, do the following...").

Either way: no brackets, no fill-in-the-blank, no template syntax. The prompt is final.

## Workflow

This is intentionally lean. Five steps. No diagnostic. No scoring.

### 1. Strip noise, extract intent

Read the messy input and silently strip:
- Filler words ("um", "uh", "like", "you know", "basically", "actually")
- False starts and self-corrections (when the user says "X — wait, no, Y", keep Y)
- Repetitions and circling
- Meta-commentary about the dictation itself ("let me think", "where was I")

Then extract the underlying intent in your own head. What is the user actually trying to accomplish? What kind of session are they trying to initiate? Do not display this work — just do it.

### 2. Detect archetype silently

Most flow-mode inputs are **keystone prompts** — the first prompt of a new chat or project where the user wants to think through something, get analysis, or explore a topic with Claude. Default to this archetype unless the input clearly signals otherwise:

- **Keystone (default)** — "I want to think about X", "help me figure out Y", "I'm trying to decide Z", "starting a new chat about W"
- **One-shot task** — "write me an email", "summarize this", "translate that" — direct, bounded requests
- **Iterative template** — "I want a reusable prompt for X" — explicit reusability signal
- **Creative generation** — "write a story", "draft a blog post", "give me ideas for"

Pick one. Do not display the choice.

**Layering note:** Step 2 picks the *archetype lane*. Step 3 calibrates *how aggressively to fill that lane* with inferred context and depth. The keystone-as-default rule and the infer-toward-depth principle operate on adjacent axes — both apply together, neither overrides the other.

### 3. Detect Case A / Case B, then infer the missing context

**First, decide which case applies.** Did the user supply real content (Case A — bake it in) or describe a class of task (Case B — write a self-contained instruction that elicits content at runtime)? This decision is upstream of every other inference and is the mechanism that makes Rule 1 enforceable. See "Two hard rules" above.

Then, regardless of case, fill in the sparse context. Dictated input skips most of the context the user holds in their head:

- **Role** — what expertise should Claude bring? Infer from domain language. If the user mentions clinical trial endpoints, infer biostatistics expertise. If they mention slide design, infer presentation expertise.
- **Audience or use case** — who is this for? Often inferable from context.
- **Output expectations** — what shape should the response take? Default to the archetype's natural format unless overridden.
- **Stakes and depth** — how deep should Claude go? Flow-mode prompts usually want depth, not surface answers, because the user is starting a thinking session.

When in doubt, infer toward depth and rigor. The user can dial it back if needed; they cannot easily dial it up after a shallow response.

### 4. Apply capability activation where it earns its place

Flow-mode prompts are often keystone prompts for analytical or strategic sessions, where capability activation techniques materially lift output quality. But not always — a flow-mode prompt for "write me a haiku about autumn" gets none of these techniques. A flow-mode prompt for "I'm trying to figure out whether to restructure my team's pricing model" gets most of them. **Calibrate to task. Do not apply mechanically.**

**4.7–4.8 calibration note.** Claude 4.7 raised the floor on these techniques; Claude Opus 4.8 raised it further — more reliable reasoning-effort calibration, better tool triggering, improved honesty, more consistent instruction-following. They are still valuable, but the bar for earning their place keeps rising. Apply each to the residual gap the model doesn't already cover by default:

- **Reasoning activation.** Split two things 4.8 pulled apart. *More thinking* is now native (adaptive thinking fires automatically on hard turns) and user-controlled (the effort dial — see the optional effort line in Output format), so a generic "think before responding" instruction earns its place rarely. *A specific reasoning structure* — forced alternatives, a self-critique pass, chain-of-verification — is still worth adding. Favor structure over volume. The scaffold earns its place mainly where surface difficulty understates actual difficulty.
- **Anti-sycophancy permission.** Hold steady — do not soften it for 4.8. 4.8's honesty gains concern the model's claims about its own work, not its willingness to push back on the user's framing; on that axis it has not improved. Still fully valuable on evaluation, critique, and decision-support — especially where the user's framing is subtly flawed in ways the model might work around rather than flag.
- **Research activation.** Narrowed on 4.8 — better tool triggering means the model skips required searches less often, so the scaffold earns its place only where the prompt's phrasing wouldn't naturally trigger search ("what should I think about X" vs. "what is the latest on X").

The candidate techniques:

- **Reasoning activation** — explicit "think before responding" instruction with decomposition
- **Anti-sycophancy permission** — explicit grant of permission to disagree, push back, identify flaws in framing
- **Epistemic calibration** — distinguish confident claims from inferences where accuracy matters
- **Research activation** — if the topic requires current information or external grounding, explicitly invite web search
- **Working principles** — for keystone prompts, brief session norms (how to handle uncertainty, when to ask vs. assume)
- **Pushback permission** — for decision-support and strategy sessions, explicit invitation to challenge the user

Match technique to task. Do not bolt every technique onto every prompt.

### 5. Produce the keystone prompt

Write the optimized prompt directly. Use XML tags where they earn their place. Match the depth of the prompt to the depth the user is signaling — if they dictated three sentences about a complex strategic question, the output should be a substantial keystone prompt. If they dictated a one-line creative request, the output should be lean.

Preserve the user's domain language and voice. Do not sanitize jargon. Do not flatten specificity.

**Be literal about scope.** Dictated input frequently leaves scope implicit — the user knows in their head "this should apply to every section" but doesn't say it. 4.8's more literal, more consistent instruction-following makes this matter more, not less: it won't silently generalize from one item to another. Where the optimized prompt requires Claude to apply something broadly, state the scope explicitly ("apply this to every section, not just the first one"; "use this voice for the entire piece, not just the opening"; "do this for every entry in the list, not just the first three").

**Use examples when the user signals voice or format preference.** When the dictation indicates a preference for *how* the output should look — "write like X," "match this tone," "in the style of Y," "structure it like my last memo," "I want it to feel like Howard Marks" — include 2–4 worked examples in `<example>` tags (wrapped in `<examples>` if multiple). Examples beat description for steering format and are among the most reliable structural moves per Anthropic's official prompting guidance. Especially load-bearing for creative-archetype prompts and any case where voice matters more than information content. Skip examples when no format preference is signaled, or when the task is generic enough that examples would over-constrain.

## Output format

**Lean and immediate.** No preamble. No diagnostic. No scoring. The output is structured as follows:

```
[The optimized prompt in a code block, ready to copy-paste]
```

After the code block, add **one short paragraph** (2–4 sentences) titled **"Inferred:"** that briefly states:
- The archetype you detected
- Whether you handled this as Case A (content baked in) or Case B (self-contained instruction)
- The key inferences you made about role, context, or scope
- Anything the user should correct if your inference was wrong

**Optional effort line.** For analytical or strategic keystone prompts, you may append one clause to the Inferred note recommending the effort dial: **high** for most, **xhigh** for the heaviest strategic work. On Opus 4.8 that dial (next to the model selector) is also the thinking control — never suggest a separate "thinking" toggle. Do not add a research / web-search recommendation here: that is friction flow doesn't need; if the prompt needs current information, the prompt itself handles it (Case B). Omit the effort line entirely for lean creative or one-shot prompts where the default is already right.

That's it. No change log. No diagnostic table. No ceiling check. The user is in flow — give them what they need and get out of the way.

If the user wants more detail (asking "why did you change X" or "what did you score it"), they can ask. The default is silent transformation.

## Constraints

- **Speed over rigor.** This is not the diagnostic skill. Do not produce nine-dimension scores. Do not produce change logs. Do not show your work unless asked.
- **No placeholders, no template variables.** The two hard rules are non-negotiable. If the prompt would need content the user hasn't supplied, handle it via Case B (instruct Claude to ask for the input, or phrase the task so the user supplies it in the next chat turn).
- **Aggressive inference, transparent flagging.** Make confident guesses and briefly note them in the "Inferred" paragraph. Do not interrogate the user with clarifying questions unless the input is genuinely incomprehensible (and even then, ask exactly one question, not five).
- **Preserve voice.** Domain language, terminology, framing — all preserved. The user will recognize their own voice in the optimized prompt, just sharper.
- **Default to depth.** Flow-mode users are usually starting analytical sessions. Optimize for unlocking Claude's depth, not for producing a tidy template.
- **No prefaces.** Do not say "Here's the optimized prompt" or "I've transformed your input into". Just the prompt, then the brief Inferred note.
- **Calibrate capability techniques.** Apply them where they earn their place, calibrated against the 4.7–4.8 baseline. Do not bolt anti-sycophancy permission onto a prompt asking for a recipe.
- **Be literal about scope.** State scope explicitly where the dictation left it implicit. 4.7 won't generalize silently.
- **Use examples only where voice or format preference was signaled.** Examples are expensive in tokens and over-constrain generic tasks. They earn their place when the user cares about *how* the output looks.
- **One response, not a conversation.** The whole transformation happens in the first response. The user can iterate from there if needed.
- **Effort line, not a settings block.** Flow may suggest an effort level (one clause, analytical/strategic prompts only) but never imports the rigorous counterpart's heavier run-settings machinery or a research-toggle recommendation — both break flow. Thinking is folded into effort; never suggest a separate thinking control.

## Failure modes to avoid

**BAD OUTPUT looks like:**
- Producing a prompt containing `[paste X here]`, `{variable}`, `<input>`, or any other template placeholder the user is expected to fill in
- Asking the user clarifying questions when the input is interpretable with reasonable inference
- Producing a diagnostic, scoring table, or change log (that's the other skill)
- Sanitizing the user's domain language into generic terminology
- Producing a prompt so heavily structured that it doesn't match the depth of the original input
- Adding capability activation techniques mechanically without regard to the task
- Long preambles before the prompt itself
- Treating dictation artifacts as content rather than noise

**The most common failures:**

1. **Placeholder dependency.** Producing a prompt that requires the user to fill in `[paste content here]` or `{topic}` template variables before it will work. This breaks the paste-immediately contract entirely. Either bake the user's actual content in (Case A) or write the prompt to elicit content at runtime (Case B). If you typed square brackets around a noun, that's the failure mode.
2. **Breaking flow with questions.** The user dictated for a reason — they wanted to keep moving. If you ask three clarifying questions, you've failed even if the eventual prompt is perfect.
3. **Diagnostic creep.** Slipping into the rigorous mode and showing scores or change logs. This skill is silent transformation. Save the analysis for the other skill.
4. **Voice flattening.** The user's "I'm thinking about whether to restructure my team's pricing model" becomes "Analyze pricing model restructuring options" — technically equivalent, but the voice is gone and the user no longer recognizes their own thought.
5. **Under-optimization out of caution.** Producing a thin prompt because the input was thin. The point of this skill is that the OUTPUT is rich even when the input is rough. Infer aggressively.
6. **Mechanical capability stacking.** Bolting all six capability techniques onto every prompt regardless of task fit. The 4.7–4.8 baseline has raised the floor — many techniques earn their place selectively, not by default.

## Evaluation criteria

A strong flow-mode optimization will:
- Appear in one response, fast, with no clarifying back-and-forth
- Contain zero placeholders or template variables — paste-ready as-is
- Preserve the user's voice and domain language
- Apply capability activation techniques calibrated to the inferred archetype and the 4.7–4.8 baseline
- State scope literally where the dictation left it implicit
- Include examples only where the user signaled voice or format preference
- Produce a prompt the user can paste immediately into a new Claude chat
- Surprise the user with how much depth was unlocked from how little input
- Make the user feel the skill is reading their mind, not interrogating them

The ultimate test: does the user paste the output and start their session immediately, without editing? If yes, the skill worked. If they need to fix it first, it didn't.

## Relationship to the rigorous skill

This skill and `prompt-optimizer` (the rigorous version) are siblings. Use this one when:
- The user is dictating or in flow
- The input is rough, messy, or unstructured
- Speed matters more than diagnostic transparency
- The user is starting a new session and wants to get going

Use `prompt-optimizer` when:
- The user has a polished prompt they want audited
- They want to see the diagnostic and change log
- They're optimizing a prompt for the library or for repeated use
- They have time and want rigor over speed

**Chain-shaped escalation.** If the dictated input is clearly chain-shaped — multi-deliverable, sequentially dependent, with natural checkpoint moments (e.g., "I want to do a full competitor analysis: market position, financials, product comparison, then a synthesis memo"; "draft a prompt that runs a thesis review and then a counter-thesis pass and then a final synthesis") — still produce a serviceable single-turn keystone, but append one line to the end of the Inferred paragraph: "This looks chain-shaped — consider running it through rigorous mode for an explicit architecture decision." This keeps flow's speed-first contract intact while keeping the escalation path visible. Do not import the architecture decision module into flow — that's diagnostic theater for this mission. A single sentence flag is enough.

Both skills produce state-of-the-art keystone prompts. They differ only in the workflow and the user's relationship to the input.
