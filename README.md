# Receipt Scanner

A simple, single-file receipt-reading web app. Vanilla JavaScript + Bootstrap 5, no build step — everything lives in `index.html`. Hosted as a static site on GitHub Pages. It takes a receipt photo (upload or camera), uses **Claude vision** to read it, and **matches each line against your personal item catalog** — printing only allowed items under your own custom names. Receipts themselves are never stored.

**Live app:** <https://gagiiiii.github.io/receiptscanner/>

## Features

- **Two ways to add a receipt** — **Upload** an image file, or use the **Camera** (opens the rear camera on phones via `capture="environment"`).
- **Claude vision + catalog matching** — the image *and your catalog* are sent to Claude, which reads each line and maps it to one of your canonical names semantically (e.g. `Orbit strong mints` and `Orbit mints` → `Orbit Bombone`), returning structured JSON.
- **Custom names & aliases** — you define the name each item should print as. Every scanned variant is learned as an alias, so future scans auto-match.
- **Output format** — matched items print as `Custom Name Weight - Price RSD`, e.g. `Jaffa Keks 150g - 200 RSD` (weight omitted when the receipt has none).
- **Reconcile unmatched** — anything not in your catalog is listed with a choice: match it to an existing item, or add it as a new one. Either way it's remembered.
- **Catalog manager** — view/rename/delete items and aliases, or add items manually.
- **Cross-device sync** — a sync code keeps your catalog live on phone and desktop (Firebase, same project as the travel app, separate `receiptcatalogs` collection).
- **Editable result + one-tap copy**, and images downscaled to ~1600px to keep token cost low.

## API key & privacy

- You supply your own **Anthropic API key** (from <https://console.anthropic.com>). Enter it in the app; the **Save** button stores it only in your browser's `localStorage` on this device.
- The key is **never** committed to this repo or sent anywhere except directly to `api.anthropic.com` when you scan.
- Because a static site can't hide a key, this is intended for **personal use**. To share the app publicly, put a small serverless proxy (e.g. a Cloudflare Worker) in front of the API so the key stays server-side.
- No receipts are stored — the image lives in the tab only until you close it.

## Cost

Uses **Claude Haiku 4.5** (`claude-haiku-4-5`), the cheapest capable vision model — roughly **$0.001–0.003 per receipt**, so ~300–1,000 scans per $1. For maximum accuracy, change `MODEL` in `index.html` to `claude-opus-4-8` (higher cost).

## How it works

1. Enter your API key (once — Save remembers it on this device).
2. Pick or snap a receipt → a preview appears.
3. Tap **Scan receipt** → the browser downscales the image, sends it to the Anthropic Messages API with `anthropic-dangerous-direct-browser-access: true`, and shows the returned item/price list.

## Hosting

Served via **GitHub Pages** from the `main` branch. On the free GitHub plan, Pages requires the repository to be **public**.

To enable: repo **Settings → Pages → Source: Deploy from a branch → `main` / `(root)`**.

## Running locally

It's a static file — open `index.html` directly in a browser, or serve the folder (recommended, so the camera & clipboard APIs behave):

```
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Tech

- [Anthropic Claude API](https://docs.claude.com/) — vision model reads the receipt
- [Bootstrap 5](https://getbootstrap.com/) — layout
- [Font Awesome](https://fontawesome.com/) — icons
