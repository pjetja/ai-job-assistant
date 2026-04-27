# AJA Generate CV PDF

Render a CV HTML file to PDF using Chrome headless.

## Arguments

`$ARGUMENTS` contains: `[role]`

If omitted: list roles that have a `cv.html` and ask user to choose.

Examples:
```
/aja-generate-cv-pdf
/aja-generate-cv-pdf "Senior Frontend Developer"
/aja-generate-cv-pdf all
```

If `all` is passed: render PDFs for every role that has a `cv.html`.

## Config

Read `~/JOB_ASSISTANT_DATA/config.md` (or `$AJA_DATA_DIR/config.md`).
Extract: `data_dir`.

## Step 1 — Resolve role(s)

If argument is `all`: collect all directories under `cvs/` that contain a `cv.html`.
Otherwise: resolve single role slug as usual.

## Step 2 — Check Chrome

```bash
CHROME="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
if [ ! -f "$CHROME" ]; then
  echo "Chrome not found at $CHROME"
  echo "Please install Google Chrome or update the CHROME path in this command."
  exit 1
fi
```

## Step 3 — Render each PDF

For each role to render:

```bash
CHROME="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
DATA_DIR="{data_dir}"
ROLE_DIR="$DATA_DIR/cvs/{role_slug}"
HTML="$ROLE_DIR/cv.html"
PDF="$ROLE_DIR/cv.pdf"

"$CHROME" \
  --headless=new \
  --disable-gpu \
  --no-pdf-header-footer \
  --print-to-pdf="$PDF" \
  "file://$HTML"
```

After each render:
- Check the PDF was created: `ls -lh "$PDF"`
- Print: `✓ {role_slug}/cv.pdf — {file_size}`
- If Chrome exits non-zero: print the error and continue to next role.

## Step 4 — Summary

Print all generated PDFs and their paths.
If any failed, list them clearly.
