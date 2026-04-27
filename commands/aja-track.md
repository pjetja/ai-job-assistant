# AJA Track Application

Log a new job application to the tracker.

## Arguments

`$ARGUMENTS` contains: `<job-url> <role> [job-number]`

- `job-url` — required. URL of the job posting.
- `role` — required. Which CV was / will be sent (e.g. "Senior Software Engineer").
- `job-number` — optional. If the job was already analyzed with `/aja-match-job`, pass its sequence number (e.g. `2`). This links the tracker row to the match report.

Examples:
```
/aja-track https://example.com/jobs/123 "Senior Software Engineer"
/aja-track https://example.com/jobs/123 "Senior Software Engineer" 2
```

## Config

Read `~/JOB_ASSISTANT_DATA/config.md` (or `$AJA_DATA_DIR/config.md`).
Extract: `data_dir`.

Tracker file: `{data_dir}/tracker/applications.md`

## Step 1 — Resolve job-number (if provided)

If `job-number` is given:
- List directories under `{data_dir}/jobs/` sorted alphabetically.
- Select the Nth directory (1-indexed). Set `job_id` to that directory name.
- Verify `{data_dir}/jobs/{job_id}/job.md` exists. If not: warn "Job number {N} not found — linking skipped." and clear `job_id`.

## Step 2 — Check for duplicate

Read `applications.md` if it exists.
Search all rows for the job URL. If found: "Already tracked: {job name} (row #{id}). Use /aja-update to update status." and stop.

## Step 3 — Fetch job info

Try to get the job title and company name:
1. If `job_id` is resolved and `{data_dir}/jobs/{job_id}/job.md` exists: read `role_title` and `company_name` from there.
2. Otherwise: invoke `aja-fetch-job` skill to fetch the URL, extract title and company. If fetch fails, use `?` as placeholder and note it.

## Step 4 — Assign ID

Count existing data rows in `applications.md` (lines starting with `| ` that are not header/separator). New ID = count + 1.

## Step 5 — Append row

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

## Step 6 — Regenerate HTML

Invoke the `aja-dashboard` command instructions (generate HTML from updated MD).

## Step 7 — Confirm

Print: `Logged #{id}: {job_title} — {company} ({role}, {date})`
