# Changelog — fable-gpt

## v1.2 — August 2026

Initial public release. Cross-model adversarial-audit workflow for Claude Code: Codex (GPT-5.6-sol) reviews every non-trivial implementation Claude (Fable/Opus) writes, as a required gate for a user who can't read the code themselves. Default path runs through the `codex-plugin-cc` plugin (no install needed); an optional direct-CLI path and an automatic low-quota review-gate seatbelt are also documented. See the inline Changelog section in `SKILL.md` for full rationale. v1.0–v1.1 were private working versions, never released publicly.
