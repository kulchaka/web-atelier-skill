---
name: web-atelier
description: Use when the user wants a stylish, modern, premium web page — landing, portfolio, promo, product page, or a new section of an existing site — built via a gated pipeline: step-by-step developer interview, design-dna.json contract from references, static-first build, GSAP motion pass, optional Three.js 3D, final audit. Not for cinematic single-asset landings built around one hero video/image or an immersive scroll story (use premium-landing), multi-page sites, or pixel-perfect mockup recreation.
---

# Web Atelier

Builds one premium web page through a gated pipeline. The value is the order
of work: interview → design contract → static → motion → (3D) → audit.
Deep domain knowledge lives in external skills (design-dna, gsap-skills,
motion-design-skill, threejs-skills) — this skill orchestrates them and
holds the gates. Work through the phases in order; never skip a gate.

Rules for every phase:

- Never re-ask what the developer already said — state the captured value in
  one line and move on.
- "Decide for me" is honored on every question: decide, state the choice in
  one line, continue.
- Each phase ends with a short checkpoint ("continue?") unless it defines a
  stronger approval gate.
- Verify every provided path (`ls`) or URL the moment it is given:
  `curl -sI <url>`, and if HEAD is rejected (403/405), retry with
  `curl -s -o /dev/null -w "%{http_code}" -L <url>`. On failure ask for a
  replacement — never proceed on a broken reference.
- Runtime artifacts live in the target project's `design/` directory and are
  referenced by path in every later prompt: `design/atelier-brief.md`,
  `design/design-dna.json`, `design/motion-plan.md`.

## Phase 0 — Dependencies

Check installed skills:

```bash
ls ~/.claude/skills/ ./.claude/skills/ 2>/dev/null | grep -iE 'design-dna|gsap|motion-design' || true
```

Empty output means none installed. Install whichever of the three is missing
(these are hard dependencies) — `-g` installs user-level, `-y` skips
interactive prompts:

```bash
npx skills add zanwei/design-dna -g -y
npx skills add https://github.com/greensock/gsap-skills -g -y
npx skills add LottieFiles/motion-design-skill -g -y
```

If an install command fails, print it for the developer to run manually and
stop. Do not proceed degraded on hard dependencies.

- `threejs-skills` is NOT installed here — only at the start of Phase 6, and
  only if the interview confirmed 3D (context hygiene).
- `genjutsu` (plugin): detect only, via the plugin cache —
  `ls ~/.claude/plugins/cache 2>/dev/null | grep -i genjutsu || true` (plugin slash
  commands do not appear in the skills list). Present → Phase 7 runs
  `/genjutsu:cast`. Absent → manual audit prompt instead. Never block on it.
- `agent-browser`: soft dependency. Present → screenshots at visual gates.
  Absent → the developer verifies visually; say so once and continue.

## Phase 1 — Interview

Read `references/interview.md` and run the eight-question interview exactly
as specified there: one question per message, AskUserQuestion for
option-based questions, open-ended ones conversational. Write the summary to
`design/atelier-brief.md` and confirm it with the developer before Phase 2.

## Phase 2 — References → Design DNA

- Developer provided references → verify each, then continue.
- No references → read `references/reference-hunt.md` and hunt candidates
  online; the developer picks 2–4.

Then read `references/dna-contract.md` and follow it: run the `design-dna`
skill over the chosen references, walk the developer through the mandatory
manual edit, and save the result as `design/design-dna.json`.

**GATE: the developer approves `design/design-dna.json` before any
structure or code.**

## Phase 3 — Structure & content plan

Propose the page structure from the brief and the DNA — no code:

- Sections in order, with the hierarchy explicit: what the visitor must see
  first, second, third.
- Exactly one H1. One primary CTA, visible without scrolling.
- Grid plan per section (columns, breakpoints).
- Real content slotted into each section — provided texts, or drafted copy
  marked DRAFT. Never lorem ipsum.

**GATE: the developer approves the structure.**

## Phase 4 — Static build

Generate the page with ZERO animations, applying ALL tokens from
`design/design-dna.json` — type scale, spacing, colors, radii, shadows.
Semantic HTML or components per the stack from the brief. Mobile-first.

Then run the "Static checklist" from `references/audit.md`. Screenshot via
agent-browser at 375px and 1440px if available.

**GATE: the page must look premium fully static. If it does not, fix it
here — motion never rescues a weak static page.**

## Phase 5 — Motion pass

Read `references/motion-pass.md` and follow both sub-steps in order:

1. Motion plan (no code) → save `design/motion-plan.md` →
   **GATE: developer approves the plan.**
2. GSAP implementation per the plan, using the prompt shape and
   non-negotiables from the reference file.

## Phase 6 — 3D (conditional)

Runs only if the brief says yes to 3D — and 3D must communicate something
about the product (product showcase, meaningful spatial hero), never
decoration. If confirmed:

1. Install now: `npx skills add https://github.com/CloudAI-X/threejs-skills -g -y`
   (same failure rule as Phase 0).
2. Implement with these requirements stated verbatim in the prompt:
   lazy-init after LCP, pause when the tab is hidden, cap pixelRatio at 2,
   dispose geometries/materials on unmount, static gradient fallback when
   WebGL is unavailable. React project → React Three Fiber.

## Phase 7 — Polish + audit

1. Polish: genjutsu present → `/genjutsu:cast` on the 1–2 weakest moments of
   the page. Absent → the manual audit prompt from `references/audit.md`.
2. Mandatory final pass: run the "Final pass" checklist from
   `references/audit.md` item by item. Fix, don't log-and-skip.
3. Report to the developer: what was built, where the three `design/`
   artifacts live, and the checklist results.
