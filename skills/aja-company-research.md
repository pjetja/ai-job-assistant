# AJA Company Research Skill

Research a company and produce a structured summary for interview preparation.

## Input

- `company_name`: name of the company
- `job_id`: used for saving output
- `data_dir`: path to user data directory

## Research Steps

Perform web searches for each of the following. Use WebSearch and WebFetch tools.

1. **Company overview:** `{company_name} company overview founded industry`
   - What they do, core product/service, business model, target market

2. **Recent news:** `{company_name} news 2025 2026`
   - Funding rounds, product launches, layoffs, leadership changes, acquisitions

3. **Tech stack:** `{company_name} tech stack engineering blog`
   - Languages, frameworks, infrastructure (useful for technical prep)

4. **Culture and values:** `{company_name} culture values glassdoor`
   - Work environment notes, employee reviews summary

5. **Salary range:** `{company_name} {role_title} salary {country}`
   - Use data from Glassdoor, Levels.fyi, LinkedIn Salary, or similar

## Output

Save to `{data_dir}/jobs/{job_id}/company-research.md`:

```markdown
# Company Research: {company_name}
**Date:** {YYYY-MM-DD}

## Overview
{2-3 sentences: what they do, stage, size}

## Product / Service
{Key offerings, customers, market position}

## Recent News
- {YYYY-MM-DD}: {event} — {source URL}
- (3-5 notable items, prioritize last 12 months)

## Tech Stack
{Known technologies — languages, frameworks, cloud, tools}
*Source: {engineering blog / job postings / other}*

## Culture Notes
{Summary from reviews — work-life balance, growth, management style}
*Glassdoor rating: {X}/5 ({N} reviews)*

## Salary Estimates
- **{role_title}:** {range} ({country}, {year})
- *Sources: {list}*

## Talking Points
- {One thing to mention in interview that shows you know them}
- {One genuine question to ask them}
- {One point to watch out for / red flag if any}
```

Return the path of the saved file.
