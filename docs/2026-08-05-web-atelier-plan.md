# web-atelier Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create the `web-atelier` Claude Code skill — a gated interview-driven pipeline that orchestrates design-dna, gsap-skills, motion-design-skill, threejs-skills and (optionally) genjutsu to build one premium web page.

**Architecture:** One orchestrator `SKILL.md` holding phases and approval gates, plus five `references/` files loaded per phase (interview, reference hunt, DNA contract, motion pass, audit). Deep domain knowledge is delegated to externally installed skills, activated by explicit trigger wording inside each phase's prompt templates.

**Tech Stack:** Markdown skill files only. No code, no build step. Verification via `grep`/`head` checks and a final skill-reviewer agent pass.

## Global Constraints

- All skill content in English (user rule: code/tech docs in English).
- Skill name exactly `web-atelier`; files live in `/Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/`; the symlink `~/.claude/skills/web-atelier` already exists — do not recreate it.
- Frontmatter has exactly two keys: `name`, `description`. Description must include both triggers and anti-triggers (premium-landing case, multi-page sites, mockup recreation).
- NO git commits: the directory is not a git repository and the user commits only on explicit request. Skip every commit step.
- Interview asks one question per message; option-based questions use AskUserQuestion; "Decide for me" honored everywhere.
- Artifacts the skill produces at runtime live in the target project's `design/` directory: `atelier-brief.md`, `design-dna.json`, `motion-plan.md`.
- Spec of record: `docs/superpowers/specs/2026-08-05-web-atelier-design.md`.

---

### Task 1: SKILL.md — orchestrator

**Files:**
- Create: `/Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/SKILL.md`

**Interfaces:**
- Consumes: nothing (first task).
- Produces: phase structure and the exact reference-file names (`references/interview.md`, `references/reference-hunt.md`, `references/dna-contract.md`, `references/motion-pass.md`, `references/audit.md`) that Tasks 2–6 must create verbatim.

- [ ] **Step 1: Write the file with exactly this content**

````markdown
---
name: web-atelier
description: Use when the user wants a stylish, modern, premium web page — landing, portfolio, promo, product page, or a new section of an existing site — built through a guided pipeline that interviews the developer step by step, derives a design-dna.json contract from references, builds static-first, adds a GSAP motion pass and optional Three.js 3D, then audits the result. Not for cinematic single-asset hero landings (use premium-landing), multi-page sites, or pixel-perfect mockup recreation.
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
- Verify every provided path (`ls`) or URL (`curl -sI`) the moment it is
  given; on failure ask for a replacement — never proceed on a broken
  reference.
- Runtime artifacts live in the target project's `design/` directory and are
  referenced by path in every later prompt: `design/atelier-brief.md`,
  `design/design-dna.json`, `design/motion-plan.md`.

## Phase 0 — Dependencies

Check installed skills:

```bash
ls ~/.claude/skills/ | grep -iE 'design-dna|gsap|motion-design'
```

Install whichever of the three is missing (these are hard dependencies):

```bash
npx skills add zanwei/design-dna
npx skills add https://github.com/greensock/gsap-skills
npx skills add LottieFiles/motion-design-skill
```

If an install command fails, print it for the developer to run manually and
stop. Do not proceed degraded on hard dependencies.

- `threejs-skills` is NOT installed here — only at the start of Phase 6, and
  only if the interview confirmed 3D (context hygiene).
- `genjutsu` (plugin): detect only —
  `ls ~/.claude/plugins/cache 2>/dev/null | grep -i genjutsu` and check the
  available-skills list for `genjutsu:cast`. Present → used in Phase 7.
  Absent → manual audit prompt instead. Never block on it.
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

1. Install now: `npx skills add https://github.com/CloudAI-X/threejs-skills`
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
````

- [ ] **Step 2: Verify frontmatter and phase structure**

Run:
```bash
head -5 /Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/SKILL.md
grep -c '^## Phase' /Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/SKILL.md
grep -o 'references/[a-z-]*\.md' /Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/SKILL.md | sort -u
```
Expected: frontmatter opens with `---` and `name: web-atelier`; phase count = 8 (Phases 0–7); referenced files are exactly `audit.md`, `dna-contract.md`, `interview.md`, `motion-pass.md`, `reference-hunt.md`.

---

### Task 2: references/interview.md

**Files:**
- Create: `/Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/references/interview.md`

