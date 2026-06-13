# ABD — LinkedIn Job Scraper

Single-file Google Colab notebook (`data_collection.ipynb`). Not a Python package — no `setup.py`, `requirements.txt`, tests, or CI.

## Dependencies

Install before running: `pip install requests beautifulsoup4 pandas lxml`

## Usage

Open `data_collection.ipynb` in Google Colab or Jupyter. Run the cells in order:

1. **Install cell** — `!pip install ...`
2. **Main code cell** — defines `run_scraper()` and helpers
3. **Call cell** — invokes `run_scraper()` with keywords/locations config

## Structure

- `data_collection.ipynb` — entire project (notebook, ~290 SLOC)
- Outputs `raw_jobs_linkedin.csv` and `raw_jobs_linkedin.json` with deduplicated records

## Scraping quirks

- Uses LinkedIn's **guest** job search API (no login). No authentication needed.
- Detail pages fetched via `id.linkedin.com` subdomain with full browser-like headers.
- Random delay 3–6s between detail requests, 2–3s between pages, retry with backoff on 429.
- Cards parsed via multiple CSS selectors (fragile — LinkedIn may change them).
- Check `scraping_linkedin.log` for rate-limit warnings and failures.

## Key functions

| Function | Role |
|---|---|
| `make_session()` | Seeds session with LinkedIn homepage + jobs page cookies |
| `get_job_cards()` | Fetches search result page (25/pg), returns partial records |
| `scrape_detail()` | Fetches individual job detail page, parses full schema |
| `run_scraper()` | Orchestrator: iterates keywords × locations × pages |

Partial fallback: if detail scrape fails, record is kept with `"Tidak Dicantumkan"` defaults.
