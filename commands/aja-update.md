# AJA Update Application

Update the status of a tracked application.

## Arguments

`$ARGUMENTS` contains: `<id> <field> <value>`

Supported fields (case-insensitive, partial match accepted):
- `response` — date of first response (YYYY-MM-DD)
- `hr` — date of HR screen (YYYY-MM-DD)
- `tech` — tech interview entry: `{n}/{total} {YYYY-MM-DD}` (e.g. `1/3 2026-05-01`)
- `nontech` — non-tech interview: `{n}/{total} {YYYY-MM-DD}`
- `offer` — `pending` / `yes` / `no`
- `rejected` — date of rejection (YYYY-MM-DD)
- `notes` — free text appended to Notes column

Examples:
```
/aja-update 3 response 2026-05-01
/aja-update 3 hr 2026-05-05
/aja-update 3 tech 1/3 2026-05-10
/aja-update 3 offer pending
/aja-update 3 rejected 2026-05-20
/aja-update 3 notes "Great culture fit discussion"
```

## Config

Read `~/JOB_ASSISTANT_DATA/config.md`. Extract: `data_dir`.
Tracker file: `{data_dir}/tracker/applications.md`

## Step 1 — Parse arguments

First token = ID (integer).
Second token = field name (match to column using aliases above).
Remaining tokens = value.

## Step 2 — Find and update row

Read `applications.md`. Locate row where first column matches `{id}`.
If not found: "Application #{id} not found." and stop.

Map field to column:
- `response` → `Response`
- `hr` → `HR Screen`
- `tech` → `Tech Interview`
- `nontech` → `Non-Tech Interview`
- `offer` → `Offer`
- `rejected` → `Rejected`
- `notes` → `Notes` (append with `; ` separator if not empty)

Update that cell value. Write file.

## Step 3 — Append to history

Add to `## History` under the comment for this application ID:
```
- {YYYY-MM-DD today}: {field} → {value}
```

## Step 4 — Regenerate HTML

Run the dashboard generation from `aja-dashboard.md`.

## Step 5 — Confirm

Print the updated row so the user can verify.
