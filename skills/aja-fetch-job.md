# AJA Fetch Job Skill

Fetch a job posting from a URL using multiple fallback strategies.
Returns the raw job description text (cleaned of HTML tags).

## Input

- `job_url`: the full URL of the job posting

## Strategy (try in order, stop at first success)

### Strategy 1 — JustJoin.it API

If URL contains `justjoin.it`:
```bash
SLUG=$(echo "{job_url}" | grep -oE '[^/]+$')
curl -s "https://api.justjoin.it/v1/offers/$SLUG" \
  -H "Accept: application/json" \
  -H "User-Agent: Mozilla/5.0"
```
Parse JSON: combine `title`, `companyName`, `body` (HTML), `requiredSkills`, `employmentTypes`, `workplaceType`, `city`.
Strip HTML from `body`.

### Strategy 2 — WebFetch

Use the WebFetch tool to fetch `{job_url}` directly. Works for non-SPA pages.
Extract the main content text (ignore nav, footer, sidebars).

### Strategy 3 — WebSearch fallback

Search: `site:{domain} "{job title}" requirements responsibilities`
If results return a cached/mirrored version with sufficient content, use that.

### Strategy 4 — Chrome headless

```bash
CHROME="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
"$CHROME" --headless=new --disable-gpu --dump-dom "{job_url}" 2>/dev/null \
  | sed 's/<[^>]*>//g' \
  | tr -s ' \n' '\n' \
  | grep -v '^$'
```

### All strategies fail

Return: `FETCH_FAILED` and tell the caller to ask the user to paste the job description manually.

## Output

Return the cleaned job description text. Do not save to file — the caller (aja-analyze-job) handles storage.
