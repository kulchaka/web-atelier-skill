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
