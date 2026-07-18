# Ledger — Study Consistency Tracker

A small web app for logging study blocks, tracking subject/topic checklists, logging practice-question sessions, and getting a rule-based read on your consistency. Pure HTML/CSS/JS — no build step, no framework — so it drops straight into GitHub Pages.

## 1. Set your Firebase rules

The app talks to your Realtime Database directly over its REST API, with no login. That means **your database rules must allow public read/write**, or every request will fail with a 401.

In the Firebase console → Realtime Database → Rules, use:

```json
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

⚠️ This makes the data readable and writable by anyone who has the URL. Fine for a personal tool nobody else knows the link to, but not for anything sensitive. If you want it locked down later, the upgrade path is: turn on Firebase Auth (email/password or anonymous), set rules to `"auth != null"`, and add the Firebase Auth SDK + a sign-in step to `app.js` — happy to build that next if you want it.

## 2. Deploy on GitHub Pages

1. Create a new repo (or use an existing one) and add these three files to the root: `index.html`, `style.css`, `app.js`.
2. Push to GitHub.
3. Repo → **Settings → Pages** → under "Build and deployment", set Source to **Deploy from a branch**, branch `main`, folder `/root`.
4. Save. Your app will be live at `https://<your-username>.github.io/<repo-name>/` within a minute or two.

No API keys, no `.env`, no backend — the Firebase URL is already wired into `app.js`.

## 3. What's inside

- **Dashboard** — today's blocks, streak counter, hours logged, and a quick insight.
- **Study Blocks** — add/edit/delete blocks with start/end time, subject, topic covered, and notes. Grouped by date.
- **Subjects** — add subjects, add/edit/delete topics under each, checkbox to mark a topic done/pending, live progress bar per subject.
- **Practice Log** — log questions attempted/correct per session; a chart plots attempted-questions volume against accuracy % over time, filterable by subject.
- **Insights** — a rule-based engine that reads your own logged data (streaks, subject balance, backlog, accuracy trend, session length) and surfaces what's worth adjusting. It's pattern-matching over your entries, not a live LLM — no external AI calls are made, so there's no API key to manage and it works fully offline from any AI provider.

## 4. Notes on the data

Everything lives in your Realtime Database under three top-level nodes: `studyBlocks`, `subjects` (with nested `topics`), and `practiceLog`. Nothing is stored locally — refreshing or switching devices just re-reads the same data.

