# claude-skills

A small library of [Claude skills](https://www.anthropic.com/news/skills) I've built and validated for my own work, published openly.

Each skill is a self-contained `SKILL.md` file with YAML frontmatter that Claude loads on demand. Drop the folder into your skills directory and Claude will pick it up.

---

## Skills

### Prompting

| Skill | What it does |
|---|---|
| [`prompt-optimizer`](./prompt-optimizer) | Rigorous-mode prompt optimizer. Triages inputs (OUT OF PURVIEW / BORDERLINE / IN PURVIEW), decides single-turn vs. chained architecture, and rewrites IN PURVIEW prompts against a 9-dimension diagnostic. |
| [`prompt-optimizer-flow`](./prompt-optimizer-flow) | Flow-mode prompt optimizer. Turns rough, dictated, stream-of-consciousness input into a finished, paste-ready Claude keystone prompt in a single pass — no placeholders, no clarifying questions, no diagnostic theater. |
| [`prompt-architect`](./prompt-architect) | Generative counterpart to the optimizers. Classifies a task against a 10-bucket taxonomy, decides single-turn vs. chained architecture, and produces a technique-grounded v1 draft (or a full chain) when you have no prompt written yet. |
| [`prompt-optimizer-fable`](./prompt-optimizer-fable) | Fable 5 edition of the rigorous-mode optimizer. The same triage and single-turn-vs-chain architecture engine as `prompt-optimizer`, recalibrated for Claude Fable 5 — Fable effort ladder, surface-aware run settings, a merged Fable capability layer, and a reasoning-echo guard. Fires only on explicit Fable invocation. |
| [`prompt-architect-fable`](./prompt-architect-fable) | Fable 5 edition of the generative architect. The same 10-bucket taxonomy and architecture decision as `prompt-architect`, plus an interactive-vs-agentic execution-mode overlay and Fable-calibrated technique stacks. Fires only on explicit Fable invocation. |

### Investment

| Skill | What it does |
|---|---|
| [`business-decoder`](./business-decoder) | First-principles, plain-English company comprehension — the front-of-funnel lens applied before any valuation or thesis work. Decodes what a company does, how it makes money, who pays and why, and whether it's a good, mediocre, or fragile business, translating all domain jargon (biology, physics, engineering) into analogies a finance generalist gets. |
| [`investment-council`](./investment-council) | Pressure-tests an investment thesis, trade, or mispricing claim through five orthogonal advisors (Bear, Bull, Base Rate Skeptic, Market Structure Analyst, Risk Manager) who analyze independently, peer-review anonymously, and return a verdict tuned to decision (take/size it) vs. analysis (is it mispriced). Grounds price, options, and held-position context live via the IBKR (Interactive Brokers) connector when available. |
| [`investment-council-challenge`](./investment-council-challenge) | The challenge-round variant of `investment-council`. The same five advisors and blind peer review, plus a fourth audit layer: the chairman drafts a verdict, an adversarial challenger attacks it — starting with every "what did all five of us miss" item the peer review raised — and the chairman resolves or overrules each point before the verdict is final. Fires only on explicit invocation, so it never collides with the default council. |

### Claude Code

| Skill | What it does |
|---|---|
| [`code-auditor-cc-codex`](./code-auditor-cc-codex) | Routes every Claude Code implementation through Codex (GPT-5.6-sol) as an adversarial second-model auditor before it ships — required cross-model review for a user who can't read the code themselves. Claude Code only; needs the Codex CLI/plugin and a ChatGPT Plus account at minimum. |

---

## Installation

**Claude Desktop / Code:**

1. Clone or download this repo.
2. Copy the skill folder you want (e.g. `prompt-optimizer/`) into your Claude skills directory:
   - macOS/Linux: `~/.claude/skills/`
   - Windows: `%USERPROFILE%\.claude\skills\`
3. Restart Claude. The skill will load when its trigger conditions are met.

**Claude.ai (web):**

Skills can be uploaded to a Project. Open the skill folder, copy the contents of `SKILL.md`, and paste it into a new Project's custom instructions, or upload the file directly if your plan supports skill uploads.

**Note on `code-auditor-cc-codex`:** the steps above only place the file — this one has hard external dependencies the others don't. Claude Code only (not Desktop or claude.ai); requires the `codex-plugin-cc` plugin installed with Codex authenticated (or a standalone `codex` CLI on PATH), plus a ChatGPT Plus account for Codex access.

---

## Why these exist

### Prompting

#### prompt-optimizer
Most published prompt optimizers solve the wrong problem. They optimize for polished prompts when what matters is the quality of the output. And they're miscalibrated at both ends of the difficulty spectrum — over-engineering simple prompts and under-architecting complex ones, especially missing the case for staged, multi-prompt structures.

The `prompt-optimizer` skill here is the result of a long iteration cycle (A/B tested against alternatives, 80% pair-level win rate at v3, refined further in v4) that taught me prompt optimization is mostly about *triage*: deciding what to leave alone, what to lightly polish, and what to rebuild.

#### prompt-optimizer-flow
The rigorous optimizer above assumes you already have a written prompt and the time to audit it. Flow mode is the other half of the same problem: you're dictating, mid-thought, and you want to start a Claude session *now*. Most optimizers break that moment — they interrogate you with clarifying questions, or hand back a template full of `[fill this in]` placeholders that doesn't actually run. Flow mode does the opposite: it strips the dictation noise, infers the intent aggressively, preserves your own voice, and returns a finished, paste-ready keystone prompt in one response. The discipline is in what it refuses to do — no scoring, no change log, no questions — so the prompt lands before the thought is gone.

#### prompt-architect
The two optimizers above both assume a prompt already exists — flow mode drafts one fast, rigorous mode audits one you've written. prompt-architect is the step before either: you have a genuinely complex task and *no* prompt in mind. It runs in reverse — given the problem, it works out which prompting technique and which architecture the task actually calls for. It classifies the task against a 10-bucket taxonomy, then makes a call most optimizers never surface: should this be a single prompt at all, or a staged chain with handoffs and checkpoints? The output is a technique-grounded draft you can run as-is, or hand to prompt-optimizer for a rigorous audit — the generative front end to the same pipeline.

#### The Fable 5 variants
`prompt-optimizer-fable` and `prompt-architect-fable` are standalone forks of the rigorous optimizer and the architect, recalibrated for Claude Fable 5 rather than adapted at runtime. The triage, the 10-bucket taxonomy and the single-turn-vs-chain architecture decision are the same engine; what changes is everything downstream of the model: a Fable effort ladder surfaced in the run settings, a surface parameter (chat, code, agentic harness) because the same prompt wants different framing in each, a re-derived capability layer, and a guard against asking Fable 5 to echo its own reasoning. They fire **only** on explicit Fable invocation — "optimize this for Fable 5," not "optimize this" — so the Opus originals remain the default and nothing silently reroutes. The forks and their originals share one architecture-decision module and the originals deliberately keep the prior generation's constants: same module, different constants, by design rather than drift.

### Investment

#### business-decoder
Every valuation and every thesis rests on a prior question that usually goes unasked: do I actually understand this business? Not the multiples, not the model — the thing itself. What does it sell, to whom, how does the money arrive, and is this a good business or a fragile one wearing a good story. `business-decoder` is the front-of-funnel lens that answers that question before any number is touched. Its hardest discipline is *de-jargoning*: on a photonics or biotech name the memo is worthless if it leaves the reader nodding along to terms they can't actually explain, so every science or engineering term is translated into a faithful plain-English analogy with the precise term kept in parentheses to verify. It runs two modes — a deep, research-backed pass for obscure or technical names, and a fast single pass for well-covered ones — and ends with a deliberate baton-pass: a good / mediocre / fragile verdict and a steer toward valuation work or `investment-council`, never a buy/sell call of its own. The defence against its one real failure mode — laundering the investor deck back as analysis — is built in: a forced read of the Risk Factors, an independent source, and a mandatory honest-bear paragraph.

#### investment-council
A compelling investment thesis usually fails for one of two reasons, and the second is the dangerous one. The first is sycophancy: the analysis tells you what you want to hear. The second is *technical incompleteness* — the thesis sounds airtight because it never had to clear a base rate, name who is on the other side of the trade, account for its factor exposure, or size itself against the book it is joining. `investment-council` convenes five orthogonal advisors — Bear, Bull, Base Rate Skeptic, Market Structure Analyst, Risk Manager — who analyze independently, peer-review each other anonymously, and hand back a verdict tuned to the question you actually asked: should I take this position, or is this asset mispriced. It live-verifies the thesis's load-bearing facts before it convenes — the single place it most often earns its keep — and it is calibrated for an institutional book, not a retail buyer who needs protecting from their own enthusiasm. As of v1.7 it grounds its inputs in live data where the tools exist: with the Interactive Brokers (IBKR) connector available it reads the current quote, 52-week range, options surface, and your held-position and book overlap directly rather than from memory — and degrades gracefully to web-search verification when no connector is present, so the published skill stays account-agnostic.

#### investment-council-challenge
The default council has a measurable leak. Its peer-review step asks all five reviewers what the *entire* council missed — and nothing downstream is obliged to consume the answer. The synopsis is explicitly fenced from resolving it; the chairman's mandate is to resolve conflicts *between* advisors, which by construction cannot reach what none of them raised. Measured across four real runs, roughly twenty collective-miss items were raised and four reached a verdict — an ~80% drop rate on the council's most expensive output, including one reviewer's finding that a source dossier showed signs of fabricated precision, which if resolved would have discounted every number in the framed question.

`investment-council-challenge` closes that loop by splitting the chairman's synthesis into draft → challenge → final, all inside one turn. The chairman writes a verdict the reader never sees; a challenger receives only the delivered bundle — framed question, context block, five advisor responses, five peer reviews, the draft — and attacks it, with the collective-miss walk as its first mandated job. The chairman then does exactly one of three things with each point: re-task a named advisor for a bounded addendum, adjudicate it from material already in hand, or overrule it with stated reasoning. The overrule is load-bearing — the challenger audits, never authors, and a run that defers to it by default has failed.

Two choices cut against the obvious ones. The challenge is **conviction-symmetric**: it hunts under-rated conviction — a dropped structural floor, an under-priced asymmetry, a base rate more favourable than the Skeptic allowed — as hard as it hunts unearned conviction, because a council that can never reach high conviction is its own failure mode. And the top conviction tier stays reachable on a same-model challenge: barring it without a cross-model challenger would remove the tier outright in a chat context, so provenance is stamped honestly and never converted into a cap on what may be concluded.

It also carries two standing examinations the default council does not — insider flow and the issuer's own bid (gated, three-state, never silent), and a technical setup read (ungated, harvested from price bars the run already retrieves). Both exist because the top-tier gate names a *structural floor* among its conditions and nothing in the original instructed anyone to go and look for one: on a real run the chairman didn't merely omit it, he asserted no floor existed and spent that assertion capping conviction, while a buyback and a large open-market chief-executive purchase both existed at the time.

It installs alongside the default rather than replacing it — explicit invocation only, distinct output filenames, `investment-council` untouched — so both can be run against the same name and compared.

### Claude Code

#### code-auditor-cc-codex
Cross-model review for someone who can't read the diff themselves isn't optional, it's the whole safeguard. `code-auditor-cc-codex` routes every non-trivial Claude Code implementation through Codex (GPT-5.6-sol) as a hostile, independently-trained second reviewer — different model lineage, different blind spots — before it's allowed to ship. In practice it turns implementation into a loop between two harnesses: Claude writes and orchestrates, Codex tears the diff apart, Claude translates and triages each finding into real bug / real risk / stylistic nit / false alarm, fixes what's real, and re-runs the audit — resolving only once Codex stops surfacing anything new. It's the one skill here with hard external dependencies: Claude Code itself, the `codex-plugin-cc` plugin (or a standalone Codex CLI), and a ChatGPT Plus account for Codex access.

### More skills coming soon
The other skills in this repo will follow the same pattern — built for real work, validated before publishing.

---

## Maintenance and updates

Each skill is subject to periodic maintenance reviews and updates, as more capable models are released. Please see each skill's Changelog for details. Current skills are optimized for Claude Opus 5 and Claude Fable 5; `code-auditor-cc-codex` additionally targets Codex (GPT-5.6-sol) as its audit model.

---

## License

MIT. Use them, fork them, modify them, ship them in your own tools. Attribution appreciated but not required.

---

## Contact

[@bizarcoin](https://x.com/bizarcoin) on X.
