# claude-skills

A small library of [Claude skills](https://www.anthropic.com/news/skills) I've built and validated for my own work, published openly.

Each skill is a self-contained `SKILL.md` file with YAML frontmatter that Claude loads on demand. Drop the folder into your skills directory and Claude will pick it up.

---

## Skills

| Skill | What it does |
|---|---|
| [`prompt-optimizer`](./prompt-optimizer) | Rigorous-mode prompt optimizer. Triages inputs (OUT OF PURVIEW / BORDERLINE / IN PURVIEW), decides single-turn vs. chained architecture, and rewrites IN PURVIEW prompts against a 9-dimension diagnostic. |

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

### More skills coming soon
The other skills in this repo will follow the same pattern — built for real work, validated before publishing.

---

## License

MIT. Use them, fork them, modify them, ship them in your own tools. Attribution appreciated but not required.

---

## Contact

[@bizarcoin](https://x.com/bizarcoin) on X.
