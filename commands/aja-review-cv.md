# AJA Review CV

Run the full CV review flow for a target role: HR Reviewer analysis followed by HR Consultant dialogue.

## Arguments

`$ARGUMENTS` contains: `[role]`

- `role` is optional. If omitted, list available roles from config and ask the user to choose.

Examples:
```
/aja-review-cv
/aja-review-cv "Senior Frontend Developer"
```

## Config

Read `~/JOB_ASSISTANT_DATA/config.md` (or `$AJA_DATA_DIR/config.md` if env var is set).
Extract: `name`, `email`, `data_dir`, `roles` list.
All paths below are relative to `data_dir` unless stated.

If config file not found: respond with "Config not found. Please follow SETUP.md to create ~/JOB_ASSISTANT_DATA/config.md." and stop.

## Step 1 — Resolve role

If `$ARGUMENTS` is empty:
- Print the roles list from config.
- Ask: "Which role do you want to review your CV for? (type the number or full name)"
- Wait for the user's response.

Validate the chosen role is in the config roles list. If not, say so and list valid roles.

Derive `role_slug`: lowercase the role name, replace spaces with hyphens, remove special characters.
Example: "Senior Frontend Developer" → `senior-frontend-developer`

## Step 2 — Check for existing source CV

Check if `cvs/{role_slug}/source-cv.md` exists.

If it exists:
- Print: "Found existing source CV at cvs/{role_slug}/source-cv.md"
- Ask: "Use existing source CV, or replace it? (existing / replace)"

If it does not exist (or user chooses replace):
- Print: "Please paste your current CV content below, or provide the path to your CV file."
- Wait for user input.
- If a file path is given: read that file's content.
- Save the content to `cvs/{role_slug}/source-cv.md`. Create the directory if needed.

## Step 3 — HR Reviewer

Read `cvs/{role_slug}/source-cv.md`.

Invoke the `aja-hr-reviewer` skill with:
- `target_role`: the resolved role name
- `cv_content`: content of source-cv.md
- `name`: from config

The skill saves its output to `cvs/{role_slug}/hr-review.md`.

Print: "HR Review complete. Saved to cvs/{role_slug}/hr-review.md"
Print a short summary (Executive Summary section only) so the user sees the headline result.

## Step 4 — HR Consultant

Ask: "Ready to discuss your CV with the HR Consultant? (yes / skip)"

If yes: invoke the `aja-hr-consultant` skill with:
- `hr_review_path`: `cvs/{role_slug}/hr-review.md`
- `cv_content`: content of source-cv.md
- `target_role`: role name

The skill runs the interactive dialogue and saves output to `cvs/{role_slug}/hr-consult.md`.

If skip: print "Skipping HR Consultant. You can run /aja-review-cv again to do the consultation later."

## Step 5 — Next step prompt

Print:
```
Review flow complete for: {role_name}

Files saved:
  cvs/{role_slug}/hr-review.md    — HR Reviewer analysis
  cvs/{role_slug}/hr-consult.md   — Agreed changes (if consultation done)

Next: run /aja-generate-cv "{role_name}" to create your optimized CV.
```
