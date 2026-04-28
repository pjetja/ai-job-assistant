# AJA Match Job

Fetch a job posting, analyze requirements, and score all available CVs.

## Arguments

`$ARGUMENTS` contains: `<job-url> [role] [--full]`

- `job-url` — required. Full URL of the job posting.
- `role` — optional. If given, score only that role's CV. If omitted, score all roles that have a `cv.md`.
- `--full` — optional. If omitted, output short report. If present, output full report with skills breakdown, strengths, gaps, and intro suggestion.

Examples:
```
/aja-match-job https://justjoin.it/offers/stripe-senior-fe
/aja-match-job https://example.com/jobs/123 "Senior Frontend Developer"
/aja-match-job https://example.com/jobs/123 --full
```

## Config

Read `~/JOB_ASSISTANT_DATA/config.md` (or `$AJA_DATA_DIR/config.md`).
Extract: `data_dir`, `roles`.

## Step 0 — Duplicate check

Read `{data_dir}/tracker/applications.md` if it exists.
If the job URL appears in any row: warn "Already tracked (or applied): {job title} — {company}. Continuing analysis anyway."

## Step 1 — Assign job ID

List directories under `{data_dir}/jobs/`. Count existing ones (zero-padded 3 digits).
Derive a short company+role slug from the URL (take domain name and last path segment, truncate to 20 chars).
New ID = `{NNN}-{slug}` e.g. `007-stripe-senior-fe`.

If `{data_dir}/jobs/{id}/job.md` already exists (URL match): skip fetch/analyze, read existing job.md.
Otherwise: proceed.

## Step 2 — Fetch job posting

Invoke `aja-fetch-job` skill with `job_url`.
If result is `FETCH_FAILED`: ask user to paste the job description. Wait for paste. Use pasted text.

## Step 3 — Analyze job

Invoke `aja-analyze-job` skill with:
- `job_text`: fetched content
- `job_url`: original URL
- `job_id`: assigned ID
- `data_dir`: from config

Saves `{data_dir}/jobs/{job_id}/job.md`.

## Step 3b — Link to tracker

Read `{data_dir}/tracker/applications.md`.
Search all rows for `job_url` in the Job Name column (markdown link href).
If a matching row is found and its `Job ID` column is `-`:
- Update that row's `Job ID` cell to `{job_id}`.
- Append to `## History` under that application's comment:
  `- {YYYY-MM-DD}: Job analysis linked → {job_id}`

## Step 4 — Score CVs

Find CVs to score:
- If `role` argument given: find `{data_dir}/cvs/{role_slug}/cv.md`
- If omitted: find all `{data_dir}/cvs/*/cv.md`
- If a role has no `cv.md`, skip it with a note: "No CV found for {role} — run /aja-review-cv first"

For each CV found, invoke `aja-match-cv` skill. Collect all score records.

Determine best CV: highest overall score.

## Step 5 — Save reports

### Short report (always saved)
Save to `{data_dir}/jobs/{job_id}/match-short.md`:

```markdown
# {role_title} at {company_name} — Match Report
**URL:** {job_url}
**Job ID:** {job_id}
**Analyzed:** {YYYY-MM-DD}

**Best CV:** {role_name} — {one sentence why}

| CV | Technical | Responsibilities | Seniority | Domain | Work Mode | Overall |
|----|-----------|-----------------|-----------|--------|-----------|---------|
| {role} | {score}% | {score}% | {score}% | {score}% | {score}% | **{score}%** |

**Red flags:** {from job.md or "None"}

Run `/aja-match-job {url} --full` for full breakdown.
```

### Full report (only if --full)
Save to `{data_dir}/jobs/{job_id}/match-full.md`:

```markdown
# {role_title} at {company_name} — Full Match Report
**URL:** {job_url}
**Job ID:** {job_id}
**Analyzed:** {YYYY-MM-DD}

---

## Best CV: {role_name}
{one sentence why}

---

## Scores
| CV | Technical | Responsibilities | Seniority | Domain | Work Mode | Overall |
|----|-----------|-----------------|-----------|--------|-----------|---------|
{rows for each CV scored}

---

## Skills Breakdown (Best CV)
**Matched (required):** {list}
**Matched (nice-to-have):** {list}
**Missing (required):** {list with severity}
**Missing (nice-to-have):** {list}

---

## Strengths to Lead With
{3-5 bullets from best CV that map to key requirements}

---

## Gaps to Address
{gaps with suggested framing for cover note}

---

## Red Flags
{from job.md}

---

## Suggested Intro Line
{one-line pitch tailored to this role and company}
```

## Step 6 — Output to terminal

Print the short report to the terminal.
If `--full`: also print the full report.

End with:
```
Saved to jobs/{job_id}/
  match-short.md {if --full: and match-full.md}
{if tracker row was found and linked: Tracker row #{tracker_id} updated — Job ID set to {job_id}}

To create a job-specific CV: /aja-job-cv {tracker_id} "{best_role}"
To prepare for interview: /aja-prep {tracker_id} hr
```