**Interfaces:**
- Consumes: Phase 1 of SKILL.md points here by exact filename.
- Produces: the runtime artifact template `design/atelier-brief.md` whose field names Phase 2–7 prompts rely on (Motion personality, 3D, Stack).

- [ ] **Step 1: Write the file with exactly this content**

````markdown
# Interview

Run before any design or code. One question per message. Use AskUserQuestion
for the option-based questions (2, 6, 7, 8); ask the open-ended ones (1, 3,
4, 5) conversationally. Skip any question the original request already
answers — state the captured value in one line and move on. "Decide for me"
on any question: decide, state the choice in one line, continue.

Questions, in order:

1. **Brand / product** (open-ended): name, what it is, tone of voice.
2. **Page goal + primary CTA** (options): sell / capture leads / present
   portfolio / announce — and what the ONE primary action is. Exactly one
   primary CTA per page.
3. **Audience** (open-ended): who lands on this page, what they already
   know, and the main objection the page must overcome.
4. **Content** (open-ended): are real texts and images available? Yes →
   collect paths/URLs and verify each immediately (`ls` / `curl -sI`). No →
   this skill drafts real copy from the brief (never lorem ipsum) and marks
   every drafted block `DRAFT:` for the developer to replace.
5. **References** (open-ended): 2–4 screenshots or site URLs of ONE
   consistent style. None → Phase 2 hunts online per `reference-hunt.md`.
6. **Motion personality** (options): confident / playful / elegant /
   minimal. Drives all Phase 5 timing and easing choices.
7. **3D** (options): yes / no / decide for me. On "decide for me": choose 3D
   only if it communicates something about the product (product showcase,
   meaningful spatial hero) — never as decoration.
8. **Stack**: detect first — if run inside an existing project, read
   `package.json` and name the detected framework (Astro, Next, React,
   Vue…); build within it. From scratch → options: Astro /
   React + Vite + Tailwind / the developer proposes their own.

Output — write `design/atelier-brief.md`:

```markdown
# Atelier Brief — <brand>

- Product: <what it is>
- Tone: <voice>
- Goal / primary CTA: <goal> / <the one action>
- Audience & objection: <who> / <what must be overcome>
- Content: provided (<paths>) | drafted (DRAFT-marked)
- References: <urls/paths> | hunt online
- Motion personality: confident | playful | elegant | minimal
- 3D: yes/no — <one-line why>
- Stack: <detected or chosen>
```

End with a checkpoint: show the brief and confirm it before Phase 2.
````

- [ ] **Step 2: Verify structure**

Run:
```bash
grep -c '^[0-9]\.' /Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/references/interview.md
grep -o 'design/atelier-brief\.md' /Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/references/interview.md | head -1
```
Expected: 8 numbered questions; artifact path `design/atelier-brief.md` present.

---

### Task 3: references/reference-hunt.md

**Files:**
- Create: `/Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/references/reference-hunt.md`

**Interfaces:**
- Consumes: Phase 2 of SKILL.md points here when the developer has no references.
- Produces: a chosen set of 2–4 reference URLs consumed by `dna-contract.md`.

- [ ] **Step 1: Write the file with exactly this content**

````markdown
# Reference Hunt

Used in Phase 2 only when the developer has no references of their own.

1. Build 2–3 search queries from the brief, e.g.:
   - `<industry> <mood> award-winning website design`
   - `<style keyword> landing page site:awwwards.com`
   - godly.website and dark.design when the mood fits (dark, editorial,
     high-craft).
2. Use WebSearch (or agent-browser when screenshots are needed) to collect
   3–5 live candidate sites. Prefer real product/brand sites over gallery
   thumbnails — the DNA extraction needs the actual page, not a preview.
3. Verify each candidate with `curl -sI` (expect 2xx/3xx) before showing it.
4. Present the candidates, one line each: which stylistic idea it
   contributes (palette, typography, layout, motion character).
5. The developer picks 2–4 (AskUserQuestion, multiSelect). If the picks mix
   two incompatible styles, say so and ask them to narrow to one direction.
6. Hand the chosen set to the DNA step (`dna-contract.md`).

Rules:

- Never proceed with 0 references — DNA from nothing reproduces the
  AI-default look (purple gradients, Inter, glass cards).
- A single reference is allowed only if the developer insists; warn that the
  DNA will overfit to it.
````

- [ ] **Step 2: Verify structure**

Run:
```bash
grep -cE 'awwwards|godly|dark\.design' /Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/references/reference-hunt.md
```
Expected: ≥ 2 lines mentioning the galleries.

