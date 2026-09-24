# Design references

Source material the app was designed from. Not deployed.

| Path | What it is |
|---|---|
| `poster.html`, `canvas.json` | The original illustrated deck/poster (April 1st edition). Source of the card captions. |
| `grimoire-reference/` | The "estimation grimoire" UI reference: header, coven row, candle bar, card frames. |
| `cards-40plus-reference/` | Second pass with new illustrations and animations for cards 40, 100, ☕ and ?. |

The app in `../src/index.html` adapts these: it keeps the layout and card look, drops the fake controls,
restores the fire progress bar and uses Pirata One for readability.
