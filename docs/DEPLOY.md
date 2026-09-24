# Deploying to GitHub Pages (auto-deploy on every push)

Cost: free on a personal account. GitHub Pages on the Free plan needs a **public** repo.
The repo holds no secrets; the app is client-side code anyway.

## One-time setup

1. **Create the repo**
   - Web: github.com → **New repository** → name `devils-poker` → **Public**
     → leave "Add README / .gitignore / license" unchecked (they already exist) → Create.
   - Or with the GitHub CLI, from inside this folder:
     ```bash
     gh repo create devils-poker --public --source=. --remote=origin
     ```
2. **Set your git identity.** The initial commits use a placeholder author:
   ```bash
   git config user.name  "Your Name"
   git config user.email "you@example.com"
   git rebase -r --root --exec "git commit --amend --reset-author --no-edit"
   ```
   Skip this if you don't mind the placeholder. Nobody blames the coven.
3. **Enable Pages with Actions** *before* the first push:
   repo → **Settings → Pages → Build and deployment → Source: GitHub Actions**.
4. **Push**
   ```bash
   git remote add origin https://github.com/<your-username>/devils-poker.git   # skip if gh did it
   git push -u origin main
   ```
5. Watch **Actions → Deploy to GitHub Pages**. When it is green the site is at
   `https://<your-username>.github.io/devils-poker/`.

## After that

Every push to `main` that touches `src/` (or the workflow) redeploys automatically.
You can also run it by hand: **Actions → Deploy to GitHub Pages → Run workflow**.

## How the workflow works

`.github/workflows/pages.yml` checks out the repo, uploads the `src/` folder as the
Pages artifact and deploys it. There is no build step. Permissions are limited to
`contents: read`, `pages: write` and `id-token: write`.

## Troubleshooting

| Symptom | Fix |
|---|---|
| Workflow fails at "Setup Pages" or "Deploy" | Pages source isn't set to **GitHub Actions** (step 3). Set it, then re-run the workflow. |
| Site is a 404 | Wait a minute and check the deploy job finished. The URL must include the repo name. |
| "Can't reach ntfy.sh" in the app | Ad blocker, corporate proxy or an ntfy outage. Try another network. |
| Fonts or React don't load | Blocked CDNs (unpkg, Google Fonts). Same suspects as above. |
| Music button does nothing | Spotify's script is blocked, or the browser blocked autoplay: press play in the corner player. |
| Private repo | Pages for private repos needs a paid plan, and the published site is public either way. |

## Custom domain (optional)

Settings → Pages → Custom domain. Add the DNS records GitHub lists, then enable "Enforce HTTPS".