---

### Task 4: references/dna-contract.md

**Files:**
- Create: `/Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/references/dna-contract.md`

**Interfaces:**
- Consumes: chosen references from the interview or `reference-hunt.md`.
- Produces: `design/design-dna.json` — the contract consumed by Phases 3–7.

- [ ] **Step 1: Write the file with exactly this content**

````markdown
# Design DNA Contract

Phase 2 runs the `design-dna` skill over the chosen references. Prompt
shape (wording chosen to trigger the design-dna skill):

```
Analyze these reference designs into a complete Design DNA JSON profile.
References: <attached screenshots / URLs>
Cover all three dimensions: design system tokens, qualitative style, and
visual effects. Note any conflicts between references.
```

The returned JSON must cover three dimensions:

1. **Tokens** — palette with roles (bg / surface / text / accent), type
   scale (families, sizes, weights, line-heights), spacing scale (base unit
   + multiples), radii, shadows/elevation, motion tokens (durations,
   easings).
2. **Qualitative style** — mood, composition principles, density,
   photography/illustration direction, brand voice.
3. **Visual effects** — which effects the references use (glassmorphism,
   grain, gradients, scroll-driven reveals, WebGL/particles) and an explicit
   in/out decision for each on THIS page.

Mandatory manual-edit step — the JSON is a draft until the developer edits
it:

- Show the full JSON. Ask the developer to strike what doesn't fit the brand
  and fix the final colors and fonts. If a client brandbook exists, feed it
  alongside the references so the DNA reflects the real brand, not just
  borrowed aesthetics.
- Enforce while editing: exactly ONE accent color; one display family plus
  at most one body family; spacing scale derived from a single base unit.
- Resolve every conflict design-dna flagged between references — a conflict
  left in the file becomes a coin flip at build time.

Save the edited result as `design/design-dna.json`. Every later prompt
references it by path ("per design/design-dna.json") — this wording also
keeps the contract in context.

GATE: the developer approves the saved file before Phase 3.
````

- [ ] **Step 2: Verify structure**

Run:
```bash
grep -c 'design/design-dna\.json' /Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/references/dna-contract.md
grep -c 'ONE accent' /Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/references/dna-contract.md
```
Expected: artifact path appears ≥ 2 times; the one-accent rule is present.

---

### Task 5: references/motion-pass.md

**Files:**
- Create: `/Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/references/motion-pass.md`

**Interfaces:**
- Consumes: `design/atelier-brief.md` (motion personality), `design/design-dna.json` (motion tokens), the built static page from Phase 4.
- Produces: `design/motion-plan.md` and the implemented GSAP code; the plan is consumed by the Phase 7 audit.

- [ ] **Step 1: Write the file with exactly this content**

````markdown
# Motion Pass

Two sub-steps, in order. Never write animation code before the plan is
approved.

## 1. Motion plan (no code)

Prompt shape (wording chosen to trigger motion-design-skill):

```
Using motion design principles, create a motion plan for this page.
For each animated element specify: purpose (why it moves), trigger,
duration, easing, choreography order. Motion personality: <from
design/atelier-brief.md>. Flag anything that should NOT animate.
```

Write the plan to `design/motion-plan.md` as a table:

| Element | Purpose | Trigger | Duration | Easing | Order |
|---------|---------|---------|----------|--------|-------|

plus a "Does NOT animate" list below the table.

Rules of taste:

- Every row needs a purpose that is not "looks cool" — direct attention,
  explain a spatial relationship, confirm an action, or set the mood in the
  hero. No purpose → the element goes to the "Does NOT animate" list.
- The hero gets the richest moment; the rest of the page earns motion
  sparingly.
- Durations: micro-interactions 150–300 ms; reveals 400–800 ms; stagger
  steps 60–120 ms. Easing per the DNA's motion tokens.

**GATE: the developer approves `design/motion-plan.md`.**

## 2. Implementation with GSAP

Prompt shape (name the APIs explicitly to trigger gsap-skills):

```
Implement the motion plan (design/motion-plan.md) with GSAP.
- gsap.timeline() for sequences (no chained delays)
- ScrollTrigger for scroll-driven sections
- Transform props only (x, y, scale, autoAlpha) — never top/left/width
- React: useGSAP() from @gsap/react with proper cleanup
- SplitText for the hero headline if the plan calls for a text reveal
```

Non-negotiables to verify in the produced code:

