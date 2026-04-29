# AJA Dashboard

Regenerate the HTML application tracker dashboard from applications.md.

## Config

Read `~/JOB_ASSISTANT_DATA/config.md`. Extract: `data_dir`, `name`.
Source: `{data_dir}/tracker/applications.md`
Output: `{data_dir}/tracker/applications.html`

## Step 1 — Parse applications.md

Read the file. Parse all rows from the `## Applications` table (skip header and separator lines).
For each row extract: `#`, job name+url (from markdown link), company, cv, job_id, sent, response, hr, tech, nontech, offer, rejected, notes.

A row with `Rejected` column not `-` is a "rejected" row.

Compute stats:
- `total`: all rows
- `responded`: rows where Response ≠ `-`
- `active`: rows where Rejected = `-` and Offer ≠ `no`
- `in_process`: rows with HR Screen ≠ `-` or Tech ≠ `-` or NonTech ≠ `-`
- `offers`: rows where Offer = `yes` or `pending`
- `rejected_count`: rows where Rejected ≠ `-`

## Step 2 — Generate HTML

Write `applications.html` using the template below with `{{ROWS}}`, `{{TOTAL}}`, `{{STATS}}` replaced.

For each data row produce a `<tr>`:
- Add `class="rejected-row"` if Rejected ≠ `-`
- `#` → `<td class="num">{#}</td>`
- Job Name → `<td class="job"><a href="{url}" target="_blank">{title}</a></td>` or `<td class="job"><span>{title}</span></td>` if no URL
- Company → `<td class="company">{company}</td>`
- CV → `<td class="cv"><span class="badge-cv">{cv}</span></td>`
- Prep → `<td class="prep">{links}</td>` — for each `prep-{stage}.html` found in `{data_dir}/jobs/{job_id}/`, render `<a href="file://{path}" target="_blank" class="badge-prep badge-prep-{stage}">{Stage}</a>` (stages: hr → HR, tech → Tech, non-tech → HM). Empty cell if no job_id or no prep files.
- Sent → `<td class="date">{sent}</td>`
- Response → `-` → `<td class="empty">—</td>`, else `<td><span class="badge badge-responded">{value}</span></td>`
- HR Screen → same pattern, class `badge-hr`
- Tech Interview → same, class `badge-tech`
- Non-Tech → same, class `badge-nontech`
- Offer: `yes` → `badge-offer-yes`, `pending` → `badge-offer-pending`, `no` → `badge-offer-no`, `-` → empty
- Rejected → `-` → empty, else `<td><span class="badge badge-rejected">{value}</span></td>`
- Notes → `<td class="notes">{notes or "—"}</td>`

