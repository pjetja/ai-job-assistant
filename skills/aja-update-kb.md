# AJA Update KB Skill

After an interview, update the knowledge base with new learnings and identify CV gaps.

## Input

- `interview_notes`: freeform text from user about what was discussed
- `stage`: which interview stage this was
- `job_md_path`: path to job.md (for context)
- `cv_md_path`: path to CV (for gap comparison)
- `data_dir`: path to data directory
- `job_id`: for saving output

## Step 1 — Identify new knowledge gaps

From `interview_notes`, identify:
1. **Questions asked that weren't in the KB** — topics the interviewer probed
2. **Questions the candidate struggled with** — gaps in current answers
3. **Technical topics mentioned** — technologies or concepts discussed

For each identified gap:
- Determine the appropriate category (existing or new)
- Generate a flashcard: question, short answer, detail

## Step 2 — Add to KB

Invoke `aja-flashcard-kb` with `operation: ADD_CARDS` for all new cards.

## Step 3 — Identify CV improvement suggestions

Compare what was discussed with what's in the CV:
- Skills mentioned heavily in interview but absent from CV → suggest adding
- Experience areas the interviewer focused on → suggest highlighting more

## Step 4 — Save post-interview notes

Save to `{data_dir}/jobs/{job_id}/post-interview-{stage}-{YYYY-MM-DD}.md`:

```markdown
# Post-Interview Notes: {stage} at {company_name}
**Date:** {YYYY-MM-DD}

## Topics Discussed
{Summary of main discussion areas}

## New KB Cards Added
{list: card_id — question}

## CV Improvement Suggestions
- {skill/area}: {specific suggestion for CV update}
  Suggested command: /aja-generate-cv "{role}" (after updating hr-consult.md with this change)

## Notes for Next Stage
{Any observations to prepare better for the next interview round}
```

Return the path.
