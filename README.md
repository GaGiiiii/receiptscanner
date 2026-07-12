# Receipt Scanner

A single-file receipt-reading web app. Vanilla JavaScript + Bootstrap 5, no build step — everything lives in `index.html`. Hosted as a static site on GitHub Pages. It photographs a receipt, uses **Claude vision** to read it, **matches each line against your personal item catalog**, and prints only allowed items under your own custom names. Receipts themselves are never stored.

**Live app:** <https://gagiiiii.github.io/receiptscanner/>

## What it does

1. **Snap or upload** a receipt (the Camera button opens the rear camera on phones via `capture="environment"`).
2. The image **and your catalog** are sent directly from your browser to the Anthropic API. Claude reads each line and maps it to one of your canonical names — semantically, not just by exact text (e.g. `Orbit strong mints` and `Orbit mints` both → `Orbit Bombone`).
3. **Matched items** print in a copyable box; **unmatched items** go to a reconcile list you resolve once and forever.

## Output format

Matched items print as:

```
Custom Name weight - unitPrice RSD  (xN when quantity > 1)
```

Examples:

```
Jaffa Keks 150g - 200 RSD
Chipsy Chili 95g - 120 RSD x3
Orbit Bombone - 60 RSD
```

- **Custom name** — the name *you* chose for the item (Title Case).
- **weight** — read from the receipt, always lowercase units (`g`, `kg`, `ml`, `l`); omitted when the receipt has none.
- **unitPrice** — price of a *single* unit, **rounded up** to whole dinars (`619.99` → `620`). If the receipt only shows a line total for several units, Claude divides it by the quantity.
- **xN** — appended only when more than one unit was bought (unit price is shown, never the multiplied total).

## Catalog & matching

- The **catalog** is your list of allowed items. Each item = one canonical name + a growing set of learned aliases.
- **Reconcile flow:** anything Claude can't match appears in an orange "Unmatched" section. For each, you either **match it to an existing item** (saves the scanned text as a new alias) or **add it as a new item** (prefilled with a cleaned Title-Case guess you can edit). Your choice is remembered, so future scans auto-match.
- **My item list** section lets you rename/delete items, remove aliases, and add items manually.

## Cross-device sync

Your catalog syncs across devices with a **sync code** (Firebase Firestore):

- Open **My item list → Create sync code** on one device, then enter that code on another to pair them. Edits sync both ways in real time (last write wins).
- The device that *creates* the code seeds the shared catalog; a device that *joins* replaces its local catalog with the shared one.

To point it at your own backend, paste your `firebaseConfig` into the marked block in `index.html` and add Firestore security rules that restrict reads/writes to your sync-code documents.

## API key & privacy

- You supply your own **Anthropic API key** (from <https://console.anthropic.com>). Enter it in the app; **Save** stores it only in this browser's `localStorage`.
- The key is **never** committed to this repo or sent anywhere except directly to `api.anthropic.com` when you scan.
- Because a static site can't hide a key, this is intended for **personal use**. To share it publicly, put a small serverless proxy (e.g. a Cloudflare Worker) in front of the API so the key stays server-side.
- No receipts are stored — the image lives in the tab only until you close it. Only the catalog persists (locally + synced).

## Cost & quality

- Uses **Claude Haiku 4.5** (`claude-haiku-4-5`), the cheapest capable vision model — roughly **$0.001–0.003 per receipt**.
- Images are sent at **1568px** (Haiku's max edge) and **JPEG quality 0.92**. Image token cost depends on pixel dimensions, not file size, so the high quality is free — it just gives Claude sharper text.
- For maximum accuracy on hard receipts, change `MODEL` in `index.html` to `claude-sonnet-5` or `claude-opus-4-8` (higher cost).

## Hosting

Served via **GitHub Pages** from the `main` branch (repo must be public on the free plan).
Enable at repo **Settings → Pages → Source: Deploy from a branch → `main` / `(root)`**.

## Running locally

Static file — open `index.html`, or serve the folder (recommended, so camera & clipboard APIs behave):

```
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Tech

- [Anthropic Claude API](https://docs.claude.com/) — vision reading + catalog matching (structured JSON output)
- [Firebase Firestore](https://firebase.google.com/docs/firestore) — sync-code catalog sync
- [Bootstrap 5](https://getbootstrap.com/) — layout
- [Font Awesome](https://fontawesome.com/) — icons