## HTML Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Job Applications — {{NAME}}</title>
<style>
*, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: 'Segoe UI', 'Helvetica Neue', Arial, sans-serif; background: #f0f2f5; color: #333; }
header { background: #2d3a4a; color: #fff; padding: 16px 28px; display: flex; align-items: center; gap: 20px; flex-wrap: wrap; }
header h1 { font-size: 18px; font-weight: 700; }
.stats { display: flex; gap: 12px; flex-wrap: wrap; margin-left: auto; }
.stat { background: rgba(255,255,255,0.1); border-radius: 6px; padding: 4px 12px; font-size: 12px; }
.stat strong { font-size: 15px; margin-right: 4px; }
.controls { display: flex; gap: 12px; align-items: center; padding: 12px 28px; background: #fff; border-bottom: 1px solid #e5e7eb; flex-wrap: wrap; }
.controls input { border: 1px solid #d1d5db; border-radius: 6px; padding: 5px 10px; font-size: 13px; outline: none; }
.controls input:focus { border-color: #2d3a4a; }
.controls label { font-size: 13px; color: #555; display: flex; align-items: center; gap: 6px; cursor: pointer; }
.wrap { padding: 20px 28px; overflow-x: auto; }
table { width: 100%; border-collapse: collapse; background: #fff; border-radius: 8px; overflow: hidden; box-shadow: 0 1px 4px rgba(0,0,0,0.08); font-size: 12.5px; }
thead tr { background: #2d3a4a; color: #fff; }
thead th { padding: 9px 12px; text-align: left; font-weight: 600; font-size: 11px; text-transform: uppercase; letter-spacing: 0.6px; white-space: nowrap; }
tbody tr { border-bottom: 1px solid #f0f2f5; transition: background 0.12s; }
tbody tr:hover { background: #f7f9fb; }
tbody tr.rejected-row { opacity: 0.4; }
tbody tr.rejected-row.hidden { display: none; }
td { padding: 8px 12px; vertical-align: middle; }
td.num { color: #aaa; font-size: 11px; width: 30px; }
td.job a, td.job span { color: #2d3a4a; font-weight: 600; text-decoration: none; font-size: 13px; }
td.job a:hover { text-decoration: underline; }
td.company { color: #555; }
td.date { color: #777; white-space: nowrap; font-size: 12px; }
td.empty { color: #ccc; }
td.notes { color: #666; font-size: 11.5px; max-width: 160px; }
.badge-cv { display: inline-block; background: #e8edf2; color: #2d3a4a; font-size: 10.5px; font-weight: 700; padding: 2px 7px; border-radius: 4px; white-space: nowrap; }
.badge { display: inline-block; font-size: 11px; font-weight: 600; padding: 2px 8px; border-radius: 20px; white-space: nowrap; }
.badge-responded { background: #dbeafe; color: #1d4ed8; }
.badge-hr { background: #fef3c7; color: #92400e; }
.badge-tech { background: #ede9fe; color: #5b21b6; }
.badge-nontech { background: #fce7f3; color: #9d174d; }
.badge-offer-yes { background: #dcfce7; color: #166534; }
.badge-offer-pending { background: #fff7ed; color: #c2410c; }
.badge-offer-no { background: #fee2e2; color: #991b1b; }
.badge-rejected { background: #fee2e2; color: #991b1b; }
.badge-prep { display: inline-block; font-size: 10px; font-weight: 700; padding: 2px 7px; border-radius: 4px; text-decoration: none; margin-right: 4px; }
.badge-prep-hr { background: #fef3c7; color: #92400e; }
.badge-prep-tech { background: #ede9fe; color: #5b21b6; }
.badge-prep-non-tech { background: #fce7f3; color: #9d174d; }
</style>
</head>
<body>
<header>
  <h1>Job Applications</h1>
  <div class="stats">{{STATS}}</div>
</header>
<div class="controls">
  <input type="text" id="filterJob" placeholder="Filter by job name..." oninput="applyFilters()">
  <input type="text" id="filterCompany" placeholder="Filter by company..." oninput="applyFilters()">
  <label><input type="checkbox" id="hideRejected" onchange="applyFilters()"> Hide rejected</label>
</div>
<div class="wrap">
  <table id="appTable">
    <thead>
      <tr>
        <th>#</th><th>Job Name</th><th>Company</th><th>CV</th><th>Prep</th>
        <th>Sent</th><th>Response</th><th>HR Screen</th><th>Tech Interview</th>
        <th>Non-Tech</th><th>Offer</th><th>Rejected</th><th>Notes</th>
      </tr>
    </thead>
    <tbody>{{ROWS}}</tbody>
  </table>
</div>
<script>
function applyFilters() {
  const job = document.getElementById('filterJob').value.toLowerCase();
  const company = document.getElementById('filterCompany').value.toLowerCase();
  const hideRej = document.getElementById('hideRejected').checked;
  document.querySelectorAll('#appTable tbody tr').forEach(function(tr) {
    const jobText = (tr.cells[1]?.textContent || '').toLowerCase();
    const compText = (tr.cells[2]?.textContent || '').toLowerCase();
    const isRejected = tr.classList.contains('rejected-row');
    const show = jobText.includes(job) && compText.includes(company) && !(hideRej && isRejected);
    tr.style.display = show ? '' : 'none';
  });
}
</script>
</body>
</html>
```

`{{STATS}}` is replaced with:
```html
<div class="stat"><strong>{total}</strong> applied</div>
<div class="stat"><strong>{responded}</strong> responded</div>
<div class="stat"><strong>{in_process}</strong> in process</div>
<div class="stat"><strong>{offers}</strong> offers</div>
<div class="stat"><strong>{rejected_count}</strong> rejected</div>
```

## Step 3 — Confirm

Print: `Dashboard regenerated: {data_dir}/tracker/applications.html ({total} applications)`
Print: `Open with: open "{data_dir}/tracker/applications.html"`
