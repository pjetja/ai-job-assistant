# AJA Track Application

Log a new job application to the tracker.

## Arguments

`$ARGUMENTS` contains: `<job-url> <role>`

- `job-url` — required. URL of the job posting.
- `role` — required. Which CV was / will be sent (e.g. "Senior Software Engineer").

If `/aja-match-job` was already run for this URL, it will have set `Job ID` automatically via Step 3b. Otherwise `Job ID` is stored as `-` and linked later when needed.

Examples:
```
/aja-track https://example.com/jobs/123 "Senior Software Engineer"
```

## Config

Read `~/JOB_ASSISTANT_DATA/config.md` (or `$AJA_DATA_DIR/config.md`).
Extract: `data_dir`.

Tracker file: `{data_dir}/tracker/applications.md`

## Step 1 — Check for duplicate

Read `applications.md` if it exists.
Search all rows for the job URL. If found: "Already tracked: {job name} (row #{id}). Use /aja-update to update status." and stop.

## Step 2 — Fetch job info

Try to get the job title and company name:
1. Check if the URL appears in any `{data_dir}/jobs/*/job.md` (via `job_url` field). If found: read `role_title` and `company_name` from there and set `job_id`.
2. Otherwise: invoke `aja-fetch-job` skill to fetch the URL, extract title and company. If fetch fails, use `?` as placeholder and note it. Set `job_id = -`.

## Step 3 — Assign ID

Count existing data rows in `applications.md` (lines starting with `| ` that are not header/separator). New ID = count + 1.

## Step 4 — Append row

Initialize `applications.md` with header if file is empty or missing:

```markdown
# Job Applications

## Applications

| # | Job Name | Company | CV | Job ID | Sent | Response | HR Screen | Tech Interview | Non-Tech Interview | Offer | Rejected | Notes |
|---|----------|---------|-----|--------|------|----------|-----------|----------------|-------------------|-------|----------|-------|
```

Append new row:
```
| {id} | [{job_title}]({job_url}) | {company} | {role} | {job_id or -} | {YYYY-MM-DD} | - | - | - | - | - | - | - |
```

Also append to `## History` section (create if missing):
```markdown
## History

<!-- {id} | {job_title} | {company} -->
- {YYYY-MM-DD}: Application sent with {role} CV
```

## Step 5 — Regenerate HTML

Invoke the `aja-dashboard` command instructions (generate HTML from updated MD).

## Step 6 — Confirm

Print: `Logged #{id}: {job_title} — {company} ({role}, {date})`
