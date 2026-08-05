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
