# AI Job Assistant

A Claude Code plugin for managing your entire job-search workflow:
CV review, job matching, application tracking, and interview preparation.

## Features

- **CV Review** — AI HR Reviewer analyzes your CV for a target role; HR Consultant discusses improvements interactively
- **Job Matching** — Score any job posting against your CVs, get gap analysis and intro suggestions
- **Application Tracking** — Track every application with status history and a generated HTML dashboard
- **Interview Prep** — Company research, role-specific questions, and a local flashcard knowledge base
- **Post-Interview** — Update your KB and get CV improvement suggestions after each interview

## Commands

| Command | Description |
|---------|-------------|
| `/aja-review-cv [role]` | Start the CV review flow for a target role |
| `/aja-generate-cv [role]` | Generate optimized CV from review outputs |
| `/aja-generate-cv-pdf [role]` | Render CV HTML to PDF |
| `/aja-match-job <url> [role] [--full]` | Score a job posting against your CVs |
| `/aja-job-cv [number] <role>` | Create a job-specific CV variant |
| `/aja-track <url> <role> [number]` | Track a new job application |
| `/aja-update <id> <field> <value>` | Update application status |
| `/aja-dashboard` | Regenerate the HTML application dashboard |
| `/aja-prep [number] [stage]` | Prepare for an interview (stage: hr/tech/non-tech) |
| `/aja-post-interview [number] [stage]` | Run post-interview debrief and KB update |

## Installation

See [SETUP.md](./SETUP.md) for full instructions.

---

## How to Use — Step by Step

### Phase A: Get your CV reviewed and create an optimized version

This is where you start. You paste your existing CV, an AI HR Reviewer gives you an honest assessment, then an HR Consultant walks you through improvements in a conversation.

**Step 1 — Start the CV review**
```
/aja-review-cv "Senior Software Engineer"
```
The assistant will ask you to paste your CV (or provide a file path). It then runs the HR Reviewer, which produces a structured analysis: strengths, gaps, presentation issues, and recommended next steps. You'll see the executive summary immediately.

**Step 2 — Discuss improvements with the HR Consultant**
After the review, you'll be asked if you want to start the HR Consultant session. Say `yes`. The consultant walks through your CV topic by topic — one question at a time — and agrees a list of changes with you. When done, say `done` to wrap up.

**Step 3 — Generate your optimized CV**
```
/aja-generate-cv "Senior Software Engineer"
```
This applies all agreed changes and produces two files: a Markdown CV and an HTML version ready for PDF export.

**Step 4 — Export to PDF**
```
/aja-generate-cv-pdf "Senior Software Engineer"
```
Requires Google Chrome. Opens the HTML file in headless Chrome and saves a print-quality PDF.

> Repeat Steps 1–4 for each role you want a CV for (e.g. "Software Architect", "Technical Lead").

---

### Phase B: Score a job posting against your CVs

Found an interesting job? Run this before applying. It fetches the job description automatically, extracts requirements, and scores each of your CVs against it.

**Step 1 — Analyze a job posting**
```
/aja-match-job https://example.com/jobs/some-role
```
The assistant fetches the posting, identifies required skills, and produces a score table showing which of your CVs is the best fit and why. For a detailed breakdown (skills matched, gaps, suggested intro line), add `--full`:
```
/aja-match-job https://example.com/jobs/some-role --full
```

**Step 2 — Create a job-specific CV (optional)**

If you want a version of your CV tailored specifically to this posting:
```
/aja-job-cv 1 "Senior Software Engineer"
```
Use the job number shown at the end of Step 1. Or omit it entirely — the assistant will show you a list to pick from. It rewrites your summary and reorders your experience bullets to match this job's requirements.

---

### Phase C: Track your applications

Keep all your applications in one place. A Markdown table is the source of truth; an HTML dashboard is auto-generated every time you add or update an entry.

**Step 1 — Log a new application**
```
/aja-track https://example.com/jobs/some-role "Senior Software Engineer"
```
If you already ran `/aja-match-job` for this job, pass its sequence number as a third argument to link them:
```
/aja-track https://example.com/jobs/some-role "Senior Software Engineer" 1
```

**Step 2 — Update status as things progress**

When you hear back, update the relevant field:
```
/aja-update 1 response 2026-05-01
/aja-update 1 hr 2026-05-05
/aja-update 1 tech "1/2 2026-05-10"
/aja-update 1 offer pending
/aja-update 1 rejected 2026-05-20
```
The number `1` is the application ID shown when you ran `/aja-track`. Each update also appends a timestamped line to the history log.

**Step 3 — View your dashboard**

The HTML dashboard is regenerated automatically after every track/update. To regenerate it manually:
```
/aja-dashboard
```
Then open `~/JOB_ASSISTANT_DATA/tracker/applications.html` in your browser. It has filters by job name and company, and a "hide rejected" toggle.

---

### Phase D: Prepare for interviews and debrief afterwards

**Before the interview — run prep**
```
/aja-prep 1 tech
```
Use the job's sequence number (or omit it to get an interactive picker). Replace `tech` with `hr` or `non-tech` depending on the stage. The assistant will:
1. Research the company (news, tech stack, culture, salary estimates)
2. Show you a full CV-vs-job skills breakdown so you know what to emphasize
3. Generate a question list — drawing from your local knowledge base first, then creating tailored questions for this specific role

All output is saved so you can review it offline before the interview.

**After the interview — run the debrief**
```
/aja-post-interview 1 tech
```
Again, omit the number to get the interactive picker.
The assistant asks you to describe what was discussed — topics, questions you struggled with, technologies that came up. From your notes it:
1. Adds new flashcard entries to your local knowledge base so you're better prepared next time
2. Identifies CV gaps uncovered by the interview and suggests specific improvements
3. Asks if you want to update the application status (e.g. mark the tech round done)

---

## Data Structure

All user data is stored in your configured data directory (default: `~/JOB_ASSISTANT_DATA/`). The plugin repo contains no personal information.
