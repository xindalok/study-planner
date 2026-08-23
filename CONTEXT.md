# Study Planner — context for Claude Code

## What this is
A rolling 3-day study/work schedule with drag-to-reorder blocks, check-offs, and an
optional Telegram reminder. Backend is a Google Sheet + Apps Script Web App. Frontend
is a single-file HTML app meant for GitHub Pages.

## Files
- `Code.gs` — Apps Script backend (paste into Extensions > Apps Script in a new Sheet)
- `index.html` — the frontend app
- `SETUP.md` — full manual setup steps (Sheet, deployment, GitHub Pages, Telegram)

## What's still needed
1. Deploy `Code.gs` to a real Google Sheet + Apps Script Web App (ideally via `clasp`
   so it's scriptable rather than done by hand in the browser).
2. Generate a real `API_KEY`, put it in both `Code.gs` and `index.html`.
3. Set up a GitHub repo, push `index.html`, enable GitHub Pages.
4. Put the deployed `/exec` URL into `index.html`'s `API_URL`.
5. (Optional) Telegram bot token + chat ID into `Code.gs`, register the webhook,
   set the daily trigger — see SETUP.md section 4.

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
