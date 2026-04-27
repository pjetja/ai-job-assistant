# AJA Prep

Prepare for an upcoming interview.

## Arguments

`$ARGUMENTS` contains: `[job-number] [stage]`

- `job-number` — optional. Sequence number of the job (e.g. `2`). If omitted, an interactive picker is shown.
- `stage` — optional. `hr` | `tech` | `non-tech`. If omitted, you will be asked.

Examples:
```
/aja-prep
/aja-prep 2
/aja-prep 2 tech
```

## Config

Read config. Extract: `data_dir`, `roles`.

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

If stage not provided or not yet given:
- Ask: "Which interview stage are you preparing for? (hr / tech / non-tech)"
- Wait for response.

Validate stage is one of: `hr`, `tech`, `non-tech`.

## Step 3 — Find best CV for this job

Check if `match-short.md` exists. If yes: read best CV from it.
If not: list all `cvs/*/cv.md` and ask user which CV they are using for this job.
Derive `cv_md_path`.

## Step 4 — Company research

Check if `{data_dir}/jobs/{job_id}/company-research.md` exists.

If it exists:
- Print: "Found existing company research."
- Ask: "Use existing research or refresh? (existing / refresh)"

If missing or refresh:
- Invoke `aja-company-research` skill.
- Print a summary of the research (Overview + Talking Points sections only).

## Step 5 — Run match in full mode (if not done)

Check if `{data_dir}/jobs/{job_id}/match-full.md` exists.
If not: run the scoring portion of `aja-match-job` logic and save `match-full.md`.
Print the skills breakdown section so the candidate knows what to reinforce.

## Step 6 — Generate questions

Check if `{data_dir}/jobs/{job_id}/questions-{stage}.md` exists.
If exists:
- Ask: "Use existing questions or regenerate? (existing / regenerate)"

If missing or regenerate:
- Invoke `aja-generate-questions` skill with `stage`, `job_md_path`, `cv_md_path`, `data_dir`, `job_id`.

Print the full questions list to terminal.

## Step 7 — Summary

Print:
```
Interview prep complete for: {role_title} at {company_name} ({stage} stage)

Files saved:
  jobs/{job_id}/company-research.md
  jobs/{job_id}/match-full.md
  jobs/{job_id}/questions-{stage}.md

Good luck!
```
