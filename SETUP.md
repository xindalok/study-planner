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
   - `TELEGRAM_TOKEN` / `TELEGRAM_CHAT_ID` — see step 4 below, or leave the
     placeholders if you're skipping Telegram for now.
   - `APP_URL` — the `/exec` URL from step 6, once you have it.
5. Select `firstTimeSetup` in the function dropdown → **Run**. Approve the permission
   prompt. This creates the `Schedule` and `Settings` tabs and generates the next few days.
6. **Deploy → New deployment → Web app.**
   - Execute as: **Me**
   - Who has access: **Only myself**
   - Deploy, then copy the `/exec` URL. That URL *is* the app.

> **There is no API key, deliberately.** An earlier version shipped a shared secret
> in `index.html` and deployed for "Anyone". That can't work: `index.html` is sent to
> the browser, so anyone who loaded the page could read the key and then read and
> write the schedule. The app is now served by Apps Script itself via `HtmlService`,
> the deployment is restricted to **Only myself**, and Google authenticates you before
> any code runs. Do not set access back to "Anyone" — with no key in the code, that
> would leave the schedule open to anyone with the URL.

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

## 3. The app on your phone

`index.html` is part of the Apps Script project — `doGet` serves it with
`HtmlService`, and the page talks to the backend over `google.script.run`, which
carries your Google identity. So there is no separate host, no `API_URL`, and no
`API_KEY` to keep in sync.

1. Open the `/exec` URL from step 1.6 on your phone, signed in to the Google
   account that owns the script.
2. **Add to Home Screen** — it behaves like an app from there.

> If the phone has several Google accounts, the `/exec` link may open under the
> wrong one and show "You need access". Open it from the right account, or append
> `/u/0/` style account selection via the Google account switcher.

The GitHub repo is now just a source backup — GitHub Pages is intentionally
disabled, because a static public page cannot hold a credential.

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
