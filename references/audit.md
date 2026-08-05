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
- [ ] All ScrollTriggers killed on route change / unmount; Three.js
      resources disposed (only if 3D was built — otherwise N/A)
