# AJA Generate CV

Generate an optimized CV (MD + HTML) for a target role using HR review and consultation outputs.

## Arguments

`$ARGUMENTS` contains: `[role]`

Examples:
```
/aja-generate-cv
/aja-generate-cv "Senior Frontend Developer"
```

## Config

Read `~/JOB_ASSISTANT_DATA/config.md` (or `$AJA_DATA_DIR/config.md`).
Extract: `name`, `email`, `data_dir`, `roles`.

## Step 1 — Resolve role

Same as in /aja-review-cv Step 1. Derive `role_slug`.

## Step 2 — Check prerequisites

Check these files exist:
- `cvs/{role_slug}/source-cv.md` — original CV
- `cvs/{role_slug}/hr-review.md` — reviewer output

If either is missing: print "Missing prerequisites for {role_slug}. Run /aja-review-cv first." and stop.

Check if `cvs/{role_slug}/hr-consult.md` exists. If not, warn:
"No HR Consultant session found. Generating CV from HR Review only (no agreed changes). Run /aja-review-cv to do the consultation."
Ask: "Continue without consultation? (yes / no)"
If no: stop.

## Step 3 — Generate CV MD

Read all three input files (whichever exist).

Using the source CV as the base, apply all changes listed in `hr-consult.md` under "Agreed Changes".
Incorporate insights from `hr-review.md` (especially strong points to highlight and gaps to address).

The output is a complete, polished CV in markdown. Rules:
- Tailor the Professional Summary for the target role
- Reorder/rewrite experience bullets to emphasize role-relevant achievements
- Update skills section per agreed changes
- Keep GDPR footer if present in source CV
- Length: 1–2 pages equivalent (aim for concise)

Save to `cvs/{role_slug}/cv.md`.

## Step 4 — Generate HTML

Read `templates/cv.html` from the plugin directory.
(Plugin directory is the directory containing this commands/ folder — go up one level.)

Fill in template placeholders using data from `cv.md` and config:
- `{{NAME}}` → name from config
- `{{ROLE}}` → target role name
- `{{EMAIL}}` → email from config
- `{{PHONE}}`, `{{LOCATION}}`, `{{LINKEDIN}}`, `{{GITHUB}}` → from source CV or ask user if not found
- `{{SUMMARY}}` → Professional Summary from cv.md
- `{{EXPERIENCE}}` → each role as `<div class="job-entry">` with title, company, date, bullets
- `{{SKILLS}}` → each skill as `<li>skill</li>` in the skills-list
- `{{EDUCATION}}` → education section
- `{{GDPR}}` → GDPR footer text if present

Save to `cvs/{role_slug}/cv.html`.

## Step 5 — Confirm

Print:
```
CV generated for: {role_name}

Files saved:
  cvs/{role_slug}/cv.md     — Markdown source
  cvs/{role_slug}/cv.html   — HTML (for PDF rendering)

Next: run /aja-generate-cv-pdf "{role_name}" to create the PDF.
```
