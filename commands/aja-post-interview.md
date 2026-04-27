# AJA Post Interview

Run post-interview debrief: update knowledge base and capture CV improvement suggestions.

## Arguments

`$ARGUMENTS` contains: `<job-id> [stage]`

Examples:
```
/aja-post-interview 007-stripe-senior-fe
/aja-post-interview 007-stripe-senior-fe tech
```

## Config

Read config. Extract: `data_dir`.

## Step 1 — Resolve job and stage

Verify `{data_dir}/jobs/{job_id}/job.md` exists.
If stage not provided: ask "Which stage just completed? (hr / tech / non-tech)"

## Step 2 — Collect interview notes

Ask: "Please share what was discussed in the interview. Include:
- Topics and questions that came up
- Any questions you struggled with or didn't know
- Technologies or concepts the interviewer focused on
- How you felt about your answers

(Type 'done' on a new line when finished)"

Wait for user to provide notes (multi-line input until 'done').

## Step 3 — Find CV path

Find best CV for this job same way as /aja-prep Step 2.

## Step 4 — Run KB update

Invoke `aja-update-kb` skill with:
- `interview_notes`: the text entered by user
- `stage`: resolved stage
- `job_md_path`: `{data_dir}/jobs/{job_id}/job.md`
- `cv_md_path`: resolved CV path
- `data_dir`
- `job_id`

## Step 5 — Update application status

Ask: "Do you want to update the application status? (yes / no)"
If yes: ask "What status update? (e.g. 'tech 1/2 {today's date}', 'rejected 2026-05-15')"
If provided: invoke `aja-update` logic for that application row.

## Step 6 — Summary

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
