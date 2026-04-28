# AJA Post Interview

Run post-interview debrief: update knowledge base and capture CV improvement suggestions.

## Arguments

`$ARGUMENTS` contains: `[tracker-id] [stage]`

- `tracker-id` — optional. The `#` from the tracker table (e.g. `40`). If omitted, an interactive picker is shown.
- `stage` — optional. `hr` | `tech` | `non-tech`. If omitted, you will be asked.

Examples:
```
/aja-post-interview
/aja-post-interview 40
/aja-post-interview 40 tech
```

## Config

Read config. Extract: `data_dir`.

## Step 1 — Resolve job

Read `{data_dir}/tracker/applications.md`. If missing: "No applications found. Run /aja-track first." and stop.

If the first token of `$ARGUMENTS` is a number N:
- Find the row where `#` column equals N. If not found: "Application #N not found in tracker." and stop.
- Extract `job_url` from the markdown link in the Job Name column.
- Extract `job_id` from the Job ID column (may be `-`).
- Remaining token (if any) is the `stage` argument.

If the first token is not a number (or no arguments given):
- Print all non-rejected tracker rows (Rejected column = `-`):
  ```
  Applications:
    2. Senior Frontend AI Developer at Remodevs — sent 2026-04-08
    26. FE Software Engineer at Bnewable BV — sent 2026-04-09
  Which application? (enter #)
  ```
  Wait for user to enter a number. Extract `job_url` and `job_id` from that row.

If `job_id` is `-`:
- Run `aja-match-job` inline: fetch `job_url`, create the job directory, score CVs, save match reports.
- `aja-match-job` will update the tracker row's `Job ID` column automatically (Step 3b).
- Re-read the tracker row to get the updated `job_id`.

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
