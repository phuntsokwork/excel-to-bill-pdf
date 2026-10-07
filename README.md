# Excel to Bill PDF

A single-file, browser-based tool that turns a spreadsheet of billing/collection records into printable payment receipts — 4 per page, laid out for cutting, and exported straight to PDF. No install, no server, no backend: open the HTML file in a browser and it runs entirely on your machine.

## What it does

1. **Upload** an Excel file (`.xlsx`, `.xls`) or `.csv`.
2. The app reads the column headers from your sheet and lets you **choose which columns appear** on the receipt (tick/untick).
3. You can **add custom fields** (e.g. "Old Acc. No.") that aren't in your sheet — they print with a blank line so you can fill them in by hand.
4. Set the **layout**: receipts per page, paper size (A4 / Legal / Letter), orientation (landscape/portrait), and whether to reserve blank space for a stamp & signature.
5. **Preview** updates live as you change settings.
6. **Download** a ready-to-print PDF — generated directly in the browser (no print dialog), named `receipts-YYYY-MM-DD.pdf`.

## Key features

- **4 receipts per page** in a 2×2 grid with dashed cut guides.
- **Column picker** — show/hide any column from your sheet; nothing is hardcoded to a fixed field list.
- **Custom fields** — add extra labeled fields with blank lines for handwritten data.
- **Smart layout** — a field automatically takes a full row (label: value) when its value is long (e.g. Transaction ID, Contract No.), otherwise two short fields share a row.
- **Accurate dates** — dates are read from the underlying Excel date value (not the on-screen formatting, which can vary by the computer's regional settings) and always rendered as `dd-mm-yyyy`, with a rounding fix so no date is ever off by a day. A dedicated "Month" column is rendered as `Mon-YYYY` (e.g. `Jul-2026`).
- **Print-safe design** — the receipt itself uses only black, gray, and a light neutral fill, so it looks the same on a black-and-white printer as it does on screen.
- **Reserved stamp/signature space** — a blank area next to the amount, with no visible placeholder box, ready for a physical stamp and signature.
- **Editable letterhead** — organization name and sub-line are plain text inputs you can change per batch.
- **Direct PDF export** — uses `html2canvas` + `jsPDF` to render and download the PDF immediately; there's no OS print dialog in the way.

## How to use it

1. Open `index.html` in any modern browser (Chrome, Edge, Firefox).
   Or use the hosted version: https://phuntsokwork.github.io/excel-to-bill-pdf/
2. Drop your Excel/CSV file onto the upload area, or click to browse.
3. In the left panel:
   - Tick/untick fields you want shown.
   - Add any custom fields you need.
   - Set receipts per page, paper size, orientation, stamp space, and cut guides.
   - Edit the letterhead text if needed.
4. Click **Generate receipts** to build the preview. Use the page tabs to flip through pages.
5. Click **Download .pdf** — the file downloads automatically once it's ready.

## Expected spreadsheet format

Any column headers are supported — the app just uses whatever headers are in row 1 of your first sheet. Columns seen in testing include things like:

| Column          | Notes                                                      |
|-----------------|--------------------------------------------------------------|
| Contract/Acc No.| Any identifier column                                        |
| Name            | Rendered as a full-width `Label: Value` row                  |
| Address         | Rendered as a full-width `Label: Value` row                  |
| Month           | If stored as an Excel date, renders as `Mon-YYYY`             |
| Bill Type       | e.g. Current / Arrear                                        |
| Date            | If stored as an Excel date, renders as `dd-mm-yyyy`            |
| Amount          | Automatically formatted with a `₹` symbol and thousands separators |
| Transaction ID  | Long values automatically get their own full-width row       |

A column named **SL** / **SL No.** is hidden by default (it's usually just a row counter) but can be re-enabled from the field list.

## Tech stack

Everything lives in one HTML file — no build step, no package manager, no server:

- **[SheetJS (xlsx)](https://sheetjs.com/)** — reads and parses the uploaded spreadsheet in the browser.
- **[html2canvas](https://html2canvas.hertzen.com/)** — renders each generated receipt page to an image.
- **[jsPDF](https://github.com/parallax/jsPDF)** — assembles those images into a downloadable multi-page PDF, sized to the chosen paper size and orientation.
- **Vanilla HTML / CSS / JavaScript** — the UI, field logic, and receipt template have no framework dependency.
- **Google Fonts** — Source Serif 4 (headings/amount), IBM Plex Sans (UI), IBM Plex Mono (receipt metadata).

All of these are loaded from public CDNs (`cdnjs.cloudflare.com`, `fonts.googleapis.com`), so an internet connection is required the first time each library/font loads, but no data ever leaves the browser — the spreadsheet is parsed and the PDF is built entirely client-side.

## File structure

```
index.html   ← the entire application (HTML + CSS + JS in one file)
```

## Known limitations

- Only the **first sheet** of a workbook is read.
- Very large sheets (many hundreds of rows) will produce a correspondingly large multi-page PDF; generation time scales with row count since each page is rendered as an image before being combined into the PDF.
- The app doesn't persist data between sessions — re-upload the file each time you open the tool.
- Field/label choices and layout settings are not currently saved; they reset if the page is refreshed.

## Possible next steps

- Save/load a "template" of field selections and layout settings.
- Support multiple sheets/tabs within one workbook.
- Add a lightweight backend if this needs to be shared with non-technical users as a hosted service instead of a local file.
