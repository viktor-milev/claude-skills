# "Field Notes" — HTML report theme

Read this only when the user asks for an HTML report / something shareable. The Markdown memo
is always the primary artifact; HTML is on demand.

## Intent
A light, paper-like editorial look — deliberately the opposite of the dark themes in
`investment-council` (Slate & Ember). It should be
genuinely restful to *read* a sit-down memo in, and it plays on the spirit of the skill:
decoding a specimen from first principles, like an analyst's field notebook.

## Palette (warm, low-contrast-glare)
- Ground (page): warm ivory `#F6F2E9`
- Paper (insets): `#FCFAF3`
- Ink (body): warm near-black `#221F1A`
- Muted (meta/secondary): `#6B655A`
- Accent (the only color): muted moss-green `#5C6B4C`, used **only** as hairlines, the short
  tick under each section title, the BLUF left-border, small-caps labels, and links — never
  as a large filled block.
- Warning banner: muted ochre (`#F2E6C7` bg / `#C39A3F` border / `#5E4A12` ink).

## Type
- One unified clean sans for headings and body, chosen to read like the Claude chat surface.
  Claude's brand sans is the proprietary **Styrene** family, which cannot be embedded in a
  portable offline file — so the stack *names* Styrene as the lead preference and falls back,
  self-contained, to Inter, then a system sans (`ui-sans-serif, system-ui, -apple-system, …`).
  No serif. Headings are the same family, just heavier weight and larger.
- Meta / ticker / kicker / footer: monospace, small, letter-spaced, in moss for the kicker.

## Build
Use the bundled skeleton `assets/field-notes.html`. Replace the `{{TOKENS}}`:
- `{{COMPANY}}` — company name (+ ticker if you like).
- `{{META}}` — e.g. `NYSE: XYZ · ADR · Decoded 17 Jun 2026 · Mode: Deep · Lens: Hardware/deep-tech`.
- `{{WARNING_BLOCK}}` — a `<div class="warning"><b>⚠ Warning</b> …</div>` if sources were
  incomplete; otherwise the empty string (omit the banner entirely).
- `{{MEMO_BODY}}` — the memo's sections 1–10 as `<section><h2>…</h2><p>…</p></section>`
  blocks. Put the 30-Second Version in a `<div class="bluf">` with the anchor line in
  `<p class="anchor">`. Use `.verdict-tag` and `.handoff` in the verdict section.
- `{{FOOTER}}` — e.g. `business-decoder · sources as dated above · not investment advice, not a valuation`.

Change **no CSS**. Keep the file fully self-contained — no external fonts, scripts, or
assets. Save it to the working directory alongside the Markdown memo and present both.
Generous margins, ~720px column, squared 3px corners, calm spacing. The CSS already respects
print and narrow screens.
