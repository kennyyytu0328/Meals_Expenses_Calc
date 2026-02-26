# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**MealWise AI — 智慧餐費結帳系統 (BYOK)**

A single-file HTML web app for parsing meal expense PDF reports using the Google Gemini AI API. Users bring their own API key (BYOK), which is stored only in `localStorage` under key `mealwise_gemini_key`.

## Running Locally

```bash
# Preferred (uses package.json script)
npm run dev
# Serves at http://localhost:8080

# Alternative: Python
python -m http.server 8080
```

> Must be served via HTTP (not `file://`) due to ES Module import maps.

## Architecture

This is a **zero-build, single-file app** (`index.html`). There are no bundlers, transpilers, or build steps. All dependencies are loaded via CDN at runtime:

| Dependency | CDN | Purpose |
|---|---|---|
| React 18 | unpkg | UI rendering |
| Babel Standalone | unpkg | JSX transpilation in-browser |
| Tailwind CSS | cdn.tailwindcss.com | Styling |
| PDF.js 3.11 | cdnjs | PDF-to-image conversion |
| `@google/generative-ai` | esm.run via importmap | Gemini API calls |
| Lucide Icons | unpkg | Icon rendering |

## Key Functions in `index.html`

All logic lives inside a single `<script type="text/babel" data-type="module">` block:

- **`pdfToImage(file)`** — Renders page 1 of PDF to JPEG base64 via PDF.js canvas (scale 2.0)
- **`processPDF(pdfFile)`** — Calls `gemini-2.5-flash` with the image + extraction prompt; parses the JSON array response
- **`handleFileUpload(e)`** — Validates `.pdf` type then triggers `processPDF`
- **`handleReceivedChange(index, value)`** — Updates received amounts and calculates change (integers only, 0–50000 range)
- **`Icon({ name, size, className })`** — Wrapper that renders Lucide icons imperatively via `lucide.createIcons()`

## State Flow

```
showSettings=true (API Key screen)
  → saveApiKey() → showSettings=false
  → uploadStep=1 (upload area)
  → handleFileUpload() → uploadStep=2 (analyzing spinner)
  → processPDF() resolves → uploadStep=3 (results table + summary)
```

Parsed data shape per row:
```js
{ rank, empId, name, due: number, received: "" | number, change: number }
```

## Gemini Prompt

The prompt in `processPDF` instructs the model to return a **pure JSON array** (no markdown). The code cleans up any stray ` ```json ``` ` markers before `JSON.parse()`. The model is hardcoded to `gemini-2.5-flash` — change this string if a different model is needed.

## Known Limitations

- Only parses page 1 of the PDF
- `due` amounts are stored as integers (`parseInt`)
- No multi-page support; extend `pdfToImage` with a loop if needed
