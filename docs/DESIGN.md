# Design notes

Visual reference lives in [`../design`](../design). The look is "estimation grimoire":
dark parchment, ember light, tarot-style cards.

## Palette and type

| Use | Value |
|---|---|
| Background | `#0b0402` with warm radial glows |
| Text | `#FFF2D8` (dimmer text `#d2b998`) |
| Amber tier | border `#bd6e1a`, accent `#ffaa2b` |
| Crimson tier | border `#a32228`, accent `#ff4d55` |
| Void tier | border `#7c2d9e`, accent `#c084fc` |

Fonts: **Pirata One** (title, card numbers, headlines), **MedievalSharp** (body),
**Cinzel** (small caps labels). The title uses Pirata One because the more ornate
blackletter that was tried first was unreadable.

## Cards

- Tiers: amber (0 to 8), crimson (13 to 100), void (☕ and ?).
- Anatomy: dashed inner border, corner brackets, number top-left, rune label top-right,
  line-art illustration, caption.
- Selection: the card lifts and glows in **its own tier colour**. Selection is also
  exposed as `aria-pressed`.
- Illustrations are inline SVG in the `ART` map, normalised to the same visual size
  (content is about 69 % of the viewBox). Cards 40, 100, ☕ and ? come from the second design
  pass and have small looping animations (`.anim-*` classes at the end of the CSS).
- Captions reserve about four lines so all cards align, with `text-wrap: balance`.

## Layout

- Desktop: header, coven row, fire progress bar, then two rows of six cards. The deck width is
  derived from the viewport height (`--h` in the CSS) so everything fits one screen where possible.
- Tablet: 3 columns. Phone: 2 columns.
- Result screen is centred: verdict, one card per distinct vote, then *New round* and *Invite* side by side.
- All buttons share one size.

## Accessibility decisions

- Cards are real `<button>`s with a descriptive `aria-label` and `aria-pressed`.
- Voting state is written out in the coven chips ("voted" / "pondering…"), not just colour.
- Vote counts are in a polite live region; the progress bar has progress semantics.
- Visible focus rings; interactive targets are at least 24 px, main buttons 48 px tall.
- Small text is kept light enough to stay readable on the dark background; decorative
  labels hide on very narrow cards instead of shrinking below a readable size.
- `prefers-reduced-motion` switches off all animation and transitions.
- Parts of the reference design that did nothing (mute button, sprint ribbon) were left out on purpose.
