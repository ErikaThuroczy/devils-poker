# 🔥 DEVils Poker

> *Estimate at your own risk. No refunds, no story points held sacred.*

Planning poker for teams that have made peace with the fact that Scrum is not
coming back from this.

**No login. No install. No build step. No mercy.** Open a link, get assigned a
random devil name, pick a card, watch the progress bar catch fire, and find out
exactly how much your team disagrees about a ticket that says "small tweak".

---

## Why this exists

We adopted Scrum to become *agile*. Here is what we got instead:

- A **daily standup** that is a status meeting with worse posture.
- A **retrospective** that produces the same three sticky notes every two weeks
  ("communication", "scope creep", "more coffee") and a solemn vow to
  *definitely* do better, which is then filed under Backlog, where dreams go to
  be groomed.
- **Story points**, invented to stop us from estimating in hours, which every
  manager on Earth now converts back into hours. With a multiplier.
- **Velocity**, a number that measures how sad we are willing to look on a chart.
- A **Definition of Done** that is exactly as done as the last thing we called done.
- **Ceremonies.** The Agile Manifesto said *individuals and interactions over
  processes and tools*. We now have fourteen recurring meetings about the process.
  The individuals are not invited to most of them.
- **Fibonacci**, because linear numbers would have made it too obvious that we are
  guessing.

Waterfall took a decade to fail. Scrum failed in two-week increments, which
allowed us to fail *iteratively* and put it on a burndown chart. Progress!

So we did the only reasonable thing left: we made estimation a séance.
If the numbers are going to be made up anyway, they may as well be made up
by devils, in a dark room, next to a progress bar that is literally on fire.

Dark humor is the last remaining deliverable. It ships every sprint. It has never slipped.

## What it does

- **Start a session**, get a link, paste it into the team chat where it will be
  ignored for eleven minutes.
- **Everyone gets a random devil name** (Beelzebub, Mammon, Leviathan…). Anonymity
  for the estimation. Accountability for nothing.
- **Pick a card** from a 12-card deck of accurate engineering sentiment.
  Change your mind as often as you like until someone reveals.
- **Fire-themed progress bar** that scales to a coven of four (more can join) and
  grows flames as votes come in.
- **Reveal** shows one card per distinct vote, with the names underneath, and a
  verdict: *Agreed*, or *No agreement yet* with the spread called out, so the
  highest and lowest voters can explain themselves.
- **New round** wipes the votes and keeps the coven.
- **Optional music** (off by default, because we're not monsters): a music button
  in the corner plays *Spooky, Scary Skeletons* through Spotify's embedded player.

## The deck

| Card | Meaning |
|---|---|
| 0 | "Already fixed it" (lies) |
| 1 | Stack Overflow copy-paste, then prayed |
| 2 | Renamed one variable, deployed to prod |
| 3 | Added a button, broke the one next to it |
| 5 | New endpoint, zero tests, full YOLO |
| 8 | "Works on my machine" (exorcism pending) |
| 13 | Unlucky 13: legacy code archaeology, bring holy water |
| 20 | Undocumented third-party API, summon the vendor |
| 40 | Rewrite the monolith, sacrifice the sprint |
| 100 | This is fine. Migrate the database. Live. On a Friday. |
| ☕ | Send help and coffee, need more info |
| ? | No idea, ask the PO, flee the meeting |

## Run it locally

It is one HTML file. It needs the internet (React and fonts come from CDNs, votes
travel through ntfy.sh). Serve it, don't double-click it:

```bash
python3 -m http.server 8000 --directory src
# open http://localhost:8000 in two tabs to play against yourself.
# Yes, that's what a Scrum Master's Tuesday feels like.
```

## Deploy it

Push to `main` and GitHub Actions publishes `src/` to GitHub Pages.
One-time setup is in [docs/DEPLOY.md](docs/DEPLOY.md). It takes about as long as
a standup that was supposed to be fifteen minutes.

## How it works (the short, honest version)

There is no server. Every join, vote, reveal and new round is a tiny JSON event
posted to a public [ntfy.sh](https://ntfy.sh) topic named after the session id.
Every browser replays the same event log and arrives at the same state. Details in
[docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).

## Things that will go wrong (like your last three sprints)

- **Sessions live about 12 hours.** ntfy's free server forgets things, which puts
  it ahead of most wikis.
- **Anyone with the link can join.** The link *is* the password. Please don't
  estimate anything classified. Or anything that would make legal nervous.
- **Rate limits.** ntfy limits requests per IP. A big coven behind one office
  network may hit it. A big coven is also, historically, how Scrum fails.
- **Music.** Without a Spotify login in the same browser you get a 30-second
  preview. Autoplay blockers may make you press play. Consent, at last, in the workplace.
- **First paint is slowish** because JSX is compiled in the browser. This was a
  conscious trade-off: zero build tooling, zero `npm install`, zero node_modules
  the size of a small moon.

## Project layout

```
.
├── src/
│   └── index.html                 # the entire app (React + Babel via CDN)
├── docs/
│   ├── ARCHITECTURE.md            # event log, reducer, data flow, limits
│   ├── DESIGN.md                  # look & feel, tiers, accessibility decisions
│   ├── DEPLOY.md                  # GitHub Pages + Actions, step by step
│   └── PLAN.md                    # the original plan (historical, see note inside)
├── design/                        # reference designs and the original poster
├── .github/workflows/pages.yml    # auto-deploy on push to main
├── LICENSE
└── README.md
```

## FAQ

**Does this fix Scrum?** No.

**Does it make estimation more accurate?** No. It makes it more *honest*, which is worse.

**Can we use this for real?** It is an April Fools tool that escaped containment.
It works. Use accordingly. Your retrospective will say the same.

**Who is responsible for this?** The coven.

## License

MIT. See [LICENSE](LICENSE). Not affiliated with Scrum.org, Atlassian, or any
consultant who has ever said "let's take this offline".
The music is Andrew Gold's; it plays through Spotify's official embedded player.
