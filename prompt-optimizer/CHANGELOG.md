# Changelog — prompt-optimizer

All notable changes to this skill. Each entry is a summary; full rationale and
per-change design notes live in the inline CHANGELOG section of [`SKILL.md`](./SKILL.md).

## v4.2 — June 2026
Recalibration for Claude Opus 4.8. Capability-activation guidance retuned to 4.8's
behavior (better tool triggering, more reliable reasoning-effort calibration, improved
honesty), with anti-sycophancy held deliberately steady. Adds a RUN SETTINGS
recommendation — the optimizer now returns suggested EFFORT and RESEARCH settings
alongside the optimized prompt. No workflow or architecture changes.

## v4.1 — May 2026
Three surgical calibration additions grounded in Anthropic's official prompting guide:
`<examples>` elevated to a first-class structural tool, a quote-grounding pattern for
long-document tasks, and scope literalism added to the capability-activation note.
No structural changes.

## v4 — May 2026
Introduces the architecture decision as a first-class step. The optimizer now decides
single-turn vs. chained multi-stage output upstream (new Step 3), governed by seven
named signals, and produces one of two deliverables: a refined single-turn template
(with a new SINGLE-TURN (CEILING) sub-band) or a chain template (README + Project
orchestrator + per-stage prompts).

## v3 — April 2026
Fixes three failure modes surfaced by a 20-prompt A/B test: signal preservation
(new Step 5.5), an honest feasibility pass allowed to cut as well as extend, and
calibrated capability activation tied to actual source material in the prompt.
