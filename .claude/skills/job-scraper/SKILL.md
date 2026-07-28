---
name: scrape
description: >
  Finds new job postings matching your profile via installed portal-search CLIs
  (LinkedIn, local job boards, and any skills added with /add-portal). Deduplicates
  across runs. Triggers on: job scrape, find jobs, search jobs, new jobs, job search,
  scrape jobs, /scrape
allowed-tools: Read, Write, Edit, Glob, Grep, Bash(bun --version), Bash(bun run .agents/skills/*/cli/src/cli.ts *), WebFetch, WebSearch, Agent, AskUserQuestion
---

# Job Scraper

---

## How It Works

This skill searches job portals using the **installed portal-search CLIs** in
`.agents/skills/` (plus WebSearch as a fallback), using queries from your profile.
It deduplicates against previously seen jobs and the application tracker, scores
each new match with a numeric fitness assessment, and presents results sorted by
that score.

## Invocation

The user triggers this skill by saying things like:
- "Find new jobs"
- "Scrape for jobs"
- "Any new positions?"
- "/scrape"

Optional arguments:
- A focus area, e.g. "/scrape data science" or "/scrape geophysics"
- "broad" to run all search categories, e.g. "/scrape broad"

---

## Execution Steps

### Step 0: Load State

1. Read `job_scraper/seen_jobs.json` (create if missing - start with `{"seen": {}}`)
2. Read `job_search_tracker.csv` to extract already-applied companies+roles
3. Read `search-queries.md` (this directory) for the search strategy

### Step 1: Search

Read `search-queries.md` (this directory) for the search strategy. By default, run the top 3 priority query categories. If the user said "broad", run all categories. If the user specified a focus area (e.g. "data science"), prioritize queries from that category.

**Use the installed CLI tools as the primary search mechanism.** Fall back to `WebSearch` only for portals that do not have a CLI skill, or if `bun` is unavailable on the system.

#### 1a. Check bun availability

```bash
bun --version
```

If this fails (bun not installed), skip to **1c (WebSearch fallback)** for all portals and note the fallback in the Step 5 output.

#### 1b. Run CLI tools (primary — run these in parallel where possible)

Discover all installed portal CLI skills by reading every `SKILL.md` found under `.agents/skills/*/SKILL.md`. Each file documents that portal's exact CLI flags and usage examples. **Use each portal's own documented interface — do not guess flags.** This approach automatically includes any new portals added via `/add-portal` without requiring changes to this file.

For each installed portal skill:

1. Read its `SKILL.md` to find the correct `bun run …` invocation and supported flags.
2. Translate the query terms from `search-queries.md` into that portal's flag format (e.g. `--key`, `--search-string`, `--query`, filter codes — whatever the portal's SKILL.md specifies).
3. Scope to the last 14 days using the portal's supported recency flag (`--jobage`, `--since <YYYY-MM-DD>`, `--order PublicationDate`, etc. — as documented per portal).
4. Cap results to ~20 per call using the portal's limit flag.
5. Use `--format json` for machine-readable output.

Run all portal CLI calls in parallel where possible using the Agent tool. Collect all `results` arrays into a single pool for Step 2, keeping each result tagged with its source portal skill (for Step 2 `detail` lookups).

If a CLI tool exits with a non-zero code, log the error message and continue — do not abort the whole search.

#### 1c. WebSearch fallback

Use `WebSearch` for:
- Portals listed in `search-queries.md` that do **not** have a corresponding directory under `.agents/skills/`
- Any portal whose CLI fails at runtime
- When bun is unavailable (Step 1a failed)

Use the site-specific query strings from `search-queries.md` directly as WebSearch queries for these portals.

### Step 2: Fetch & Parse

For each promising result from Step 1:

**From CLI results:** Search output already includes title, company, location, date,
and URL. For jobs worth a deeper look, fetch full detail with that portal's `detail`
command (see its SKILL.md — do not guess flags) to extract **key requirements**,
**application deadline**, and a brief description snippet.

**From WebSearch results:** Use `WebFetch` on the posting URL and extract the same
fields manually.

For every candidate:
- Skip if the URL or company+title combo already exists in `seen_jobs.json`
- Skip if the company+role already appears in `job_search_tracker.csv`

### Step 3: Fitness Assessment

For each new job, compute a numeric **fitness score (0-100)** using the same weighted dimensions as `.claude/skills/job-application-assistant/04-job-evaluation.md` - this is **triage-depth** (posting text + profile only, no company research, no salary lookup), the same depth `/rank` operates at. Read that file once per run if it isn't already in context.

1. Score each dimension from whatever posting content Step 2 fetched (full detail if fetched, title/snippet if that's all there was), against the framework's Strong/Moderate/Weak match areas and career-goal priorities:
   - Technical Skills (0-100)
   - Experience Match (0-100)
   - Behavioral/Culture Fit (0-100) - light-touch; deep culture research belongs to Step 3.5, not this step
   - Career Alignment (0-100)
   - Location: PASS/FAIL/FLAG (unweighted - a FAIL excludes the job per Important Rule 3 regardless of score)
2. Compute the overall score with the framework's weights (Technical 30%, Experience 25%, Behavioral 15%, Career 30%) and store as `rank_score`.
3. Map to the framework's verdict bands (Strong Fit 75+, Good Fit 60-74, Moderate Fit 45-59, Weak Fit 30-44, Poor Fit <30) and store as `rank_verdict`.
4. Derive the coarse `fit` bucket that gates Step 3.5 and the rest of this skill directly from the score: `high` = Strong/Good Fit (60+), `medium` = Moderate Fit (45-59), `low` = Weak/Poor Fit (<45).

For every high/medium match, also write a **one-line fit reason in Hebrew** (למה זה מתאים: which core skills/experience/behavioral traits from the profile the posting hits, or the main gap). This feeds the Hebrew presentation in Step 5.

**Honesty rule (same as `/rank` and `/apply`):** score only from content actually fetched - never invent posting details, never inflate a score because a company name looks prestigious. If Step 2 could only get a title/snippet (no real detail), leave `rank_score`/`rank_verdict` unset, keep the bucket at `low`, and note "לא ניתן לדרג - תיאור לא נשלף" in the fit reason instead of guessing a number.

### Step 3.5: Company Snapshot (high/medium matches only)

Enrich each high/medium match with company context (cap at ~6 companies per run; skip low matches):

1. Check `seen_jobs.json` first - if another entry for the same company already carries `company_info`, reuse it.
2. Otherwise run WebSearch: `"<company>" LinkedIn about employees` and `"<company>" Glassdoor rating reviews work culture`.
3. Extract three things:
   - **What the company does** - one line (product/domain/size if visible)
   - **Location** - the office relevant to the posting, plus a commute check against the location filter in `search-queries.md`
   - **Reputation as a workplace** - rating if found (e.g. "Glassdoor 4.1/5"), one line of recurring themes (culture, management, work-life balance), and the source
4. **Do not WebFetch glassdoor.com pages directly** (login/bot wall) - rely on search snippets. Never invent ratings; if nothing reliable surfaces, write "לא נמצא מידע אמין" and move on.
5. Store the result on the job entry in `seen_jobs.json` as `company_info: {"about": "...", "location": "...", "reputation": "...", "source": "...", "checked": "YYYY-MM-DD"}` so future runs reuse it.

### Step 4: Deduplicate & Store

1. Add ALL fetched jobs (new and skipped) to `seen_jobs.json` with structure:
```json
{
  "seen": {
    "<url_or_company_title_key>": {
      "title": "...",
      "company": "...",
      "url": "...",
      "first_seen": "YYYY-MM-DD",
      "fit": "high/medium/low",
      "status": "new/skipped/evaluated/ranked/expired",
      "rank_score": 0,
      "rank_verdict": "Strong Fit/Good Fit/Moderate Fit/Weak Fit/Poor Fit",
      "rank_date": "YYYY-MM-DD"
    }
  }
}
```

Step 3's Fitness Assessment writes `rank_score`, `rank_verdict`, and `rank_date` directly and sets `status` to `"ranked"` - it scores at the same depth `/rank` does and writes the same fields, so a job scored during this run does not need a separate `/rank` pass. Leave these three fields unset (and `status: "new"`) only for the Step 3 honesty-rule fallback, where no real posting content was fetched to score from. `/rank` remains useful afterward for: re-scoring everything with `--all` once the profile changes, and picking up older entries that predate this step and still only carry a `fit` bucket with no `rank_score`.

Step 3.5 extends it additively too: high/medium entries may carry `company_info` (about / location / reputation / source / checked). Preserve it when re-writing entries, and reuse it instead of re-searching a company seen in a previous run (refresh if `checked` is older than ~60 days).

2. Only present jobs NOT already in the seen list or tracker.

### Step 5: Present Results

**Present the results in Hebrew** (user preference). Keep job titles, company names, and technical terms in their original language; everything else - headers, fit reasons, company snapshots - in Hebrew. **Sort by `rank_score` descending** (highest fitness score first; unscored fallback entries last):

```
## משרות חדשות - YYYY-MM-DD

נמצאו X משרות חדשות (Y התאמה גבוהה, Z בינונית, W נמוכה).

| # | ציון | דירוג | תפקיד | חברה | מיקום | מועד אחרון | קישור |
|---|------|-------|-------|------|-------|-----------|-------|
| 1 | 84 | Strong Fit | ... | ... | ... | ... | [קישור](...) |

### פירוט - התאמה גבוהה ובינונית
לכל משרה בהתאמה גבוהה/בינונית:
- **ההתאמה שלך:** 1-2 שורות בעברית - אילו כישורים/ניסיון מהפרופיל המשרה פוגעת בהם, ומה הפער העיקרי אם יש (מ-Step 3)
- **על החברה:** מה החברה עושה, מיקום המשרד ובדיקת נסיעה, והמוניטין שלה כמקום עבודה כולל דירוג ומקור (מ-Step 3.5)
- **דרישות מפתח לבדוק / דגלים אדומים**
```

After presenting, ask (in Hebrew):
> "רוצה שאעריך לעומק אחת מהמשרות? תן לי את המספר/ים."

If the user picks a number, invoke the **job-application-assistant** skill workflow (fit evaluation first, then CV + cover letter if approved) - it re-evaluates from scratch with company research; this step's fitness score is a triage signal, not a substitute.

`/rank` is still available for a full re-score of everything (`--all`, e.g. after a profile update) or to pick up older entries scraped before this step existed that still only carry a `fit` bucket with no `rank_score`. (`/rank` sets the `ranked` and `expired` status values in `seen_jobs.json`; treat both as already-seen for dedup purposes.)

### Step 5.5: Write the Dated Search-Output File (every run)

After presenting, **always** write a persistent search-output file so the run is reproducible and the user has apply links plus document links in one place.

- **Path:** `job_scraper/<DDMMYY>.md` (matches the user's convention, e.g. `140726.md` for 2026-07-14). The `job_scraper/` directory is gitignored (personal data), so these files stay local. If today's file already exists, update it rather than clobbering prior detail.
- **Contents (Hebrew, per Step 5 preference; keep titles/company/technical terms in the original language):**
  - Search date, sources searched, and summary counts.
  - A table of every high/medium match: title, company, location, posted date, and a markdown **apply link** (the posting URL).
  - For any job in this run that `/apply` produced documents for, add links to the created **CV and cover letter**. Paths are relative from `job_scraper/`: `../cv/main_<company>.pdf` and `../cover_letters/cover_<company>_<role>.pdf`, plus the `.tex` sources. Only link documents that actually exist on disk; leave the columns blank until `/apply` has generated them.
  - Company snapshots from Step 3.5 (about / location / reputation + source).
  - Skipped/low matches summarized, with a pointer to `seen_jobs.json`.

### Step 6: Update Tracker (Optional)

If the user decides to apply to any job, add a row to `job_search_tracker.csv`.

---

## Important Rules

1. **Never fabricate job postings.** Only present jobs from actual CLI search/detail output or WebSearch/WebFetch results.
2. **Respect deduplication.** Always check seen_jobs.json AND job_search_tracker.csv before presenting.
3. **Focus on configured geographic area.** Skip jobs that require relocation or are clearly outside commute range.
4. **Only open positions.** Skip postings with expired deadlines or those marked as closed.
5. **Be efficient with detail fetches.** Don't run `detail` or WebFetch on every search hit — pre-filter by title/snippet, then fetch only promising matches.
6. **Parallel searches.** Run portal CLI searches in parallel; use WebSearch only for gaps the CLIs don't cover.
7. **Company reputation is best-effort.** Ratings and culture signals come from public search snippets (LinkedIn/Glassdoor); always name the source, never invent a rating, and treat reviews as one noisy signal - not a verdict.
8. **Hebrew presentation.** Final output to the user is in Hebrew (Step 5); job titles, company names, and technical terms stay in the original language.
9. **Fitness score is triage-depth, not final.** Step 3's `rank_score` comes from posting text and your profile only - it stands in for `/rank`'s scoring so you don't have to run both, but it is never a substitute for `/apply`'s full evaluation (fresh fetch, company research, salary benchmark) before anything gets drafted.
