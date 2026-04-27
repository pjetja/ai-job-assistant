# AJA Match CV Skill

Score a single CV against a structured job analysis.
Returns a score record (not saved to file — the caller aggregates and saves).

## Input

- `job_md_path`: path to `job.md` (from aja-analyze-job)
- `cv_md_path`: path to a `cv.md` for one role
- `role_name`: display name of this CV's role (e.g. "Senior Frontend Developer")

## Scoring

Read both files. Score across these dimensions (0–100 each):

| Dimension | Weight | What to assess |
|-----------|--------|---------------|
| Technical skills | 35% | % of required skills present; bonus for nice-to-haves |
| Responsibilities | 30% | How closely CV bullets map to listed responsibilities |
| Seniority/experience | 20% | Years, ownership level, scope described in CV |
| Domain fit | 10% | Industry/product type alignment |
| Work mode | 5% | Remote/hybrid/location compatibility |

**Overall** = weighted average of the five dimensions.

For each dimension, note:
- **Matched:** specific items from CV that satisfy this dimension
- **Missing:** specific requirements not addressed

## Output Format

Return a structured record (in-memory, not saved):

```
role_name: {role_name}
technical: {score}
responsibilities: {score}
seniority: {score}
domain: {score}
work_mode: {score}
overall: {weighted_average}
matched_required: [list of matched required skills]
matched_nice: [list of matched nice-to-haves]
missing_required: [list with severity: critical/moderate/minor]
missing_nice: [list]
strengths: [3-5 specific CV bullets that map to key requirements]
gaps: [specific gaps with suggested framing]
```
