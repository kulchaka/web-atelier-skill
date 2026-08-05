# web-atelier

A Claude Code skill that builds one premium, modern web page — landing,
portfolio, promo, or product page — through a gated pipeline instead of a
single "make it pretty" prompt.

The skill's core idea: **great pages come from the order of work, not from
bigger prompts.** It interviews you step by step, locks a design contract
before any code, builds static-first, adds motion as a separate directed
pass, and audits the result. Deep domain knowledge is delegated to
specialized skills it installs and orchestrates:

| Role | Skill | What it contributes |
|---|---|---|
| Design specification | [zanwei/design-dna](https://github.com/zanwei/design-dna) | Turns references into a quantified `design-dna.json` (tokens + style + effects) |
| Motion direction | [LottieFiles/motion-design-skill](https://github.com/LottieFiles/motion-design-skill) | Timing, easing, choreography, what NOT to animate |
| Animation code | [greensock/gsap-skills](https://github.com/greensock/gsap-skills) | Correct GSAP APIs, ScrollTrigger, cleanup, performance |
| 3D (optional) | [CloudAI-X/threejs-skills](https://github.com/CloudAI-X/threejs-skills) | Three.js scenes with LCP-safe init and disposal |
| Final polish (optional) | [AThevon/genjutsu](https://github.com/AThevon/genjutsu) | `/genjutsu:cast` pass on the weakest moments |

## Install

```bash
npx skills add kulchaka/web-atelier-skill -g -y
```

Or clone into your skills directory:

```bash
git clone git@github.com:kulchaka/web-atelier-skill.git ~/.claude/skills/web-atelier
```

The hard dependencies (design-dna, gsap-skills, motion-design-skill) are
checked and auto-installed by the skill itself on first run. threejs-skills
is installed only if your page actually needs 3D. Genjutsu is optional — if
the plugin is present it is used for polish, otherwise a manual audit runs.

## Usage

In Claude Code, inside your project (or an empty directory), ask for a page
in plain language:

```
Build me a stylish landing page for my lash studio
```

The skill triggers on requests like "stylish / modern / premium page",
"landing", "portfolio", "promo". Then it walks you through the pipeline:

### The pipeline

1. **Interview** — 8 questions, one at a time: brand, goal + primary CTA,
   audience, content, references, motion personality, 3D, stack. Answer
   "decide for me" anywhere and it decides. Everything already said in your
   first message is never re-asked.
2. **Design DNA** — your 2–4 references (or candidates it hunts online for
   you) become `design/design-dna.json`: palette, type scale, spacing,
   effects. You edit it by hand — it's the contract. **Gate: you approve.**
3. **Structure** — sections, hierarchy, one H1, grid plan, real copy.
   **Gate: you approve.**
4. **Static build** — the full page with zero animations. **Gate: it must
   look premium standing still — motion never rescues a weak static page.**
5. **Motion pass** — first a motion plan (purpose / trigger / duration /
   easing per element + a "does not animate" list), then GSAP
   implementation. **Gate: you approve the plan before any animation code.**
6. **3D** (only if justified) — lazy-init after LCP, static fallback,
   proper disposal.
7. **Audit** — genjutsu polish or manual audit against the contract, then a
   mandatory final pass: `prefers-reduced-motion`, LCP/CLS, keyboard
   navigation, responsive at 375/768/1440.

### Artifacts

The skill leaves three files in your project's `design/` directory — they
are the source of truth for every later change to the page:

- `design/atelier-brief.md` — the interview summary
- `design/design-dna.json` — the design contract (hand-edited)
- `design/motion-plan.md` — the motion contract

Re-use the same `design-dna.json` for the next page and you get brand
consistency for free.

## What it is NOT for

- Cinematic landings built around one hero video/image with an immersive
  scroll story — that's a different, narrower formula.
- Multi-page sites, routing, CMS integration.
- Pixel-perfect recreation of an existing mockup.

## Requirements

- [Claude Code](https://claude.com/claude-code) with skills support
- Node.js (for `npx skills add` auto-installs)
- Optional: `agent-browser` skill for automated screenshots at visual gates
