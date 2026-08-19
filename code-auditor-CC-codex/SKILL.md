---
name: fable-gpt
description: Cross-model coding workflow for a user who cannot read code. Use Codex (GPT-5.6-sol) as an ADVERSARIAL AUDITOR of code written by Fable/Opus — and later as an executor for heavy implementation. Invoke at the start of any coding session that should get an independent second-model review. Triggers include "fable-gpt", "audit this with Codex", "adversarial review", "have Codex check this", "cross-model review". Do NOT trigger outside Claude Code (requires the codex-plugin-cc plugin and an authenticated Codex CLI), for non-coding requests, or before any implementation exists to audit.
---

# Fable-GPT: Codex as adversarial auditor

## Why this exists
The user has **no coding experience and cannot read the code themselves**. A second, independently-trained model reviewing the code that Fable/Opus writes IS their code review. Different model lineage → different blind spots. Without it, every line ships on one model family's self-assessment. Treat the Codex audit as a required gate, not a nicety.

## Roles
- **Fable / Opus (you):** author, orchestrator, and *final* reviewer. You write the code, run the Codex audit, translate and verify the findings, fix, and re-audit. You own the final call.
- **Codex (GPT-5.6-sol):** the adversarial auditor (active role now). Later — an executor for heavy implementation via `/codex:rescue`.

## The audit workflow (primary — do this by default)

After completing any non-trivial implementation, **before telling the user it's done**, get an adversarial Codex audit. The user cannot read code, so this is a required gate, not optional.

