# AJA Prep

Prepare for an upcoming interview.

## Arguments

`$ARGUMENTS` contains: `[job-number] [stage] [--html]`

- `job-number` — optional. Sequence number of the job (e.g. `2`). If omitted, an interactive picker is shown.
- `stage` — optional. `hr` | `tech` | `non-tech`. If omitted, you will be asked.
- `--html` — optional flag. If present, generate a self-contained HTML prep sheet at the end.

Examples:
```
/aja-prep
/aja-prep 2
/aja-prep 2 tech
/aja-prep 2 tech --html
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

## Step 6 — Initialize KB

Ensure the knowledge base directory exists:
```bash
mkdir -p {data_dir}/knowledge-base
```

Read `{data_dir}/knowledge-base/index.md`.
If missing, create it with this header:

```markdown
# Flashcard Knowledge Base — Index

| Category | File | Cards | Last Updated |
|----------|------|-------|--------------|
```

This ensures the KB always exists before questions are generated or saved.

## Step 7 — Generate questions

Check if `{data_dir}/jobs/{job_id}/questions-{stage}.md` exists.
If exists:
- Ask: "Use existing questions or regenerate? (existing / regenerate)"

If missing or regenerate:
- Invoke `aja-generate-questions` skill with `stage`, `job_md_path`, `cv_md_path`, `data_dir`, `job_id`.
- The skill reads the KB for existing cards, generates new ones, saves new cards to KB, and saves the question list.

After generation, print the full questions list to terminal.

## Step 8 — Generate HTML (if --html flag given)

If `--html` was passed in arguments:

Read the prep template: `{plugin_dir}/templates/prep.html`
(Plugin directory = the directory containing the `commands/` folder — go up one level.)

Combine content from:
- `{data_dir}/jobs/{job_id}/job.md` → role, company, salary, work mode
- `{data_dir}/jobs/{job_id}/company-research.md` → overview, talking points
- `{data_dir}/jobs/{job_id}/match-full.md` → scores, strengths, gaps, suggested intro
- `{data_dir}/jobs/{job_id}/questions-{stage}.md` → all questions with short answers

Fill template placeholders:
- `{{ROLE_TITLE}}` → role title from job.md
- `{{COMPANY}}` → company name
- `{{STAGE}}` → interview stage (capitalized: "HR Screen", "Technical", "Non-Technical")
- `{{DATE}}` → today's date
- `{{SALARY}}` → salary from job.md
- `{{WORK_MODE}}` → work mode + location
- `{{COMPANY_OVERVIEW}}` → Overview paragraph from company-research.md
- `{{TALKING_POINTS}}` → Talking Points bullets from company-research.md as `<li>` items
- `{{OVERALL_SCORE}}` → overall % from match-full.md
- `{{SCORE_ROWS}}` → score table rows as `<tr>` elements
- `{{STRENGTHS}}` → one `<div class="strength-item">` per bullet: `<div class="strength-label">Strength</div>{text}`
- `{{GAPS}}` → one `<div class="gap-item">` per gap: `<div class="gap-label">Gap</div>{framing text}`
- `{{INTRO_LINE}}` → Suggested Intro Line text from match-full.md
- `{{QUESTIONS}}` → each question as a collapsible card:
  ```html
  <div class="q-card">
    <div class="q-header">
      <div class="q-num">{N}</div>
      <div class="q-text">{question text}</div>
      <div class="q-toggle">▾</div>
    </div>
    <div class="q-body">
      <div class="q-short">{short answer}</div>
      <div class="q-detail">{detail answer}</div>
      <div class="q-tags">{each tag as <span class="q-tag">tag</span>}</div>
    </div>
  </div>
  ```
- `{{QUESTIONS_TO_ASK}}` → one `<div class="ask-item">` per question: `<div class="ask-num">{N}</div><div>{text}</div>`
- `{{TALKING_POINTS}}` → one `<div class="talking-point">` per bullet: `<div class="tp-icon">→</div><div class="tp-text">{text}</div>`
- `{{SCORE_ROWS}}` → one `<tr>` per dimension with score bar:
  ```html
  <tr>
    <td>{dimension}</td>
    <td><div class="score-bar-wrap"><div class="score-bar"><div class="score-bar-fill" style="width:{score}%"></div></div></div></td>
    <td>{score}%</td>
  </tr>
  ```

Save to: `{data_dir}/jobs/{job_id}/prep-{stage}.html`

Open the file: `open "{data_dir}/jobs/{job_id}/prep-{stage}.html"`

## Step 9 — Summary

Print:
```
Interview prep complete for: {role_title} at {company_name} ({stage} stage)

Files saved:
  jobs/{job_id}/company-research.md
  jobs/{job_id}/match-full.md
  jobs/{job_id}/questions-{stage}.md
  {if --html: jobs/{job_id}/prep-{stage}.html}

Good luck!
```
