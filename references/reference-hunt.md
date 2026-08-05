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
3. Verify each candidate with `curl -sI` (expect 2xx/3xx; if HEAD is
   rejected with 403/405, retry `curl -s -o /dev/null -w "%{http_code}" -L`)
   before showing it.
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
