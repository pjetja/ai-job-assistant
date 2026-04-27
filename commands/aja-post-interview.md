# AJA Post Interview

Run post-interview debrief: update knowledge base and capture CV improvement suggestions.

## Arguments

`$ARGUMENTS` contains: `[job-number] [stage]`

- `job-number` — optional. Sequence number of the job (e.g. `2`). If omitted, an interactive picker is shown.
- `stage` — optional. `hr` | `tech` | `non-tech`. If omitted, you will be asked.

Examples:
```
/aja-post-interview
/aja-post-interview 2
/aja-post-interview 2 tech
```

## Config

Read config. Extract: `data_dir`.

## Step 1 — Resolve job

List all directories under `{data_dir}/jobs/` sorted alphabetically.

If the first token of `$ARGUMENTS` is a number N:
- Select the Nth directory (1-indexed). Set `job_id` to that directory name.
- Remaining token (if any) is the `stage` argument.

If the first token is not a number (or no arguments given):
- Print a numbered list:
  ```
  Jobs:
    1. Senior Software Engineer at Stripe — 2026-04-25
    2. Software Architect at Revolut — 2026-04-26
  Which job? (enter number)
  ```
  Read role title, company, and date from each job's `job.md`. Wait for user to enter a number. Set `job_id`.

If `{data_dir}/jobs/` is empty or missing: "No jobs found. Run /aja-match-job first." and stop.

Verify `{data_dir}/jobs/{job_id}/job.md` exists.

## Step 2 — Resolve stage

If stage not provided: ask "Which stage just completed? (hr / tech / non-tech)"

## Step 3 — Collect interview notes

Ask: "Please share what was discussed in the interview. Include:
- Topics and questions that came up
- Any questions you struggled with or didn't know
- Technologies or concepts the interviewer focused on
- How you felt about your answers

(Type 'done' on a new line when finished)"

Wait for user to provide notes (multi-line input until 'done').

## Step 4 — Find CV path

Find best CV for this job same way as /aja-prep Step 3.

## Step 5 — Run KB update

Invoke `aja-update-kb` skill with:
- `interview_notes`: the text entered by user
- `stage`: resolved stage
- `job_md_path`: `{data_dir}/jobs/{job_id}/job.md`
- `cv_md_path`: resolved CV path
- `data_dir`
- `job_id`

## Step 6 — Update application status

Ask: "Do you want to update the application status? (yes / no)"
If yes: ask "What status update? (e.g. 'tech 1/2 {today's date}', 'rejected 2026-05-15')"
If provided: invoke `aja-update` logic for that application row.

## Step 7 — Summary

Print:
```
Post-interview debrief complete.

New KB cards added: {N}
CV improvement suggestions: {N}

Full notes saved to:
  jobs/{job_id}/post-interview-{stage}-{date}.md

Review your CV improvement suggestions and run:
  /aja-review-cv "{role}" — to start a new CV improvement cycle
```
