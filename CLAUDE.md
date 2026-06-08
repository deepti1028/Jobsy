# Jobsy — Claude Code Instructions

## What this project is
A static GitHub Pages site that tracks companies hiring remotely.
- `index.html` — the full app (loads data, renders cards, handles search/filter/add/edit/delete)
- `data.json` — the source of truth for all company data
- Hosted at: https://deepti1028.github.io/Jobsy/

## How to add companies from a LinkedIn post

The user will paste a list of companies with lnkd.in short URLs copied from a LinkedIn post. Here's exactly what to do:

### Step 1 — Resolve URLs
Run a curl loop to follow all lnkd.in redirects and get the final careers page URLs:
```bash
cd /Users/deeptijain/Desktop/Deepti/Projects/Jobsy
# For each company:
curl -sL --max-time 8 -o /dev/null -w "%{url_effective}" "https://lnkd.in/XXXXX"
```
- Some URLs resolve cleanly to careers pages
- Some stay as lnkd.in (LinkedIn blocked) → use knowledge of the company to find the actual careers URL
- Some resolve to a homepage (e.g. symetra.com) → append /careers or /about-us/careers from knowledge

### Step 2 — Deduplicate
Read `data.json` and check: skip any company whose `name` OR `url` already exists (case-insensitive, ignoring www/http/trailing slash).

### Step 3 — Add to data.json
Append new entries to the `companies` array. Each entry format:
```json
{
  "id": "slugified-company-name",
  "name": "Company Name",
  "industry": "Technology|Finance|Education|Healthcare|E-Commerce|Marketing|Media|Design|Other",
  "remoteType": "Fully Remote",
  "url": "https://resolved-careers-url.com",
  "notes": "One sentence about what the company does.",
  "dateAdded": "YYYY-MM-DD",
  "linkedinPostUrl": "https://www.linkedin.com/posts/..."
}
```
- `remoteType` is almost always `"Fully Remote"` for posts titled "100% remote"
- `linkedinPostUrl` = the full LinkedIn post URL the user shared (strip UTM params if messy)
- Update `lastUpdated` at the top of data.json to today's date

### Step 4 — Commit and push
```bash
git add data.json
git commit -m "Add N companies from LinkedIn post ([author name])"
git push origin main
```

## How to tell Claude to do this in a new session

Just say:
> "Here's a LinkedIn post content, add these companies to Jobsy"

Then paste the company list. Claude will handle the rest.

Or if you have a LinkedIn URL:
> "Scrape this LinkedIn post and add companies to Jobsy: [URL]"

Claude will use Firecrawl to scrape (note: LinkedIn blocks Firecrawl directly, so it falls back to web search + parsing the snippet).

## Data.json field reference
| Field | Description |
|-------|-------------|
| `id` | Lowercase slug, e.g. `"cash-app"` |
| `name` | Display name |
| `industry` | One of the 9 categories above |
| `remoteType` | `"Fully Remote"` / `"Remote-First"` / `"Remote-Friendly"` |
| `url` | Direct link to careers/jobs page (not homepage) |
| `notes` | One sentence — what the company does |
| `dateAdded` | ISO date `YYYY-MM-DD` |
| `linkedinPostUrl` | Source LinkedIn post URL (or `null` if added manually) |

## Repo path
`/Users/deeptijain/Desktop/Deepti/Projects/Jobsy`

## GitHub
https://github.com/deepti1028/Jobsy
