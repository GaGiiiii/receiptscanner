# Receipt Scanner

A simple, single-file receipt-reading web app. Vanilla JavaScript + Bootstrap 5, no build step — everything lives in `index.html`. Hosted as a static site on GitHub Pages. It takes a receipt photo (upload or camera), reads it **entirely in your browser** with OCR, and lists each item and its price in a copyable text box. Nothing is uploaded or saved.

**Live app:** <https://gagiiiii.github.io/receiptscanner/>

## Features

- **Two ways to add a receipt** — **Upload** an image file, or use the **Camera** (opens the rear camera on phones via `capture="environment"`).
- **On-device OCR** — text recognition runs 100% in the browser with [Tesseract.js](https://tesseract.projectnaptha.com/); no server, no API key, no account.
- **Item + price parsing** — each detected line is split into an aligned `item        price` list. Handles both `12.99` and `12,99` (and thousands separators).
- **Editable result** — the output lands in a textarea you can tweak before copying.
- **One-tap copy** — copy the whole list to the clipboard.
- **Raw text toggle** — switch between the parsed list and the full raw OCR text, in case parsing misses a line.
- **Progress feedback** — a progress bar shows OCR status while the image is read.

## Privacy & storage

- **No persistence.** Images never leave your device and nothing is stored — no `localStorage`, no backend, no uploads.
- Everything happens client-side; closing the tab discards all data.

## How it works

1. Pick or snap a receipt → a preview appears.
2. Tap **Scan receipt** → Tesseract.js recognizes the text in-browser.
3. Each line ending in a price is turned into an `item — price` row; the rest becomes the raw-text view.

> **Accuracy note:** OCR works best on crisp, flat, well-lit receipts. Faded thermal paper, curled receipts, or tight columns can produce misreads — use the raw-text toggle and edit the box as needed.

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

- [Tesseract.js](https://tesseract.projectnaptha.com/) — in-browser OCR
- [Bootstrap 5](https://getbootstrap.com/) — layout
- [Font Awesome](https://fontawesome.com/) — icons
