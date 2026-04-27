# AJA Job CV

Generate a job-specific CV variant for a given job posting and role.

## Arguments

`$ARGUMENTS` contains: `<job-id> <role>`

Example:
```
/aja-job-cv 007-stripe-senior-fe "Senior Frontend Developer"
```

## Config

Read config as usual. Extract `data_dir`.

## Step 1 — Check prerequisites

- `{data_dir}/jobs/{job_id}/job.md` must exist. If not: "Job {job_id} not found. Run /aja-match-job first."
- `{data_dir}/cvs/{role_slug}/cv.md` must exist. If not: "No CV found for {role}. Run /aja-review-cv first."
- Read `{data_dir}/jobs/{job_id}/match-short.md` or `match-full.md` if exists (for context).

## Step 2 — Generate job-specific CV

Read:
- `{data_dir}/jobs/{job_id}/job.md` — required skills, responsibilities
- `{data_dir}/cvs/{role_slug}/cv.md` — base CV

Create a tailored variant that:
- Rewrites the Professional Summary to directly reference the company and role
- Reorders experience bullets to lead with the most relevant achievements for THIS job's responsibilities
- Highlights required skills that appear in the CV (bold them in the skills list)
- Adds a one-line intro comment at the top noting which job this was tailored for

Save to `{data_dir}/jobs/{job_id}/cv-{role_slug}.md`.

## Step 3 — Confirm

Print:
```
Job-specific CV created: jobs/{job_id}/cv-{role_slug}.md

Tailored for: {role_title} at {company_name}
Based on: cvs/{role_slug}/cv.md

To generate PDF: /aja-generate-cv-pdf from the job-specific HTML
(Note: generate-cv-pdf targets the cvs/ directory; for job CVs, generate HTML manually or add a --job flag)
```

Note to implementer: the PDF flow for job-specific CVs is intentionally deferred. The CV MD can be used directly for applications in most cases.
