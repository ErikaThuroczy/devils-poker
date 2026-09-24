# Architecture

One static file, no build, no backend of our own.

```
 Browser A ─┐                                   ┌─ Browser B
            │  POST event    ┌──────────────┐   │
            ├──────────────▶ │  ntfy.sh     │ ◀─┤
            │                │  topic:      │   │
            ◀── SSE stream ──│  devils-     │──▶┤  SSE stream
                             │  poker-<sid> │
                             └──────────────┘
   every client replays the same ordered log  →  reduce()  →  same state
```

## Stack

- **React 18** and **ReactDOM** (UMD builds) and **Babel Standalone** from unpkg,
  compiling JSX in the browser. Pinned versions, see the `<script>` tags in `src/index.html`.
- **Google Fonts**: Pirata One (title, numbers, headlines), MedievalSharp (body), Cinzel (small labels).
- **ntfy.sh** as the shared message bus.
- **Spotify iFrame API**, loaded lazily, only after someone turns the music on.

## Why ntfy and not a JSON store

The first plan used jsonblob.com. In a browser it answers cross-origin requests
with `403` and no CORS headers, so it can't be used from a web page. ntfy is built
for browser use (`fetch` to publish, `EventSource` to subscribe), needs no signup,
and lets the client choose its own topic name, so there's no "create session" call.
An append-only log also removes the read-modify-write races a shared JSON blob has.

## Session and identity

- Session id: random string generated client-side, put in the URL as `?s=<id>`.
  Topic = `devils-poker-<id>`. The id is the only secret.
- Identity: `localStorage["devils:<sid>"] = { id, name }`. A refresh keeps your name
  and vote. `id` is random, `name` is a random devil from the pool in `NAMES`.

## Events

Every message body is JSON with a client-generated `eid` (used to de-duplicate
replays after reconnects).

| Event | Fields | Effect |
|---|---|---|
| `join` | `id`, `name` | Adds the participant if the id is new. Name clashes get " II" appended. |
| `vote` | `id`, `v`, `r` | Sets the vote if round `r` is current and not yet revealed. |
| `reveal` | `r` | Marks round `r` as revealed. |
| `new` | `r` | Starts round `r + 1`, clears votes, keeps participants. |
| `leave` | `id` | Removes a participant ("banish", for people who left the call). |

`reduce(events)` folds the log into `{ round, revealed, participants }`. Events for a
stale round are ignored, which makes double-clicks and simultaneous clicks harmless.

## Reveal rules

- Reveal is **manual** (anyone can press it). The button pulses when everyone present has voted.
  Auto-reveal was removed on purpose: people need time to change their minds and wait for teammates.
- The progress bar scales to `max(4, participants)`; `EXPECTED = 4` is the desired team size.
- The result screen groups identical votes into one card with names underneath.
  One group means *Agreed*; several means *No agreement yet* plus the numeric spread.

## Delivery details

- The client subscribes with `EventSource("https://ntfy.sh/<topic>/sse?since=all")`, which
  replays the cached history, then streams live. Reconnects replay again; `eid` dedupes.
- Ordering is the server's delivery order. Same-second ties are theoretically possible
  and practically harmless for this use case.

## Music

`useMusic()` in `src/index.html` injects Spotify's iFrame API on first toggle, creates a
compact embed controller for track `5uP1WWp6LRSNUNvmzANBOK` and shows it in a corner
panel. Toggle off pauses it. Anonymous listeners get a 30-second preview (Spotify limit).
The loop-on-end handler is untested against a real full-length playback.

## Limits

- ntfy free tier: roughly 12 hours of message retention and per-IP request limits.
- No authentication; anyone with the link can read and write the session.
- Browser-side JSX compilation makes first load slower than a built bundle.

## If you outgrow it

Swap the transport (`send()` and the `EventSource` effect in `Game`) for your own
backend or another pub/sub. `reduce()` and the UI don't care where events come from.
