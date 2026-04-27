# AJA Analyze Job Skill

Parse raw job description text and extract structured requirements.
Saves a structured `job.md` file.

## Input

- `job_text`: raw text of the job posting
- `job_url`: original URL
- `job_id`: the ID assigned by the caller (e.g. `001-stripe-senior-fe`)
- `data_dir`: path to user's data directory

## Extraction

From the job text, extract:

| Field | Notes |
|-------|-------|
| `role_title` | Exact title as written |
| `seniority` | Junior / Mid / Senior / Lead / Principal / Director |
| `company_name` | Hiring company |
| `industry` | Best guess from context |
| `required_skills` | Hard requirements — list each skill individually |
| `nice_to_have` | Explicitly marked as optional/bonus |
| `years_experience` | Required years, if stated |
| `responsibilities` | Bulleted list from job posting |
| `work_mode` | remote / hybrid / on-site |
| `location` | City/country if stated |
| `salary_range` | If disclosed |
| `red_flags` | Stack mismatch, unrealistic requirements, vague description, etc. |
| `company_notes` | Any info about company stage, size, culture mentioned |

## Output

Save to `{data_dir}/jobs/{job_id}/job.md`:

```markdown
# {role_title} at {company_name}
**URL:** {job_url}
**Analyzed:** {YYYY-MM-DD}
**Job ID:** {job_id}

## Company
- **Name:** {company_name}
- **Industry:** {industry}
- **Notes:** {company_notes}

## Role
- **Title:** {role_title}
- **Seniority:** {seniority}
- **Work mode:** {work_mode}
- **Location:** {location}
- **Salary:** {salary_range or "not disclosed"}
- **Experience required:** {years_experience or "not stated"}

## Required Skills
{comma-separated list}

## Nice-to-Have Skills
{comma-separated list}

## Responsibilities
{bulleted list}

## Red Flags
{bulleted list or "None"}
```

Return the path of the saved file.
