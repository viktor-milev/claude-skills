# claude-skills

A small library of [Claude skills](https://www.anthropic.com/news/skills) I've built and validated for my own work, published openly.

Each skill is a self-contained `SKILL.md` file with YAML frontmatter that Claude loads on demand. Drop the folder into your skills directory and Claude will pick it up.

---

## Skills

| Skill | What it does |
|---|---|
| [`prompt-optimizer`](./prompt-optimizer) | Rigorous-mode prompt optimizer. Triages inputs (OUT OF PURVIEW / BORDERLINE / IN PURVIEW), decides single-turn vs. chained architecture, and rewrites IN PURVIEW prompts against a 9-dimension diagnostic. |
| [`prompt-optimizer-flow`](./prompt-optimizer-flow) | Flow-mode prompt optimizer. Turns rough, dictated, stream-of-consciousness input into a finished, paste-ready Claude keystone prompt in a single pass — no placeholders, no clarifying questions, no diagnostic theater. |

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

---

## Why these exist

### prompt-optimizer
Most published prompt optimizers solve the wrong problem. They optimize for polished prompts when what matters is the quality of the output. And they're miscalibrated at both ends of the difficulty spectrum — over-engineering simple prompts and under-architecting complex ones, especially missing the case for staged, multi-prompt structures.

The `prompt-optimizer` skill here is the result of a long iteration cycle (A/B tested against alternatives, 80% pair-level win rate at v3, refined further in v4) that taught me prompt optimization is mostly about *triage*: deciding what to leave alone, what to lightly polish, and what to rebuild.

### prompt-optimizer-flow
The rigorous optimizer above assumes you already have a written prompt and the time to audit it. Flow mode is the other half of the same problem: you're dictating, mid-thought, and you want to start a Claude session *now*. Most optimizers break that moment — they interrogate you with clarifying questions, or hand back a template full of `[fill this in]` placeholders that doesn't actually run. Flow mode does the opposite: it strips the dictation noise, infers the intent aggressively, preserves your own voice, and returns a finished, paste-ready keystone prompt in one response. The discipline is in what it refuses to do — no scoring, no change log, no questions — so the prompt lands before the thought is gone.

### More skills coming soon
The other skills in this repo will follow the same pattern — built for real work, validated before publishing.

---

## Maintenance and updates

Each skill is subject to periodic maintenance reviews and updates, as more capable models are being released by Anthropic. Please see the Changelog for further information. Current skills are optimized for Opus 4.8

---

## License

MIT. Use them, fork them, modify them, ship them in your own tools. Attribution appreciated but not required.

---
