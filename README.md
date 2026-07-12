# Receipt Scanner

A simple, single-file receipt-reading web app. Vanilla JavaScript + Bootstrap 5, no build step — everything lives in `index.html`. Hosted as a static site on GitHub Pages. It takes a receipt photo (upload or camera) and uses **Claude vision** to list each item and its price in a copyable text box. Nothing is uploaded to any server of ours or saved anywhere.

**Live app:** <https://gagiiiii.github.io/receiptscanner/>

## Features

- **Two ways to add a receipt** — **Upload** an image file, or use the **Camera** (opens the rear camera on phones via `capture="environment"`).
- **Claude vision reading** — the image is sent directly from your browser to the Anthropic API, which reads receipts far more accurately than on-device OCR and returns clean `item — price` lines.
- **Editable result** — the output lands in a textarea you can tweak before copying.
- **One-tap copy** — copy the whole list to the clipboard.
- **Cost-aware** — images are downscaled to ~1600px in the browser before sending, keeping token usage (and cost) low.

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
