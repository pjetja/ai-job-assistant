# Changelog

## v0.4.0 — Phase D: Interview Prep + Post-Interview
- `/aja-prep` — full interview preparation flow (company research, CV match, question generation)
- `/aja-post-interview` — post-interview debrief, KB update, CV gap suggestions
- `aja-company-research` skill — web research: overview, news, tech stack, culture, salary
- `aja-generate-questions` skill — stage-specific questions from KB + tailored for role
- `aja-flashcard-kb` skill — read/write/search local flashcard knowledge base (4 operations)
- `aja-update-kb` skill — post-interview KB update and CV improvement capture

## v0.3.0 — Phase C: Application Tracking
- `/aja-track` — log new application with duplicate detection and auto-dashboard refresh
- `/aja-update` — update application status (response, HR, tech, non-tech, offer, rejected, notes)
- `/aja-dashboard` — regenerate HTML dashboard from applications.md with filters and stats

## v0.2.0 — Phase B: Job Matching
- `/aja-match-job` — fetch job posting, analyze requirements, score all CVs, short + full reports
- `/aja-job-cv` — generate job-specific CV variant tailored to a posting
- `aja-fetch-job` skill — multi-strategy fetcher (JustJoin API, WebFetch, WebSearch, Chrome headless)
- `aja-analyze-job` skill — extract structured requirements from raw job text
- `aja-match-cv` skill — score one CV against job requirements (35/30/20/10/5 weighted dimensions)

## v0.1.0 — Phase A: Foundation + CV Review
- Plugin scaffold (README, SETUP, CHANGELOG, config template)
- `templates/cv.html` — base HTML template for CV rendering
- `/aja-review-cv` — HR Reviewer analysis + HR Consultant interactive dialogue
- `/aja-generate-cv` — optimized CV generation (MD + HTML)
- `/aja-generate-cv-pdf` — Chrome headless HTML to PDF rendering
- `aja-hr-reviewer` skill — senior HR analysis with gap severity ratings
- `aja-hr-consultant` skill — 7-topic interactive CV improvement dialogue
