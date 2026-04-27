# AJA Prep

Prepare for an upcoming interview.

## Arguments

`$ARGUMENTS` contains: `<job-id> [stage]`

- `job-id` — required. Must exist in `jobs/`.
- `stage` — optional. `hr` | `tech` | `non-tech`. If omitted, ask user.

Examples:
```
/aja-prep 007-stripe-senior-fe
/aja-prep 007-stripe-senior-fe tech
```

## Config

Read config. Extract: `data_dir`, `roles`.

## Step 1 — Resolve job and stage

Verify `{data_dir}/jobs/{job_id}/job.md` exists. If not: "Job {job_id} not found. Run /aja-match-job first."

If stage not provided:
- Ask: "Which interview stage are you preparing for? (hr / tech / non-tech)"
- Wait for response.

Validate stage is one of: `hr`, `tech`, `non-tech`.

## Step 2 — Find best CV for this job

Check if `match-short.md` exists. If yes: read best CV from it.
If not: list all `cvs/*/cv.md` and ask user which CV they are using for this job.
Derive `cv_md_path`.

## Step 3 — Company research

Check if `{data_dir}/jobs/{job_id}/company-research.md` exists.

If it exists:
- Print: "Found existing company research."
- Ask: "Use existing research or refresh? (existing / refresh)"

If missing or refresh:
- Invoke `aja-company-research` skill.
- Print a summary of the research (Overview + Talking Points sections only).

## Step 4 — Run match in full mode (if not done)

Check if `{data_dir}/jobs/{job_id}/match-full.md` exists.
If not: run the scoring portion of `aja-match-job` logic and save `match-full.md`.
Print the skills breakdown section so the candidate knows what to reinforce.

## Step 5 — Generate questions

Check if `{data_dir}/jobs/{job_id}/questions-{stage}.md` exists.
If exists:
- Ask: "Use existing questions or regenerate? (existing / regenerate)"

If missing or regenerate:
- Invoke `aja-generate-questions` skill with `stage`, `job_md_path`, `cv_md_path`, `data_dir`, `job_id`.

Print the full questions list to terminal.

## Step 6 — Summary

Print:
```
Interview prep complete for: {role_title} at {company_name} ({stage} stage)

Files saved:
  jobs/{job_id}/company-research.md
  jobs/{job_id}/match-full.md
  jobs/{job_id}/questions-{stage}.md

Good luck!
```
