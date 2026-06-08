# Changelog — prompt-optimizer-flow

Lean release log. Full rationale for each version lives in the inline **CHANGELOG**
section of `SKILL.md` (the design journal); this file is the browse-the-folder summary.

## v3 — June 2026

Recalibration for Claude Opus 4.8. The Step 4 calibration note now spans 4.7–4.8;
reasoning activation splits natively-handled "more thinking" (the effort dial) from
prompt-supplied reasoning structure; and the output format gains an optional one-clause
effort-line recommendation for analytical/strategic prompts. The rigorous counterpart's
heavier run-settings machinery and research-toggle recommendation were deliberately not
imported — both break flow. No structural changes. Full rationale: inline CHANGELOG in
`SKILL.md`.

## v2 — May 2026

Five surgical additions: the two hard rules (no-placeholder rule + Case A / Case B
handling), a 4.7 calibration note on Step 4, scope literalism, examples-for-voice-or-format,
and a chain-shaped escalation flag. No structural changes. Full rationale: inline
CHANGELOG in `SKILL.md`.

## v1 — initial

First flow-mode optimizer: the lean five-step silent-transformation workflow. Private
working version; not separately detailed in the inline journal.
