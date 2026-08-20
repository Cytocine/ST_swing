# SuperCloud Terminal

A single-page, installable PWA for an Alpaca-powered trading dashboard —
candlestick charts (1D/4H), indicators, and a watchlist scanner.

## Project structure

```
.
├── index.html                  # the app (was gemini-code-1787249244466.html)
├── manifest.json                # PWA manifest
├── sw.js                        # service worker (app-shell cache; live Alpaca calls always hit network)
└── icons/
    ├── icon.svg
    ├── icon-192.png
    ├── icon-512.png
    ├── icon-maskable-192.png
    └── icon-maskable-512.png
```

Your Alpaca API key/secret are entered in-app and stored in the browser's
`localStorage` — they are never bundled into these files or sent anywhere
except directly to Alpaca's API.

## 1. Push to GitHub

```bash
cd supercloud-terminal
git init
git add .
git commit -m "Initial commit: SuperCloud Terminal PWA"
git branch -M main
git remote add origin https://github.com/<your-username>/supercloud-terminal.git
git push -u origin main
```

## 2. Deploy on Cloudflare Pages

1. Go to the Cloudflare dashboard → **Workers & Pages** → **Create application** → **Pages** → **Connect to Git**.
2. Select the `supercloud-terminal` repo.
3. Build settings:
   - **Framework preset:** None
   - **Build command:** *(leave blank)*
   - **Build output directory:** `/`
4. Click **Save and Deploy**. Cloudflare serves the static files directly — no build step needed.
5. Every push to `main` auto-deploys. You'll get a `*.pages.dev` URL, and can attach a custom domain under **Custom domains** in the Pages project settings.

## 3. Install as an app

Once deployed over HTTPS (Cloudflare Pages does this by default):
- **Desktop Chrome/Edge:** address-bar install icon, or menu → "Install SuperCloud Terminal".
- **Android Chrome:** menu → "Add to Home screen" / "Install app".
- **iOS Safari:** Share → "Add to Home Screen" (this is what makes `apple-touch-icon` / `apple-mobile-web-app-*` meta tags matter — iOS doesn't read `manifest.json` for the icon).

## Notes on the service worker

- Precaches the app shell (`index.html`, `manifest.json`, icons) so the UI loads offline/instantly on repeat visits.
- Requests to `*.alpaca.markets` are always sent straight to the network — live quotes and account data are never cached.
- The `lightweight-charts` CDN script is cached on first load and revalidated in the background on subsequent loads.
- Bump `CACHE_NAME` in `sw.js` (e.g. `supercloud-terminal-v2`) whenever you ship a new version, so old caches are cleared and clients pick up the update.

## Local testing

Service workers require a secure context, so `file://` won't work. Serve locally with:

```bash
python3 -m http.server 8080
```

Then open `http://localhost:8080`.
