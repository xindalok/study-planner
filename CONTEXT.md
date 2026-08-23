# Study Planner — context for Claude Code

## What this is
A rolling 3-day study/work schedule with drag-to-reorder blocks, check-offs, and an
optional Telegram reminder. Backend is a Google Sheet + Apps Script Web App. Frontend
is a single-file HTML app served by that same Apps Script project.

## Files
- `Code.gs` — Apps Script backend (paste into Extensions > Apps Script in a new Sheet)
- `index.html` — the frontend app
- `SETUP.md` — full manual setup steps (Sheet, deployment, GitHub Pages, Telegram)

## Status
Deployed and working. The Sheet, the Apps Script project, and the Web App
deployment all exist; `clasp` drives pushes and versioning from this folder.

## Auth — do not regress this
There is **no API key**. The app is served by the Apps Script project itself
(`doGet` → `HtmlService` → `index.html`) and calls the backend over
`google.script.run`. The deployment is **Only myself**, so Google authenticates
the caller before any code runs.

The original design put a shared `API_KEY` in `index.html` on GitHub Pages. That
is not securable — the page source is public, so the key was public. It has been
removed from both files and nothing accepts it any more. GitHub Pages is disabled;
the repo is a source backup only.

## What's still needed
1. Run `setupTrigger` once from the editor — it regenerates the rolling window, so
   without it the schedule stops after `DAYS_AHEAD`.
2. (Optional) Telegram bot token + chat ID into `Code.gs`, register the webhook,
   see SETUP.md section 4. Note the webhook needs its own **anonymous** deployment,
   since Telegram posts without a Google login; validate it with Telegram's
   `X-Telegram-Bot-Api-Secret-Token` header, not a key in the URL.

## Schedule design (already agreed, don't change without asking)
- 2 subjects/day: Slot 1 daily (alternates "Vibecode App Dev" / "Fundamentals
  (JS/React)"), Slot 2 by fixed weekday (Digital Cloud Leader most days, Korean
  pinned to Tue/Fri, none on Saturday).
- Each subject block = 3×30min focus + 10min short breaks between, 30min long break
  between slot 1 and slot 2.
- Weekday start 19:30, Saturday 16:00, Sunday 11:00 (all editable in the app).
- Times are never stored — only block order + duration. Clock times are recomputed
  from the day's start time on every read, which is what makes drag-to-reorder work.

## Suggested first prompt to Claude Code
"Read CONTEXT.md and SETUP.md in this folder. Deploy Code.gs as a Google Apps Script
Web App (use clasp if available), set up a GitHub repo for index.html with Pages
enabled, and wire the two together so the app works end to end."
