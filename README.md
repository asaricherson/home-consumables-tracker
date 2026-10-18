# Home Consumables Tracker

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR-USERNAME/home-consumables-tracker/blob/main/home_consumables_tracker.ipynb)

A Google Colab notebook that keeps a Google Sheet of replaceable household items (air filters, water filters, smoke detector batteries, etc.), tracks price history, and sends a push notification to my phone when something needs to be replaced.

## The problem

Household consumables fail silently. Nobody remembers when the upstairs HVAC filter was last changed, and the cat fountain filter gets swapped when someone notices the water smells. I wanted one place that tracks replacement intervals and tells me when to act.

## What it does

| Feature | Detail |
|---|---|
| **Sheet bootstrap** | Creates and formats the Google Sheet (frozen header, date and currency formats, color-coded status) and seeds it with 24 common household items |
| **Due-date logic** | Live sheet formulas compute Next Due, Days Until Due, and Status (OK / DUE SOON / OVERDUE) from Last Replaced + interval |
| **Price tracking** | Scrapes each product URL (JSON-LD structured data first, then meta tags, then site-specific selectors), logs a dated row to a Price History tab, and tracks lowest price seen |
| **Notifications** | Push alerts via [ntfy](https://ntfy.sh) with urgency levels; optional Gmail alert. Includes price and a buy link, and flags when the price is at its lowest seen |
| **Alert hygiene** | Per-item re-notify cooldown so one overdue filter does not ping every run |
| **Helper** | `mark_replaced("Air purifier filter")` resets an item's clock |

## Setup

1. Click the **Open in Colab** badge above.
2. Install the ntfy app and subscribe to a long random topic name.
3. In Colab Secrets (key icon) add `NTFY_TOPIC` and enable notebook access. Optional: `GMAIL_ADDRESS` and `GMAIL_APP_PASSWORD`.
4. Run all cells. Authorize Google Drive/Sheets access when prompted.
5. Fill in Product URL and Last Replaced in the sheet for items you own; delete the rest.

## Design decisions and limitations

- **Formulas live in the sheet, not Python.** Due dates and status calculate in Sheets so the tracker stays useful and readable without running the notebook.
- **Price scraping is best effort.** Large retailers (Amazon especially) often block scripts. Failures are reported and never break the run; prices can be entered manually. A reliable version would use a paid price API.
- **Manual trigger.** Colab has no free scheduler. Next step is porting to a GitHub Actions cron job with a service-account credential.
- **Secrets stay out of the repo.** Credentials come from Colab Secrets only.

## Stack

Python, gspread, Google Sheets API, BeautifulSoup, requests, ntfy.sh

## Roadmap

- [ ] GitHub Actions scheduled run (no manual trigger)
- [ ] Price-drop alerts (notify when price falls X% below 90-day average)
- [ ] Price history chart tab
- [ ] Quantity/stock-on-hand column so alerts say "reorder" vs "replace now"
