# Website Visit Log Analyzer
Hackathon Project #97 (Everyday Innovators · Data & Visualization)

A beginner-friendly, menu-driven Python app that summarizes website visits **by day and referrer**
from CSV data, with strict validation, JSON persistence, search, executive summaries, warnings,
Matplotlib charts and multi-format exports. Core app uses only the standard library; Matplotlib is
needed only for charts (the app still runs without it).

## Quick start
```bash
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt    # matplotlib (charts only)
python main.py
```
Requires Python 3.10+. Run tests: `python -m unittest discover -s tests -v`

## Features (brief → where it lives)
| Requirement | Implementation |
|---|---|
| Interactive terminal menu | `visitlog/cli.py`, `visitlog/ui.py` (ANSI colours, paginated tables) |
| Defensive input validation | `visitlog/validation.py` + retry loops (`ui.ask`) – bad input never crashes |
| Summarize by day & referrer | `visitlog/analytics.py` (`by_day`, `by_referrer`, `day_referrer_matrix`) |
| Local persistence | `data/visits.json` (atomic writes, auto-recovery from missing/corrupt file) |
| Search & filter | Menu 3: keyword, referrer, page, date range, min duration |
| Executive summary | Menu 7: totals, averages, bounce rate, busiest day, top referrer, trend |
| Matplotlib charts | Menu 8: daily trend, referrer distribution, stacked day × referrer (PNG) |
| Multi-format export (stretch) | Menu 9: Markdown report, TXT audit log, CSV summaries |
| Threshold warnings (stretch) | Traffic spikes (> mean + 2σ), single-source dependence (> 60%), high bounce rate, bot-like sessions, zero-visit days |
| Bulk import (stretch) | Menu 10: row-by-row validation, rejects logged to `output/import_errors.log` |

## Data format
CSV columns: `date,time,visitor_id,page,referrer,duration_sec`
- `date` YYYY-MM-DD (not in the future) · `time` HH:MM · `visitor_id` 1–32 chars `[A-Za-z0-9_-]`
- `page` starts with `/` · `referrer` domain (blank → `direct`; `https://www.Google.com/x` → `google.com`)
- `duration_sec` integer 0–86400

## Sample data
- `data/sample_visits.csv` / `.json` – 46 records incl. boundary cases (00:00, 23:59, 0s and 86400s
  durations, 32-char IDs), a traffic spike (Nov 12), a missing day (Nov 9) and referrer normalization.
- `data/legacy_import_with_errors.csv` – 10 rows, 8 deliberately invalid (bad date/time, blank ID,
  negative/non-numeric duration, future date, duplicate) to show the error log.
- Regenerate with `python generate_sample_data.py`.

## 5-minute demo script
1. `python main.py` → welcome screen → **12** (load sample dataset).
2. **1** (add visit): type a bad date, `99:99`, `bad id!`, `pricing`, `-5` to show validation, then valid values.
3. **4 / 5 / 6** → by-day, by-referrer, day × referrer tables.
4. **7** → executive summary with warnings (spike on Nov 12, gap on Nov 9, long sessions).
5. **10** → `data/legacy_import_with_errors.csv` → 2 accepted, 8 rejected with reasons.
6. **9** → exports + charts in `output/`.
7. **0** to exit, relaunch `python main.py` → record count is preserved (persistence).

## Project layout
```
main.py                     entry point
generate_sample_data.py     sample data generator
visitlog/  models · validation · storage · analytics · reports · plotting · ui · cli
data/      sample CSV/JSON, legacy CSV with errors, (visits.json created on first run)
output/    generated charts and reports
tests/     unit tests (validation, analytics, storage/import)
```

## Ideas to extend
Tkinter/Textual front-end · GeoIP or UTM-campaign grouping · weekly/monthly roll-ups · IP-based bot filtering.
