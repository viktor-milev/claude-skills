Changelog — prompt-optimizer-fable

v1.0 — July 2026

First public release. A standalone Claude Fable 5 fork of the rigorous-mode
prompt-optimizer: the effort model is rewritten for the Fable 5 ladder (low / medium /
high / xhigh / max, high default, bidirectional), a surface parameter (chat / code /
cowork / harness) gates run settings and agentic techniques, the capability layer is
re-derived and merged into a single Fable 5 layer, a reasoning-echo guard is added, and
the single-turn-vs-chain architecture thresholds are recalibrated. Fires only on explicit
Fable invocation; the Opus original remains the default.

Full design rationale is in the inline CHANGELOG section of SKILL.md.
