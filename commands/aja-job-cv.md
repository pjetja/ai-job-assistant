# AJA Job CV

Generate a job-specific CV variant for a given job posting and role.

## Arguments

`$ARGUMENTS` contains: `[job-number] <role>`

- `job-number` — optional. The sequence number of the job (e.g. `2`). If omitted, an interactive picker is shown.
- `role` — required. Which CV to base the variant on.

Examples:
```
/aja-job-cv "Software Architect"
/aja-job-cv 2 "Software Architect"
```

## Config

Read config as usual. Extract `data_dir`.

## Step 1 — Resolve job

List all directories under `{data_dir}/jobs/` sorted alphabetically (zero-padded names give chronological order).

If the first token of `$ARGUMENTS` is a number N:
- Select the Nth directory (1-indexed). That directory name is `job_id`.
- Remaining tokens are the `role` argument.

If the first token is not a number (or no argument given):
- Print a numbered list:
  ```
  Jobs:
    1. Senior Software Engineer at Stripe — 2026-04-25
    2. Software Architect at Revolut — 2026-04-26
  Which job? (enter number)
  ```
  Read role title and company from each job's `job.md`. Wait for user to enter a number. Set `job_id` to the selected directory name.
- If `role` was not already in `$ARGUMENTS`, ask: "Which role CV to use?" and list available `cvs/*/cv.md`.

If `{data_dir}/jobs/` is empty or missing: "No jobs found. Run /aja-match-job first." and stop.

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
