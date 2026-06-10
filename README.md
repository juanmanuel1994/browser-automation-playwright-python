# browser-automation-playwright-python

Browser automation scripts built with [Playwright for Python](https://playwright.dev/python/).
Covers the most common automation tasks: automatic login, scraping JS-rendered pages,
capturing screenshots, and exporting data to CSV/JSON — with ready-to-run examples for
e-commerce and form automation.

---

## Requirements

- Python 3.10+
- pip

---

## Installation

```bash
# 1. Clone / download this folder
cd browser-automation-playwright-python

# 2. Create and activate a virtual environment (recommended)
python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS / Linux
source .venv/bin/activate

# 3. Install Python dependencies
pip install -r requirements.txt

# 4. Install Playwright browser binaries (Chromium only)
playwright install chromium

# 5. Copy the env template and fill in your values (optional)
cp .env.example .env
```

---

## Project Structure

```
browser-automation-playwright-python/
│
├── login_automation.py      # Automatic login + session persistence
├── scrape_dynamic.py        # Scraping JS-rendered (SPA) pages
├── screenshot_capture.py    # Full-page, element, mobile & batch screenshots
├── export_data.py           # CSV and JSON export / import helpers
│
├── examples/
│   ├── ecommerce_scraper.py # End-to-end e-commerce scraper (books.toscrape.com)
│   └── form_automation.py   # Form filling & multi-record submission (demoqa.com)
│
├── output/                  # Generated files land here (gitignored)
├── .env.example             # Environment variable template
├── requirements.txt
└── README.md
```

---

## Scripts

### `login_automation.py` — Automatic Login

Fills username/password, submits the form, saves session cookies to `output/session.json`,
and demonstrates reusing that session on a second browser launch.

```bash
python login_automation.py
```

**Key functions:**

| Function | Description |
|---|---|
| `login(page, url, username, password)` | Fill and submit any login form |
| `save_session(context)` | Persist cookies/storage to disk |
| `load_session(playwright)` | Reopen browser with saved session |

**Environment variables:**
```
LOGIN_URL=https://your-site.com/login
LOGIN_USERNAME=your_user
LOGIN_PASSWORD=your_pass
```

---

### `scrape_dynamic.py` — JS-Rendered Page Scraper

Waits for `networkidle` + a target selector before extracting data. Handles lazy loading
via automated scrolling. Override `extract_items_generic()` with site-specific selectors.

```bash
python scrape_dynamic.py
```

**Key functions:**

| Function | Description |
|---|---|
| `wait_for_dynamic_content(page, selector)` | Block until JS finishes rendering |
| `scroll_to_load_lazy(page)` | Scroll to trigger lazy-loaded content |
| `scrape_page(url, wait_selector)` | Full scrape pipeline for one URL |
| `save_results(items, filename)` | Save results to JSON |

**Environment variable:**
```
SCRAPE_URL=https://your-spa-site.com/listings
```

---

### `screenshot_capture.py` — Screenshots

```bash
python screenshot_capture.py
```

**Key functions:**

| Function | Description |
|---|---|
| `screenshot_full_page(url)` | Stitched full-page PNG |
| `screenshot_viewport(url)` | Visible area only |
| `screenshot_element(url, selector)` | Crop to a specific DOM element |
| `screenshot_mobile(url)` | iPhone 13 emulation |
| `screenshot_batch(urls)` | Multiple URLs in one browser session |

All output goes to `output/`.

---

### `export_data.py` — CSV & JSON Export

```bash
python export_data.py
```

**Key functions:**

| Function | Description |
|---|---|
| `export_json(data, filename)` | Write list of dicts to JSON |
| `export_csv(data, filename)` | Write list of dicts to CSV |
| `load_json(path)` | Read JSON back into memory |
| `load_csv(path)` | Read CSV back as list of dicts |
| `merge_exports(*paths)` | Merge + deduplicate multiple JSON files |

---

## Examples

### E-commerce Scraper (`examples/ecommerce_scraper.py`)

Scrapes book listings from [books.toscrape.com](https://books.toscrape.com) —
a scraping-friendly demo store — across multiple paginated pages.

Extracts: title, price (£), star rating, availability, and product URL.

```bash
python examples/ecommerce_scraper.py
```

Output files:
- `output/books.json`
- `output/books.csv`

Console summary:
```
Total books  : 60
Avg price    : £35.12
Avg rating   : 3.0 / 5
```

To scrape the full catalogue (1000 books), change `max_pages=3` to `max_pages=50`
in `scrape_catalogue()`.

---

### Form Automation (`examples/form_automation.py`)

Fills out the [DemoQA Practice Form](https://demoqa.com/automation-practice-form) —
a public form with all common field types.

Covers: text inputs, email, phone, gender radio buttons, date picker, subjects
autocomplete, hobby checkboxes, address textarea, and cascading state/city dropdowns.

```bash
# Visible browser (recommended for first run)
python examples/form_automation.py

# Headless (change the flag in __main__ or call directly)
```

Output files:
- `output/form_confirmation.png`
- `output/form_submission_result.json`

For bulk submissions, use `demo_multi_submit(records)` with a list of data dicts.

---

## Adapting to Your Own Site

1. **Login**: Update the CSS selectors in `login()` (username field, password field, submit button).
2. **Scraping**: Replace `extract_items_generic()` in `scrape_dynamic.py` with your target selectors.
3. **Forms**: Copy the field-filling pattern from `fill_practice_form()` and adjust selectors.
4. **Credentials**: Store secrets in `.env`, never hardcode them.

---

## Tips

- **Headless mode**: All scripts default to `headless=True`. Pass `headless=False` to watch the browser.
- **Timeouts**: Increase `timeout` arguments for slow sites.
- **Anti-bot**: For heavily protected sites, consider adding realistic delays with `page.wait_for_timeout(ms)` and randomizing user agents.
- **Async**: All scripts use the synchronous Playwright API. For high-concurrency needs, switch to `playwright.async_api` with `asyncio`.

---
