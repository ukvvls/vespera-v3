# VESPERA V3 — Player System

A fresh, responsive Arabic RTL player dashboard with a more transparent Liquid Glass design, daily quests, XP/ranks, a separate optional fitness module, local data storage, a local backup console, and an optional Cloudflare Workers AI assistant.

## Included files

- `index.html` — player website (no player login).
- `admin.html` — local backup/import/export tools for the current browser only.
- `config.example.js` — template for the Cloudflare Worker URL.
- `cloudflare-worker/worker.js` — AI endpoint; API credentials are not placed in the browser.
- `cloudflare-worker/wrangler.jsonc` — Workers AI binding configuration.

## What works immediately

- Add preset and custom daily tasks.
- Complete/reopen/delete tasks.
- XP totals, player ranks, level, and weekly activity grid.
- Player display name.
- Optional FITNESS section, off by default. Its tasks remain separate from daily quests.
- Fitness profile fields (height, weight, age) and a numeric BMI calculation only; no weight classification, calorie target, or diet prescription.
- Weekly activity schedule and performance log for push-ups, pull-ups, plank, and running distance.
- Local backup export/import through `admin.html`.

Player and fitness data are saved in the browser's `localStorage` on that device. They do not sync between devices and are not visible to a central admin account.

## Publish the website on GitHub Pages

1. Create a new repository named `vespera-v3`.
2. Upload `index.html`, `admin.html`, and `config.example.js` to the repository root.
3. In GitHub: Settings → Pages → Deploy from a branch → `main` → `/(root)`.
4. Open the published Pages URL. The website works without an account or login.

## Connect VESPERA AI (Cloudflare Workers AI)

The AI screen is included, but cloud replies are not active until you deploy the Worker and set its URL. The website never contains a Cloudflare API token.

### Deploy from Cloudflare dashboard / Wrangler

1. Create a Cloudflare Worker project using the files in `cloudflare-worker/` (the easiest reproducible route is Wrangler from a computer with Node.js installed).
2. Keep `wrangler.jsonc` and `worker.js` together in the Worker project folder.
3. In `wrangler.jsonc`, replace `https://YOUR-GITHUB-USERNAME.github.io` with the exact origin of your GitHub Pages site (origin only; no repository path).
4. Deploy the Worker with Wrangler. Its `AI` binding is defined in the config and uses the Workers AI model `@cf/zai-org/glm-4.7-flash`.
5. Copy the Worker URL (for example `https://vespera-v3-ai.YOUR-SUBDOMAIN.workers.dev`).
6. Copy `config.example.js` to a new file named `config.js`, then replace `AI_WORKER_URL` with your Worker URL.
7. Upload `config.js` to the website repository root. It contains only the public Worker URL, not a secret key.
8. Reload the GitHub Pages website and open VESPERA AI.

Example Wrangler commands (run inside the Worker folder):

```bash
npx wrangler deploy
```

### Important AI usage notes

- Cloudflare Workers AI documents a free allocation of 10,000 Neurons per day; the quota resets daily at 00:00 UTC. Usage beyond the free allocation requires a paid Workers plan. Check the current Cloudflare pricing page before enabling billing.
- The free allocation is shared across the account's Workers AI usage, not reserved just for VESPERA.
- A public Worker URL can be called by anyone who discovers it. The Worker checks the browser origin and limits request size, but CORS is not authentication and can be bypassed by non-browser clients. Before sharing the site publicly, add a Cloudflare Rate Limiting rule for the Worker endpoint and monitor AI usage in the Cloudflare dashboard.
- If the free quota is exhausted or the model is unavailable, the AI chat will show a connection error; the rest of VESPERA keeps working.

## Admin limitation

`admin.html` is a local data utility, not a secure multi-user administration backend. Since player records are stored only on each visitor's device and there is no player sign-in, a central admin cannot inspect every player's data. Centralized user management would require a database, authentication, and access policies as a separate future phase.

## Privacy

- Daily tasks, player name, fitness settings, schedule, and performance logs stay in local browser storage.
- The AI request sends only the typed message and a small summary of daily task titles/status and XP. Height, weight, age, and exercise logs are not sent to the AI Worker.
- Do not put API tokens, service-role keys, or private credentials in frontend files.
