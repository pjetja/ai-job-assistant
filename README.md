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
| `/aja-job-cv <job-id> <role>` | Create a job-specific CV variant |
| `/aja-track <url> <role>` | Track a new job application |
| `/aja-update <id> <field> <value>` | Update application status |
| `/aja-dashboard` | Regenerate the HTML application dashboard |
| `/aja-prep <job-id> [stage]` | Prepare for an interview (stage: hr/tech/non-tech) |
| `/aja-post-interview <job-id>` | Run post-interview debrief and KB update |

## Installation

See [SETUP.md](./SETUP.md) for full instructions.

## Data Structure

All user data is stored in your configured data directory (default: `~/JOB_ASSISTANT_DATA/`). The plugin repo contains no personal information.
