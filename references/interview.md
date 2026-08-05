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
   collect paths/URLs and verify each immediately (`ls` / `curl -sI`, GET
   fallback if HEAD is rejected — see SKILL.md rules). No →
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
- Motion personality: <chosen: confident | playful | elegant | minimal>
- 3D: yes/no — <one-line why>
- Stack: <detected or chosen>
```

End with a checkpoint: show the brief and confirm it before Phase 2.
