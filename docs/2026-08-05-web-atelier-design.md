# web-atelier — Design Spec

Date: 2026-08-05
Status: approved by Andrii (design review passed)

## Purpose

A Claude Code skill that builds a stylish, modern, premium-quality web page by
(1) extracting everything it needs from the developer through a step-by-step
brainstorming interview, then (2) orchestrating a pipeline of specialized
external skills — `design-dna`, `gsap-skills`, `motion-design-skill`,
`threejs-skills`, and optionally `genjutsu` — with explicit approval gates
between phases.

The skill's own value is the **process**: interview → design contract →
gated phases → audit. Deep domain knowledge (GSAP API correctness, Three.js
patterns, motion principles, DNA extraction) is delegated to the installed
external skills, activated by trigger words embedded in each phase's prompts.

## Relationship to existing skills

- `premium-landing` (already installed globally) stays as-is. It owns the
  narrow case: cinematic landing page built around ONE central video/image
  asset with one signature mechanic.
- `web-atelier` is the broader tool: any modern single web page — landing,
  portfolio, promo, product page, a section of an existing site.
- The frontmatter description must explicitly exclude: (a) the cinematic
  single-asset hero case (route to premium-landing), (b) multi-page sites.

## File layout

```
~/Documents/Dev/skills/web-atelier/    (dev location)
  SKILL.md               — phases, gates, orchestration rules
  references/
    interview.md         — interview question set: order, format, skip rules
    dna-contract.md      — design-dna.json schema expectations + manual-edit rules
    reference-hunt.md    — finding references online (Awwwards, Godly, Dark.design)
    motion-pass.md       — motion-plan template + GSAP implementation rules
    audit.md             — final checklist: performance, a11y, cleanup, responsive
```

Installed globally via symlink: `~/.claude/skills/web-atelier` →
`~/Documents/Dev/skills/web-atelier`.

## Triggering (frontmatter description)

Triggers: "stylish / modern / premium web page", "landing page", "portfolio
page", "promo page", "walk me through building a page", "use the design
skills pipeline". Ukrainian equivalents implied by context ("стильна
сторінка", "сучасний лендінг").

Anti-triggers (stated in description): cinematic hero-driven landing around
one central video/image asset → premium-landing; multi-page sites;
pixel-perfect mockup recreation.

## Pipeline — phases and gates

Every phase ends with a short checkpoint question ("продовжую?") unless the
phase explicitly defines a stronger approval gate. The developer's answers
from the original request are never re-asked — captured values are stated in
one line and the interview moves on.

### Phase 0 — Dependencies

- Check `~/.claude/skills/` (and plugin cache) for: `design-dna`,
  `gsap-skills` (installed name may be `gsap`), `motion-design-skill`.
- Missing → auto-install via `npx skills add <repo>`:
  - `npx skills add https://github.com/greensock/gsap-skills`
  - `npx skills add zanwei/design-dna`
  - `npx skills add LottieFiles/motion-design-skill`
- `threejs-skills` (`CloudAI-X/threejs-skills`) is installed ONLY if the
  interview later reveals 3D is wanted (context hygiene). Install happens at
  the start of Phase 6, not Phase 0.
- `genjutsu` (plugin, `AThevon/genjutsu`): detect only. Present → used in
  Phase 7 polish. Absent → manual audit prompt instead; never block on it.
- `agent-browser`: soft dependency for screenshots/verification. Absent →
  developer verifies visually.
- If `npx skills add` fails: print the exact command for manual execution
  and stop.

### Phase 1 — Interview

One question at a time. AskUserQuestion for option-based questions;
open-ended ones asked conversationally. "Decide for me" honored everywhere:
skill decides and states the choice in one line.

Question set (order):
1. Brand/product + tone (open-ended)
2. Page goal + primary CTA
3. Target audience
4. Content: real texts/images provided, or skill drafts placeholder-free copy
5. References: developer has 2–4 (screenshots/URLs), or skill hunts online
6. Motion personality: confident / playful / elegant / minimal
7. 3D: yes / no / decide-for-me (decide-for-me → 3D only if it communicates
   something about the product, never decoration)
