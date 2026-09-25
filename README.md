# Syllabus AI

A local, offline-first study and exam-prep dashboard. Tracks chapters,
exams, worksheets, and tasks, scores what actually needs your
attention using a real prioritization system (not just due dates), and
optionally pings you on Discord and lets you talk to an AI assistant
about your own data.

Runs entirely on your own computer. Your data never leaves your
machine unless you choose to connect Discord or an AI provider.

---

## What you get

- **Chapter and exam tracking** with a real scoring system: urgency,
  effort, and priority combined, not just a due-date sort
- **Worksheets** on a spaced-retrieval schedule
- **Tasks** — a simple to-do list for non-syllabus things (School,
  Tuition, Self, Other)
- **Discord notifications** — a daily digest, urgent-chapter alerts,
  exam countdowns, and a full syllabus dump 5 days before each exam,
  all written in a dry, direct voice
- **An AI assistant** built into the app that can read and update your
  data by chat, if you provide your own API key
- **Gamification** — XP and ranks, if that motivates you

---

## Requirements

- Windows 10 or 11 (this is built and tested for Windows; Mac/Linux
  will need manual adjustments)
- [Python 3.10 or newer](https://www.python.org/downloads/) — when
  installing, check **"Add Python to PATH"**
- That's it. No database server, no Docker, nothing else to install
  system-wide.

---

## 1. Get the code

Download this repository as a ZIP (green **Code** button → **Download
ZIP**) and extract it somewhere you'll remember, or clone it:

```bash
git clone https://github.com/NKARN-CODE/Syllabus-AI.git
```

## 2. Install dependencies

Open a terminal (Command Prompt or PowerShell) inside the extracted
folder, then:

```bash
pip install -r requirements.txt
```

If you hit a permissions error, try `pip install -r requirements.txt --user`.

## 3. Run it

Double-click **`Run_Dashboard.bat`**, or from a terminal:

```bash
cd backend
python desktop.py
```

The app opens in its own window. Your data lives in
`backend/syllabus.db`, created automatically on first run.

---

## Setting up Discord notifications (optional)

You'll get a daily summary and real-time alerts posted to a Discord
channel of your choice. This uses a **webhook**, not a bot — no
Discord Developer account needed, five minutes total.

1. In Discord, right-click the channel you want alerts in → **Edit
   Channel**
2. Go to **Integrations** → **Webhooks** → **New Webhook**
3. Give it a name (e.g. "Syllabus AI"), click **Copy Webhook URL**
4. In the `backend` folder, copy `.env.example` and rename the copy to
   exactly `.env` (not `.env.txt` — turn on file extensions in Windows
   Explorer if you can't see them)
5. Open `.env` in Notepad. You'll see one line:
   ```
   DISCORD_WEBHOOK_URL=
   ```
   Paste your webhook URL right after the `=`, no quotes, no spaces.
   Save the file.
6. Restart the app. Go to **Settings** — it should now say **"✓
   Connected."**

**If it doesn't connect**, run this from the `backend` folder for a
plain-English diagnosis:

```bash
python -c "import discord_notify; discord_notify.diagnose()"
```

It'll tell you exactly what's wrong — file missing, URL malformed, or
Discord itself rejecting the request — instead of a bare error.

**What you'll actually receive**, once connected:

| When | What |
|---|---|
| Every day, 7am | Full digest: nearest exam, worksheets due, tasks due, streak, one suggested priority |
| Every day, if anything is overdue | Urgent chapters, grouped by subject |
| Every day, if a worksheet is due | Worksheet reminder |
| Every day, while an exam is within 10 days | Exam countdown with readiness % |
| Once, exactly 5 days before an exam | Full syllabus for that exam, grouped by subject |
| Every day at 9pm, only if nothing's logged yet | Streak-risk nudge |

The background check runs hourly, so expect messages within the hour
of their trigger time, not the exact minute. All of these timings are
adjustable — see `EXAM_COUNTDOWN_START_DAYS`, `DAILY_DIGEST_HOUR`, and
similar constants near the top of `backend/db.py`.

---

## Setting up the AI assistant (optional)

The AI assistant lives in **Settings → AI Assistant**. It can answer
questions about your data and make changes (mark chapters done, create
exams, link chapters to exams) through natural conversation. It cannot
delete anything — deletions stay in the main UI only.

You need your own API key from one provider:

- **Anthropic (Claude)** — [console.anthropic.com](https://console.anthropic.com)
- **OpenAI** — [platform.openai.com](https://platform.openai.com)
- **NVIDIA NIM** — any OpenAI-compatible NIM endpoint

Paste your key into Settings, pick your provider and model, save. Each
message you send costs one API call billed to your own account — this
app doesn't mark up or resell usage.

---

## Data & privacy

- Everything lives in `backend/syllabus.db`, a single SQLite file, on
  your computer only
- `.env` (your Discord webhook) and `syllabus.db` (your real data) are
  both excluded by `.gitignore` — if you fork or push this repo, they
  never get committed
- Nothing is sent anywhere except: Discord (only the notification
  text, only if you set up a webhook) and your chosen AI provider
  (only if you use the assistant, only the messages you send it)

---

## Troubleshooting

**The app won't start / port already in use**
Close any other copy of the app that might already be running, then
try again.

**`ModuleNotFoundError` on launch**
Run `pip install -r requirements.txt` again from inside the `backend`
folder specifically, not the repo root.

**Discord shows "Not connected" after setup**
Run the diagnostic command above. The most common cause is the `.env`
file being saved as `.env.txt` by Windows without you noticing.

**I want to start fresh**
Close the app, delete `backend/syllabus.db`, restart. A new empty
database is created automatically. This is permanent and cannot be
undone — back up the old file first if you want to keep it.

---

## License / contributing

This is a personal project. Feel free to fork it for your own use.
