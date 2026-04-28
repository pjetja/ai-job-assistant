# AJA Job CV

Generate a job-specific CV variant for a given job posting and role.

## Arguments

`$ARGUMENTS` contains: `[tracker-id] <role>`

- `tracker-id` — optional. The `#` from the tracker table (e.g. `40`). If omitted, an interactive picker is shown.
- `role` — required. Which CV to base the variant on.

Examples:
```
/aja-job-cv "Software Architect"
/aja-job-cv 40 "Software Architect"
```

## Config

Read config as usual. Extract `data_dir`.

## Step 1 — Resolve job

Read `{data_dir}/tracker/applications.md`. If missing: "No applications found. Run /aja-track first." and stop.

If the first token of `$ARGUMENTS` is a number N:
- Find the row where `#` column equals N. If not found: "Application #N not found in tracker." and stop.
- Extract `job_url` from the markdown link in the Job Name column.
- Extract `job_id` from the Job ID column (may be `-`).
- Remaining tokens are the `role` argument.

If the first token is not a number (or no argument given):
- Print all non-rejected tracker rows (Rejected column = `-`):
  ```
  Applications:
    2. Senior Frontend AI Developer at Remodevs — sent 2026-04-08
    26. FE Software Engineer at Bnewable BV — sent 2026-04-09
  Which application? (enter #)
  ```
  Wait for user to enter a number. Extract `job_url` and `job_id` from that row.
- If `role` was not already in `$ARGUMENTS`, ask: "Which role CV to use?" and list available `cvs/*/cv.md`.

If `job_id` is `-`:
- Run `aja-match-job` inline: fetch `job_url`, create the job directory, score CVs, save match reports.
- `aja-match-job` will update the tracker row's `Job ID` column automatically (Step 3b).
- Re-read the tracker row to get the updated `job_id`.

## Step 2 — Check prerequisites

- `{data_dir}/jobs/{job_id}/job.md` must exist.
- `{data_dir}/cvs/{role_slug}/cv.md` must exist. If not: "No CV found for {role}. Run /aja-review-cv first."
- Read `{data_dir}/jobs/{job_id}/match-short.md` or `match-full.md` if exists (for context).

## Step 3 — Generate job-specific CV

Read:
- `{data_dir}/jobs/{job_id}/job.md` — required skills, responsibilities
- `{data_dir}/cvs/{role_slug}/cv.md` — base CV

Create a tailored variant that:
- Rewrites the Professional Summary to directly reference the company and role
- Reorders experience bullets to lead with the most relevant achievements for THIS job's responsibilities
- Highlights required skills that appear in the CV (bold them in the skills list)
- Adds a one-line intro comment at the top noting which job this was tailored for

Save to `{data_dir}/jobs/{job_id}/cv-{role_slug}.md`.

## Step 4 — Confirm

Print:
```
Job-specific CV created: jobs/{job_id}/cv-{role_slug}.md

Tailored for: {role_title} at {company_name}
Based on: cvs/{role_slug}/cv.md

To generate PDF: /aja-generate-cv-pdf from the job-specific HTML
(Note: generate-cv-pdf targets the cvs/ directory; for job CVs, generate HTML manually or add a --job flag)
```

Note to implementer: the PDF flow for job-specific CVs is intentionally deferred. The CV MD can be used directly for applications in most cases.
