# Changelog — prompt-architect

Lean release log. Full design-journal rationale lives in the inline CHANGELOG section of `SKILL.md`.

## v2 — June 2026

Initial public release. The generative counterpart to `prompt-optimizer`: takes a described task/problem for which no prompt exists yet, classifies it against a 10-bucket taxonomy, makes an explicit output-architecture decision (single-turn vs. chained multi-stage), and produces a technique-grounded v1 draft — or, for chain verdicts, a full chain deliverable (README + Project orchestrator + per-stage prompts). v2's headline change over the private v1 is promoting the architecture decision to a first-class step rather than a single-turn-only output. Validated against Claude Opus 4.8 with no changes required. See the inline CHANGELOG in `SKILL.md` for the full rationale.

_(v1 was a private working version and was never released publicly.)_