8. Stack: auto-detect if run inside an existing project (Astro, Next, React,
   etc. — build within it); from scratch → Astro / React+Vite+Tailwind /
   developer-proposed stack

Verify any provided asset/reference immediately (`ls` for paths,
`curl -sI` for URLs); on failure ask for a replacement, never proceed on a
broken reference.

Output: `design/atelier-brief.md` — interview summary, the contract all
later phases reference.

### Phase 2 — References → Design DNA

- If developer has no references: search the web (WebSearch / agent-browser)
  for 3–5 candidate sites matching the described mood (Awwwards, Godly,
  Dark.design as starting points per `reference-hunt.md`), show them, the
  developer picks 2–4.
- Run the chosen references through the `design-dna` skill → full 3-dimension
  JSON profile (tokens + qualitative style + visual effects). Note conflicts
  between references.
- Show the JSON to the developer for manual editing — this is the contract.
- Save as `design/design-dna.json`.
- **GATE: developer approves the DNA before any structure/code.**

### Phase 3 — Structure & content plan

Sections, hierarchy (what the user sees first/second/third), one H1, clear
CTA, grid plan. No code. Real content slotted in (no lorem ipsum).
**GATE: approve structure.**

### Phase 4 — Static build

Generate the page with ZERO animations, applying ALL tokens from
design-dna.json. Semantic HTML / components per detected stack. Mobile-first.

Static checklist (from audit.md, static subset):
- Type scale respected (no ad-hoc sizes)
- Spacing consistent (multiples of base unit)
- Text contrast ≥ 4.5:1
- Looks right at 375px and 1440px

Screenshot via agent-browser if available. **GATE: the page must look
premium fully static — if not, fix before adding motion.**

### Phase 5 — Motion pass

Two sub-steps:
1. **Motion plan** (no code): per animated element — purpose, trigger,
   duration, easing, choreography order; motion personality from the brief;
   explicit list of what does NOT animate. Prompt phrased to trigger
   `motion-design-skill`. **GATE: approve motion plan.**
2. **Implementation with GSAP**: prompts phrased to trigger `gsap-skills` —
   gsap.timeline() for sequences, ScrollTrigger for scroll-driven sections,
   transform-only (x/y/scale/autoAlpha, never top/left/width), React →
   useGSAP() with cleanup, kill ScrollTriggers on route change.
- Save plan as `design/motion-plan.md`.

### Phase 6 — 3D (conditional)

Runs only if Phase 1 answered yes AND 3D communicates something (product
showcase, meaningful hero background). Installs `threejs-skills` now.
Requirements baked into the prompt: lazy-init after LCP, pause when tab
hidden, pixelRatio cap 2, dispose on unmount, static-gradient fallback
without WebGL, R3F wrapper in React projects.

### Phase 7 — Polish + audit

- Genjutsu present → `/genjutsu:cast` pass on weak spots.
- Absent → manual audit prompt: audit page against design-dna.json + motion
  plan, list every deviation, fix one by one.
- Then the mandatory final pass (audit.md):
  - prefers-reduced-motion: disable/simplify non-essential animation
  - transform+opacity only; will-change sparingly
  - LCP < 2.5s, CLS < 0.1; heavy JS must not block render
  - keyboard navigation + focus states survive animations
  - 375 / 768 / 1440 screenshots (agent-browser) or developer verification;
    touch targets ≥ 44px
  - all ScrollTriggers killed / Three.js resources disposed on route change

## Artifacts in the user's project

- `design/atelier-brief.md` — interview summary
- `design/design-dna.json` — design contract (edited by hand)
- `design/motion-plan.md` — motion contract

All later phases and the audit reference these files by path.

## Error handling

- Broken asset/reference URL or path → verify immediately, ask for
  replacement.
- `npx skills add` failure → print command for manual run, stop.
- No agent-browser → developer verifies visually at each visual gate.
- External skill not triggering (answer lacks its knowledge) → the phase
  prompts name things explicitly ("using GSAP ScrollTrigger…", "per
  design-dna.json…") to force trigger-word matches.

## Out of scope (YAGNI)

- Multi-page sites, routing, CMS integration.
- Lottie/dotLottie export workflows (motion-design-skill is used for
  principles, not Lottie tooling).
- Deployment.
