> **Historical document.** This is the original plan. It has been superseded: the storage
> layer moved from jsonblob.com (403 in browsers) to ntfy.sh, reveal became manual, and the design
> changed a lot. Current documentation: [ARCHITECTURE.md](ARCHITECTURE.md), [DESIGN.md](DESIGN.md).

# DEVils Poker — live estimation app (plan)

A tiny, no-build, no-server planning poker app using the DEVils Poker deck.
Goal: join via a link, vote, watch a "burning" progress bar fill as people
vote, see who agreed on what. Hosted for free off a personal GitHub account.

## Requirements (from conversation)

- Single simple React app, no build step.
- Landing action generates a shareable link at the top to join the same session.
- On join, each participant is auto-assigned a random devil name (no login).
- Progress bar at top, fire/burning visual, fills in real time as votes come in.
- Default expected session size = 4, but more people can join freely.
- When everyone has voted, reveal all votes and show what the team agreed on
  (consensus, or call out a split).
- Host it as a GitHub Gist, free, under the personal GitHub account.
- Keep it as simple as possible — this is a joke/April Fools tool, not a product.

## Architecture

- **Single `index.html` file.** React 18 UMD + ReactDOM 18 UMD + Babel Standalone,
  all from CDN (`unpkg.com`) via `<script>` tags — no npm, no bundler, JSX written
  directly in a `<script type="text/babel">` block.
- **No custom backend.** Shared session state (who joined, who voted) lives in a
  free public JSON store: **jsonblob.com** (`https://jsonblob.com/api/jsonBlob`).
  - `POST` with an initial JSON body creates a blob and returns its id (via the
    `Location` response header — **unverified**, see Risks below).
  - `GET /api/jsonBlob/<id>` reads it, `PUT /api/jsonBlob/<id>` overwrites it.
  - No auth, no signup — anyone with the id can read/write it. Fine for an
    internal joke tool, not fine for anything sensitive.
- **Polling, not sockets.** Client polls the blob every ~2s and diffs locally.
  "Real time" here means "every couple of seconds," which is plenty for a
  voting session.
- **Identity**: on first load in a session, generate a random `clientId`
  (`c` + random base36) and pick a random devil name, store both in
  `localStorage` keyed by session id so refreshing the page doesn't rename you
  or drop your vote.

## Data shape (the JSON blob)

```json
{
  "round": 1,
  "revealed": false,
  "participants": {
    "c8f3ak2p": { "name": "Belphegor", "vote": null }
  }
}
```

- Join = add `clientId` to `participants` with `vote: null`.
- Vote = set `participants[clientId].vote`.
- Auto-reveal when `votesCast === participantCount && participantCount > 0`.
- Manual "Reveal now" button too, for stragglers who won't vote.
- "New Round" clears all votes, sets `revealed: false`, `round += 1`, keeps
  participants.

## Progress bar semantics (my interpretation — confirm later)

Denominator = actual current participant count, not a hard-coded 4.
The "default 4" is just UI copy ("built for a coven of ~4, more welcome") —
the bar itself always reflects real joined vs. real voted, so it doesn't
jump weirdly when a 5th person joins mid-round. Flag if this isn't what was
meant.

## Deck (reuses the DEVils Poker captions, text-only — no illustrated art in
the app; the illustrated deck stays the print/poster artifact)

| Value | Caption | Tier |
|---|---|---|
| 0 | Already fixed it (lies) | ember |
| 1 | Copy-paste, then prayed | ember |
| 2 | Renamed a variable, deployed to prod | ember |
| 3 | Added a button, broke the one next to it | ember |
| 5 | New endpoint, zero tests, full YOLO | orange |
| 8 | Works on my machine (exorcism pending) | orange |
| 13 | Legacy code archaeology, bring holy water | crimson |
| 20 | Undocumented API, summon the vendor | crimson |
| 40 | Rewrite the monolith, sacrifice the sprint | hellfire |
| 100 | Migrate the DB. Live. On a Friday. | hellfire |
| ☕ | Need more info, send coffee | cursed |
| ? | No idea, ask the PO | cursed |

Tier colors carried over from the poster: ember `#f2a341`, orange `#e8611f`,
crimson `#d1272a`, hellfire `#ff2b3d`, cursed `#b06bd6`.

## Devil names pool (folklore/grimoire names, public domain, not tied to any
copyrighted character)

Beelzebub, Mephistopheles, Asmodeus, Belial, Astaroth, Abaddon, Baphomet,
Moloch, Paimon, Lucifer, Lilith, Mammon, Behemoth, Leviathan, Azazel,
Belphegor, Furfur, Andras, Malphas, Bael, Naamah, Xaphan, Vapula, Zagan.

## Hosting — tiny public repo + GitHub Pages (preferred)

More reliable than a gist + third-party renderer: GitHub serves it directly,
no dependency on gistpreview/githack staying up. Still free, same personal
account.

1. Create a new **public** repo on GitHub, e.g. `devils-poker`.
2. Add the single `index.html` app at the repo root, commit to `main`
   (via the web UI's "Add file" or a normal `git push` — either works, there's
   no build step).
3. Repo → **Settings → Pages**. Under "Build and deployment", set
   **Source: Deploy from a branch**, **Branch: `main`**, folder **`/ (root)`**,
   then **Save**.
4. GitHub builds it (takes ~30–60s, refresh the Pages settings page to see the
   green "Your site is live at…" banner once done). The URL is
   `https://<your-username>.github.io/devils-poker/`.
5. Every future push to `main` auto-redeploys the same URL — handy while
   iterating on the app.
6. Share link = that Pages URL + `?s=<jsonblob-id>` once a session is created.

### Fallback if a repo feels like too much ceremony

Paste `index.html` into a Gist (gist.github.com) and view it live via
`https://gistpreview.github.io/?<gist-id>` (a free third-party renderer that
fetches the gist's `index.html`). Faster to set up, but depends on that
third-party site staying up — GitHub Pages doesn't have that dependency.

## Open risks / things to verify before relying on this

- **Not yet verified**: whether jsonblob.com exposes the `Location` header to
  browser JS on a cross-origin POST (needed to learn the new blob's id right
  after creating it). If it doesn't, the create-session flow needs a fallback
  (e.g. read an id echoed in the response body, or switch to a store that
  returns the id in the JSON body instead of a header).
- jsonblob.com is a free third-party service with no SLA — fine for a one-off
  April Fools session, not for anything that must not go down or disappear.
- No auth on the blob: anyone with the link can read/overwrite session state.
  Acceptable for this use case; call it out if scope ever grows beyond a joke.

## Next steps when picked back up

1. Confirm the progress-bar denominator interpretation above.
2. Write the single `index.html` (React/Babel CDN, jsonblob polling, devil
   names, burning progress bar, reveal + consensus screen).
3. Spot-check the jsonblob `Location`-header assumption in a real browser
   before finishing the create-session flow.
4. Paste into a Gist, wire up gistpreview link, do an end-to-end test with
   2+ browser tabs acting as different participants.
