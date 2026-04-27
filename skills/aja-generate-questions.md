# AJA Generate Questions Skill

Generate interview questions for a specific stage, using the local KB first.

## Input

- `stage`: `hr` | `tech` | `non-tech`
- `job_md_path`: path to `job.md` (for role/company context)
- `cv_md_path`: path to the best-match CV's `cv.md`
- `data_dir`: path to data directory
- `job_id`: for saving output

## Step 1 — Load context

Read `job.md` (required skills, responsibilities, company).
Read `cv.md` (candidate's background for tailoring questions).

## Step 2 — Check KB for existing questions

Invoke `aja-flashcard-kb` with `operation: READ_INDEX`.
If `EMPTY_KB`: skip to Step 3.

Based on the job's required skills and stage, determine relevant categories:
- `hr` stage: Leadership, Behavioral, General, any soft-skill categories
- `tech` stage: all technical skill categories matching required skills (React, TypeScript, etc.)
- `non-tech` stage: Leadership, Behavioral, Problem Solving, Culture Fit

For each relevant category: invoke `aja-flashcard-kb` with `operation: READ_CATEGORY`.
Collect all matching flashcards.

## Step 3 — Generate missing questions

For questions NOT already in the KB, generate new ones tailored to:
- The specific role and company
- The stage of the interview
- The candidate's background (to anticipate follow-ups based on their CV)

Generate questions in flashcard format:
```
{question text}
Short: {1-2 sentence answer}
Detail: {fuller explanation with examples}
Tags: {skill/category tags}
```

## Step 4 — Save new questions to KB

For all newly generated questions: invoke `aja-flashcard-kb` with `operation: ADD_CARDS`.

## Step 5 — Save question list for this interview

Save to `{data_dir}/jobs/{job_id}/questions-{stage}.md`:

```markdown
# Interview Questions: {role_title} at {company_name}
**Stage:** {stage}
**Date:** {YYYY-MM-DD}

## Likely Questions

### From Knowledge Base
{Group by category. Each question: ID, question text, short answer}

### Tailored for This Role
{Questions specific to this company/role — not yet in KB}

## Questions to Ask Them
1. {question about team/role/company — something you genuinely want to know}
2. {question about tech stack / process}
3. {question about growth / expectations}
```

Return path of saved file.
