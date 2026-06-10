# Changelog — investment-council

Lean release log. The full, rationale-rich design journal lives in the `## CHANGELOG`
section inside `SKILL.md`; this file is the headline-level release record.

## v1.2 — June 2026

First public release. A council of five orthogonal advisors — Bear, Bull, Base Rate
Skeptic, Market Structure Analyst, Risk Manager — that pressure-tests an investment
thesis, trade, position, or mispricing claim and returns a conviction-tiered verdict
tuned to DECISION (take/size it) vs. ANALYSIS (is it mispriced). This release adds a
degraded-run rule (when live verification is unavailable, load-bearing facts are flagged
memory-not-verified and conviction is capped below FAT PITCH), recalibrates the synthesis
reasoning note for Opus 4.8, and adds a length-pressure safeguard that protects advisor
depth and synthesis over the HTML report. Architecture is unchanged — single-run by
design. v1.0 and v1.1 were private working versions and were never released publicly. See
the inline `## CHANGELOG` in `SKILL.md` for the full per-version rationale.
