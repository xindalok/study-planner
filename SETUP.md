# Study Planner — setup

Three pieces: a Google Sheet (data), an Apps Script Web App (backend), and a page on
GitHub Pages (the app). Telegram is optional but wired up.

Do this once on a laptop. After that everything works from your phone.

---

## 1. Sheet + backend

1. Create a new Google Sheet. Name it anything.
2. **Extensions → Apps Script**. Delete the placeholder code.
3. Paste in all of `Code.gs`.
4. At the top of the file, fill in:
   - `API_KEY` — invent a random string. You'll paste the same one into `index.html`.
   - `TELEGRAM_TOKEN` / `TELEGRAM_CHAT_ID` — see step 4 below, or leave the
     placeholders if you're skipping Telegram for now.
   - `APP_URL` — your GitHub Pages URL from step 3.
5. Select `firstTimeSetup` in the function dropdown → **Run**. Approve the permission
   prompt. This creates the `Schedule` and `Settings` tabs and generates the next few days.
6. **Deploy → New deployment → Web app.**
   - Execute as: **Me**
   - Who has access: **Anyone**
   - Deploy, then copy the `/exec` URL.

> "Anyone" is required because your phone's browser calls this URL without a Google
> login. The `API_KEY` is what actually keeps strangers out, so make it long.

---

## 2. Settings you can change from your phone

The `Settings` tab holds:

| Key | Meaning |
|---|---|
| `weekdayStart` | when Mon–Fri begins (default `19:30`) |
| `saturdayStart` | default `16:00` |
| `sundayStart` | default `11:00` |
| `rotationIndex` | internal — tracks the CDL/Korean alternation |

You can edit start times in the Sheets mobile app, but the app itself has a start-time
field at the top, which is easier.

---

## 3. The app on GitHub Pages

1. Create a repo, e.g. `study-planner`.
2. Add `index.html` to the root.
3. In `index.html`, set:
   - `API_URL` → the `/exec` URL from step 1.6
   - `API_KEY` → the same string you put in `Code.gs`
4. **Settings → Pages → Source: main branch, / (root)**. Save.
5. Your app is at `https://YOURNAME.github.io/study-planner/`.
6. Open it on your phone and **Add to Home Screen** — it behaves like an app from there.

---

## 4. Telegram (optional)

1. Message **@BotFather** → `/newbot` → copy the token.
2. Message your new bot once so it can reply to you.
3. Visit `https://api.telegram.org/bot<TOKEN>/getUpdates` and find `"chat":{"id":...}`.
4. Put both into `Code.gs`, then **redeploy** (Deploy → Manage deployments → edit → New version).
5. Run `registerTelegramWebhook` once, with your `/exec` URL pasted into the
   `WEB_APP_URL` constant inside that function.
6. Run `setupTrigger` once to schedule the daily reminder (defaults to 6pm — change
   `atHour` first if you want it elsewhere).

Commands: `/today` resends today's plan, `/replan` rebuilds today from the template.

---

## How the schedule works

Times are **never stored** — only the order of blocks and each block's duration. The
start time plus the durations produce the clock times on every read. That's why
dragging a block to a new position instantly recomputes everything below it.

Default shape:

- **Weekdays and Sunday** — App Dev (3 × 30min, 10min breaks between), 30min long
  break, then the rotating subject (3 × 30min, 10min breaks). 3h of focus.
- **Saturday** — App Dev only (3 × 30min + breaks). 1.5h of focus.
- The second subject alternates Digital Cloud Leader → Korean → Digital Cloud Leader …
  across every day that has one.

To change durations or the shape, edit `FOCUS_MIN`, `SHORT_BREAK_MIN`, `LONG_BREAK_MIN`
or `buildTemplate()` in `Code.gs`, then use **Reset this day** in the app to rebuild.

New days are generated automatically by the daily trigger, three days ahead. Existing
days are never overwritten, so your check-offs and custom ordering survive.