### Default path — the plugin's adversarial review (works today, no install)
The `openai/codex-plugin-cc` plugin is installed and Codex is authenticated, but the standalone `codex` command is **not** on the system PATH — so drive audits through the plugin, which carries its own working Codex. When an implementation is complete, prompt the user to type this in the normal Claude Code input box (it's just a command, no terminal needed):

```
/codex:adversarial-review
```
**Real flags (verified against codex-plugin-cc 1.0.6 source):** `--scope auto|working-tree|branch`, `--base <ref>`, `--wait|--background`, plus optional free-text focus (e.g. `/codex:adversarial-review --scope working-tree focus on the ledger math`). It's diff-aware and returns structured findings.

**There is NO `--effort` flag on the review path.** `--effort` exists only on the `task` subcommand. The review parser (`valueOptions: ["base","scope","model","cwd"]`) does not recognize it, and unknown flags are pushed into positionals — so `--effort high` silently becomes the literal *focus text* `"--effort high"`, corrupting the review's steering while doing nothing to effort. **Never pass it.**

**Reasoning effort for reviews is set at the config layer only:** `model_reasoning_effort` in `~/.codex/config.toml` (currently `"high"`). The review inherits it; there is no per-invocation override.

Then ingest the output and continue with "Handling Codex's findings" below.

### Optional autonomous path — direct CLI (only if a bare `codex` works)
If the user later installs the standalone CLI (`npm i -g @openai/codex`, so a bare `codex` command exists on PATH), you can run the audit yourself without asking them to type anything:

```bash
codex exec -c 'model_reasoning_effort="high"' --sandbox read-only --skip-git-repo-check "You are a hostile, skeptical code reviewer. ASSUME this code contains bugs and prove it. Review the following files for: correctness bugs, security issues, unhandled edge cases, race conditions, wrong assumptions, and logic errors. Challenge the design choices. For every issue cite file:line and explain the concrete failure (input → wrong output/crash). Do NOT modify anything. Files: <list the changed files here>"
```
- `-c 'model_reasoning_effort="high"'` forces **high** effort for this run (model defaults to GPT-5.6-sol from `~/.codex/config.toml`); `--sandbox read-only` = never edits during a review; `--skip-git-repo-check` lets it run outside a git repo.
- Do NOT use `--profile audit` — this Codex build (0.144.x) requires profiles in a separate `<name>.config.toml` file; the inline `-c` override above is simpler and always works.
- **If `codex` isn't found on PATH, don't fall back to guessing — use the default plugin path above.**

## Handling Codex's findings (this is the important part for a non-coder)
Never paste raw Codex output at the user and never auto-accept it. For each finding:
1. **Translate** it into plain English — what breaks, when, and why it matters.
2. **Verify** it against the actual code yourself. Codex can be wrong or raise non-issues. Mark each finding **real bug / real risk / stylistic nit / false alarm**, and say *why* you agree or disagree.
3. **Triage & fix** the real ones. Show the user a short, ranked plain-English list: what you found, what you're changing, what you're leaving and why.
4. **Re-audit** after fixing (re-run Option A) until Codex surfaces nothing new that you judge real.
5. Only then report done — and state that the code passed an independent Codex audit at high effort.

## The delegation workflow (later — when the user wants heavier automation)
Once the user is ready to have Codex *implement*, not just review:
- Use `/codex:rescue` or the `codex:codex-rescue` subagent for heavy implementation, multi-file refactors, debugging, or test-fixing.
- Keep each Codex task **focused and specific**. Vague tasks produce vague code.
- **Inspect the result yourself before accepting it.** Do not blindly trust Codex output — the same audit discipline above applies to code Codex wrote.
- You stay the orchestrator: plan, decompose, delegate a focused unit, review, integrate.

## Guardrails & expectations
- **Read-only for audits, always** (`--sandbox read-only`). Never let a "review" edit files.
- **ChatGPT Plus has weekly Codex limits.** High-effort audits are cheap enough to run routinely; heavy executor use burns quota faster. If the user hits a limit, drop audit effort or batch reviews.
- **The "10 subagents / 60% token savings" framing from social posts is marketing** — real savings depend entirely on how much you delegate. Don't promise it.
- Diff-based reviews (`/codex:adversarial-review`) work best when the audited project is a **git repo**. If it isn't, use Option A with an explicit file list, or offer to `git init` first.

## Automatic seatbelt — the review gate (optional, recommended for the user)
`/codex:setup --enable-review-gate` turns on a lightweight automatic check: after any turn where Claude *edited code*, Codex runs a fast stop-gate review and returns ALLOW or BLOCK (with a one-line reason) before the task can finish. It only fires on code-edit turns (not status/setup turns), so it's low-noise and low-quota. It is NOT a deep audit — it catches blocking issues (second-order failures, empty-state, stale state, rollback risk). Treat it as a seatbelt; still run `/codex:adversarial-review` for real audits. Toggle off with `/codex:setup --disable-review-gate`.

## Command cheat-sheet
| Goal | Command |
|---|---|
| Adversarial audit (default, no install needed) | `/codex:adversarial-review [--scope auto\|working-tree\|branch] [--base <ref>] [focus text]` — **no `--effort` flag exists; effort comes from `~/.codex/config.toml`** |
| Autonomous audit (only if a bare `codex` is on PATH) | `codex exec -c 'model_reasoning_effort="high"' --sandbox read-only --skip-git-repo-check "<adversarial prompt> <files>"` |
| Standard review | `/codex:review` |
| Delegate implementation to Codex (later) | `/codex:rescue` (or `codex:codex-rescue` subagent) |
| Check / cancel running Codex jobs | `/codex:status` · `/codex:result` · `/codex:cancel` |
| Continue this session inside Codex | `/codex:transfer` |
| Verify plugin install / review gate | `/codex:setup` |

## Changelog

**v1.2 — Aug 2026 (public release)** — Initial public release. Standalone cross-model workflow: Claude (Fable/Opus) as author/orchestrator, Codex (GPT-5.6-sol) as adversarial auditor, closing the loop until Codex surfaces nothing new the author judges real. Default path drives the audit through the `codex-plugin-cc` plugin (no install, no PATH dependency); an optional direct-CLI path exists for anyone who installs the standalone `codex` binary. Documents a `--effort` flag trap on the review subcommand (silently swallowed into free-text focus rather than raising an error) and an optional low-quota automatic review-gate seatbelt for post-edit turns. v1.0–v1.1 were private working versions, never released publicly.