- `gsap.registerPlugin(...)` before any plugin use.
- React/SPA: `useGSAP()` or `gsap.context()` with revert on unmount; all
  ScrollTriggers killed on route change.
- `autoAlpha` for fades (not bare `opacity`); `clearProps` where a tween
  must not leave inline styles behind.
- `prefers-reduced-motion` respected from the start — full handling is
  audited again in Phase 7.
````

- [ ] **Step 2: Verify structure**

Run:
```bash
grep -c 'design/motion-plan\.md' /Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/references/motion-pass.md
grep -cE 'useGSAP|ScrollTrigger|autoAlpha' /Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/references/motion-pass.md
```
Expected: plan path appears ≥ 3 times; GSAP trigger terms present on ≥ 4 lines.

---

### Task 6: references/audit.md

**Files:**
- Create: `/Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/references/audit.md`

**Interfaces:**
- Consumes: `design/design-dna.json`, `design/motion-plan.md`, the built page.
- Produces: the "Static checklist" used by Phase 4's gate and the "Final pass" + polish prompt used by Phase 7.

- [ ] **Step 1: Write the file with exactly this content**

````markdown
# Audit Checklists

## Static checklist (Phase 4 gate)

- [ ] Type scale from design/design-dna.json respected — no ad-hoc sizes
- [ ] Spacing consistent — multiples of the base unit only
- [ ] Text contrast ≥ 4.5:1
- [ ] Exactly one H1; primary CTA visible without scrolling
- [ ] Looks premium fully static at 375px and 1440px — if not, fix before
      any motion

## Polish step (Phase 7, before the final pass)

- Genjutsu installed → run `/genjutsu:cast <weak spot>` on the 1–2 weakest
  moments of the page.
- Not installed → manual audit prompt:

```
Audit this page against design/design-dna.json and design/motion-plan.md.
List every deviation: wrong tokens, inconsistent spacing, easing mismatches,
janky animations. Then fix them one by one.
```

## Final pass (Phase 7, mandatory)

Run item by item after polish. Fix, don't log-and-skip.

- [ ] `prefers-reduced-motion`: non-essential animation disabled or
      simplified
- [ ] Only `transform` + `opacity` animated; `will-change` used sparingly
- [ ] LCP < 2.5 s, CLS < 0.1; 3D/heavy JS does not block first render
- [ ] Keyboard navigation works; focus states survive animations
- [ ] 375 / 768 / 1440 verified — agent-browser screenshots if available,
      otherwise the developer checks; touch targets ≥ 44 px
- [ ] All ScrollTriggers killed and Three.js resources disposed on route
      change / unmount
````

- [ ] **Step 2: Verify structure**

Run:
```bash
grep -c '^\- \[ \]' /Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/references/audit.md
grep -c 'genjutsu:cast' /Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/references/audit.md
```
Expected: 11 checklist items total (5 static + 6 final); genjutsu polish present.

---

### Task 7: Review and validation

**Files:**
- Modify (if findings): any of the six files above.

**Interfaces:**
- Consumes: all six files from Tasks 1–6.
- Produces: a validated, installed skill.

- [ ] **Step 1: Confirm the skill resolves through the symlink**

Run:
```bash
ls -la ~/.claude/skills/web-atelier/ && ls ~/.claude/skills/web-atelier/references/
```
Expected: SKILL.md plus exactly 5 reference files: `audit.md`, `dna-contract.md`, `interview.md`, `motion-pass.md`, `reference-hunt.md`.

- [ ] **Step 2: Cross-file consistency check**

Run:
```bash
grep -rho 'design/[a-z-]*\.\(md\|json\)' /Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/ | sort -u
```
Expected: exactly three artifact paths — `design/atelier-brief.md`, `design/design-dna.json`, `design/motion-plan.md`. Any fourth spelling is a typo to fix.

- [ ] **Step 3: Run the skill-reviewer agent**

Dispatch the `plugin-dev:skill-reviewer` agent on `/Users/andriikulchytskyi/Documents/Dev/skills/web-atelier/` to review description triggering quality, structure, and progressive disclosure.

- [ ] **Step 4: Apply reviewer findings**

Fix real findings inline (Edit). Skip stylistic suggestions that conflict with the spec (e.g., the two-key frontmatter rule or the no-commit rule).

- [ ] **Step 5: Report completion**

Summarize to the developer: skill installed at `~/.claude/skills/web-atelier`, how to trigger it, and that the first real run (e.g., on a lashroom.cz page) is the true test.
