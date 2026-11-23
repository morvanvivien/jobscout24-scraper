# How to Scrape JobScout24 Jobs: Swiss Job Postings by Canton, Work Load and Full Descriptions (JobScout24 Jobs API)

> **Run it now, no code: [jobscout24-jobs-scraper on the Apify Store](https://apify.com/bibian/jobscout24-jobs-scraper)** &nbsp;|&nbsp; $0.002 per job posting ($2.00 per 1,000) &nbsp;|&nbsp; no login, no proxy to set up

JobScout24 (jobscout24.ch) is one of Switzerland's large job boards, in German, French and English: 80,782 Stellenangebote announced on 22 November 2026, from nursing, trades and logistics to IT, banking and temporary staffing. There is no public feed of them, and the site shows them 20 at a time. This guide shows how to export them as a clean dataset instead of writing a scraper: one row per posting with employer, town, canton and postal code, the work load (pensum) as text and as numbers, the contract kind, the level of the position, the sector, the exact publication time and the full description in plain text, plus a salary in francs when the text states one. It uses a free-to-try Apify Actor, from the console or from code, with no login and nothing to configure.

Searches this page answers: JobScout24 scraper, jobscout24.ch scraper, JobScout24 jobs API, Swiss job postings dataset, Stellenangebote Schweiz Daten, offres d'emploi Suisse, how to scrape JobScout24, Pensum part-time jobs Switzerland data, monitor new job postings in Switzerland daily.

## What you get from the JobScout24 scraper

- **The whole board, newest first.** With nothing typed in, the run returns the latest postings. Add a keyword, a town or a canton, a contract type, a work load range, or paste any jobscout24.ch search URL: a keyword page, a town page or the filters of the site's own search form.
- **Swiss fields.** `city`, `region` (the canton code, `ZH`), `canton` (its name), `postalCode`, and the pensum as text (`80% - 100%`) and as numbers (`workloadMin`, `workloadMax`), so a part-time filter is one line in your pipeline.
- **The exact publication time.** `datePosted` is read on each posting page, in ISO 8601 with the Swiss offset in force that day (`2026-11-21T12:47:28+01:00`).
- **A salary in francs, when the text states one.** Staffing agencies often write "Lohn" lines in the text; those ranges come out as `salaryMin`, `salaryMax` and `CHF`, with the period only when the text says it. Nothing is guessed: 3 of the 10 postings of the salary search below carried one, none of the 50 of the pre-filled run.
- **Three site languages.** German, French or English pages set the language of town names and of the links in `url`; the postings keep the language their employer wrote them in (`language`).
- **A daily watch that charges only for what is new.** Tick `onlyNew` and schedule the Actor: each run delivers, and bills, only the postings no earlier run of that search delivered.
- **No personal data.** E-mail addresses and phone numbers are removed from descriptions, and so are the names of contact persons (on a best-effort basis); the dataset holds job postings only.

Export the dataset as JSON, CSV or Excel, or read it through the Apify API. A real sample (five postings from live runs on 23 November 2026, 00:49 to 00:51 UTC, descriptions shortened to 300 characters, no personal data) is in [`sample-output.json`](sample-output.json).

## Output fields

The unified job fields (the same names as the other job scrapers in this series, following schema.org `JobPosting`) come first; the JobScout24 fields come after them.

| Field | What it is |
|---|---|
| `id`, `sourceId`, `source` | Stable identifier (`jobscout24.ch:<posting id>`), the posting's id on the site (a GUID), and `jobscout24.ch`; the same from one run to the next |
| `url`, `title` | Public page of the posting, in the site language you chose, and the job title as published |
| `companyName`, `companyUrl`, `confidentialCompany` | Employer name and its JobScout24 page; `companyName` is `null` and `confidentialCompany` is `true` when the employer is hidden |
| `locationLabel`, `city`, `region`, `postalCode`, `country` | Place as displayed, town, canton code (`ZH`), postal code, and `CH` |
| `remote` | `remote`, `hybrid`, `onsite` or `unknown`, only when the title or text says it in so many words ("Homeoffice möglich", "100% remote", "télétravail partiel") |
| `employmentType` | `full_time`, `part_time`, `temporary`, `internship`, `freelance` or `unknown`: the contract when it says it all, else the work load (a permanent post below 100% is `part_time`) |
| `salaryMin`, `salaryMax`, `salaryCurrency`, `salaryPeriod` | Numbers and `CHF`, only when the text states them after a pay word ("Lohn", "Gehalt", "salaire"); the period (`hour`, `month`, `year`) only when the text says it |
| `datePosted`, `validThrough` | Publication time (ISO 8601, Swiss offset); `validThrough` is always `null`: the site has no expiry date |
| `experienceLevel`, `industry`, `skills` | Level of the position (`Employee`, `Specialist`, `Executive`), the sector when the posting has one, and an empty list (the site has no skills) |
| `workload`, `workloadMin`, `workloadMax` | The work load (pensum) as the site writes it (`70% - 100%`) and as numbers |
| `contractType` | The site's contract kind: `permanent`, `temporary`, `internship`, `freelance` or `side_job` |
| `canton`, `category` | The canton's name in English (`Basel-Landschaft`) and the site's professional group (`Medizin / Gesundheit / Psychologie / Soziales`) |
| `companyType`, `origin`, `language` | `sme`, `large_company` or `staffing_agency` as the list shows it; `jobscout24`, or `web` for postings the site collected from other websites; the language of the ad (`de`, `fr`, `en`, `it`) |
| `description` | Plain-text description, when `includeDescriptions` is on (default) |
| `searchUrl`, `scrapedAt` | The pasted URL the row came from (when there is one), extraction time |

Five real rows from the sample runs:

| Title | Employer | Place | Work load | Contract | Salary | Posted |
|---|---|---|---|---|---|---|
| Oberärztin / Oberarzt | Psychiatrische Dienste Aargau AG | Aarau, AG | 60% - 100% | permanent, full_time | - | 2026-11-02 |
| Studierende Expertin / Studierender Experte Intensivpflege NDS (a) 100% | Klinik im Park | Zürich, ZH | 100% | temporary | - | 2026-11-01 |
| THUN CarePeople-Crew Orthopädie: Dipl. Pflegefachperson HF 50-80% | CarePeople AG | Bern, BE | 50% - 80% | permanent, part_time | - | 2026-11-22 |
| Assistant·e médical·e CFC | APTIA SA | Lausanne, VD | 80% - 100% | permanent, full_time | - | 2026-11-22 |
| Senior Software Engineering (m/w/d) | Rocken® | Mels, SG | 80% - 100% | permanent, full_time | CHF 100,000-140,000 | 2026-11-20 |

## Use cases

- **Watch the Swiss job market.** Which employers, roles and cantons are hiring, day after day, with the work load as a number you can filter on.
- **Feed a job alert or an aggregator.** A stable `id`, an exact `datePosted` and the posting's own page on every row, and `onlyNew` so each scheduled run only pays for fresh postings.
- **Compare pay where it is published.** Staffing agencies write salary ranges in the text; they come out as numbers with the currency.
- **Follow one employer, sector or region.** Paste the search you ran on the site (a town, a category, a contract type) and get exactly what the page lists, newest first.
- **Track temporary and part-time work.** Filter on contract kind and on the pensum range, and read `companyType` to separate staffing agencies from direct employers.

## Input

| Field | What it does |
|---|---|
| `keywords` | A job title or skill in any of the site's languages (`Pflegefachfrau`, `infirmier`, `data engineer`). The site's own search: it looks in the title and the text of the posting. |
| `location` | A Swiss town or canton (`Zürich`, `Genève`, `Aargau`), several separated by commas (one search each). The site's own town list is used: `Geneva` finds `Genf`. Empty means all of Switzerland. |
| `language` | `de` (default), `fr` or `en`: the language of the site's pages, of its town names and of the links in `url`. |
| `employmentType` | The contract type, as the site's own filter sets it: `any`, `permanent`, `temporary`, `internship`, `freelance` or `side_job` (part time work next to another income). |
| `minWorkload`, `maxWorkload` | The pensum range you want, from 20 to 100. The site keeps the postings whose range meets yours: 80 to 100 returns full-time jobs and jobs that can be done at 80%. |
| `publishedWithin` | `any`, `1` (24 hours), `3`, `7` or `30` days, whatever the search, pasted URLs included. The cut is made on the publication time read on each posting page, or on the age shown on the list when descriptions are off. |
| `searchUrls` | Search pages copied from jobscout24.ch: `/de/jobs/`, a keyword page (`/de/jobs/pflege/`), a town page (`/de/jobs-in-bern/`, `/fr/jobs-à-genève/`, `/en/jobs-in-geneva/`) and the filters of the site's own search form (`cotidl` contract, `catidl` category, `insidl` sector, `jopidl` position, `regidl` region, `wt` work load, `actuality`, `lngCode` ad language). Page and order parameters are ignored: the Actor reads newest first. A single posting, a company page, an account page or the apprenticeship section is refused with a message. |
| `maxItems` | The run stops once this many postings are saved (default 50). The site shows at most 2,000 postings per search. |
| `onlyNew` | For scheduled runs: skip postings an earlier run of this same search already delivered. |
| `includeDescriptions` | Open each posting page for the description, exact time, canton, postal code, sector, contract kind and language (default on). Off, only the list is read: 20 postings per request. |

The fields make a search of their own when you fill them in; they do not narrow a pasted URL (except `publishedWithin`). The whole-board URL pre-filled in **Search URLs** on the Actor page is dropped as soon as the fields ask for something narrower. Leave everything empty to read all of Switzerland.

Ready-to-copy inputs. The searches below were run on the Apify cloud on 23 November 2026 (capped at 10 postings, without `onlyNew`) and each returned 10 postings.

Nurses in Zurich:

```json
{
  "keywords": "Pflegefachfrau",
  "location": "Zürich",
  "maxItems": 100
}
```

Postings whose text mentions the pay ("Lohn"), published in the last week:

```json
{
  "keywords": "Lohn",
  "publishedWithin": "7",
  "maxItems": 200
}
```

A daily watch on the whole board, listing fields only, charged only for what is new (set it on a daily schedule):

```json
{
  "publishedWithin": "1",
  "onlyNew": true,
  "includeDescriptions": false,
  "maxItems": 1000
}
```

Exactly the postings of a search page you copied from the site:

```json
{
  "searchUrls": ["https://www.jobscout24.ch/de/jobs-in-z%C3%BCrich/pflege/?cotidl=1"],
  "maxItems": 200
}
```

## Three ways to use it

### 1. Apify console, no code

1. Open [the Actor page](https://apify.com/bibian/jobscout24-jobs-scraper) and click **Try for free** (a free Apify account is enough). The form is already filled with the whole board, newest first, and 50 postings.
2. Add a keyword and a town, a contract type or a work load range, or paste a search URL, then click **Start**.
3. When the run finishes, open the **Output** tab, then **Export** as CSV, Excel or JSON.
4. For a daily watch, open **Schedules**, create a daily schedule for the Actor, and tick `onlyNew` with `publishedWithin` set to `1` in the input.

### 2. Apify API with JavaScript

```bash
npm install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
node examples/run.js
```

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: process.env.APIFY_TOKEN });

const run = await client.actor('bibian/jobscout24-jobs-scraper').call({
    keywords: 'Pflegefachfrau',
    location: 'Zürich',
    maxItems: 100,
}, { memory: 1024 });

const { items } = await client.dataset(run.defaultDatasetId).listItems();
for (const job of items) {
    console.log(`${job.title} | ${job.companyName} | ${job.city}, ${job.region} | ${job.workload} | ${job.employmentType} | ${job.datePosted}`);
}
```

The complete file is [`examples/run.js`](examples/run.js) (ES module, Node 18 or later).

### 3. Apify API with Python

```bash
pip install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
python examples/run.py
```

```python
import os
from apify_client import ApifyClient

client = ApifyClient(os.environ["APIFY_TOKEN"])

run = client.actor("bibian/jobscout24-jobs-scraper").call(
    run_input={"keywords": "Pflegefachfrau", "location": "Zürich", "maxItems": 100},
    memory_mbytes=1024,
)

for job in client.dataset(run.default_dataset_id).iterate_items():
    print(f"{job['title']} | {job['companyName']} | {job['city']}, {job['region']} | {job['workload']} | {job['employmentType']} | {job['datePosted']}")
```

The complete file is [`examples/run.py`](examples/run.py). Never put your token in the code: read it from the `APIFY_TOKEN` environment variable as above. On Windows, the complete file switches the console output to UTF-8, because Swiss postings hold characters its default code page cannot print.

## Pricing

Pay per result: **$0.002 per job posting** ($2.00 per 1,000 postings), plus a start fee of $0.0025 per GB of run memory, with no monthly subscription. These are the free-plan prices; paid Apify plans get lower ones (the Store page shows the lowest tier, from $1.60 per 1,000). A run that saves 100 postings costs about $0.20 plus the start fee. Apify's free plan includes monthly credits, enough to try it. Prices are those shown on the [Store page](https://apify.com/bibian/jobscout24-jobs-scraper) when this guide was written (23 November 2026); the Store page is the reference.

## Limits and honest notes

- **The site shows at most 2,000 postings per search** (100 pages of 20). The 2,000 newest of the whole board covered about 31 hours on 22 November 2026, so a scheduled run once or twice a day with `onlyNew` stays under the cap. For a bigger pull, split the work by town, keyword, contract type or category in a pasted URL; the run tells you when a search hit the limit.
- **Speed.** The pre-filled run (50 postings with descriptions) took 53 seconds; ten postings took 8 to 12 seconds in the tests behind the inputs above, and 2 seconds without descriptions. A run with descriptions reads one posting page per second: about 1,000 postings in 17 minutes. Without descriptions, 1,000 postings take about 50 seconds.
- **Without descriptions, `datePosted` is `null`.** The list only gives an age ("3 d"): `publishedWithin` then cuts on that age. The daily-watch input above therefore returns rows without a publication time and without canton or postal code; turn `includeDescriptions` on if you need them.
- **The first rows are not all from today.** Paid placements ("Top Listing") ride at the top of every list page whatever their age: the 50 postings of the pre-filled run were published between 1 and 22 November. Use `publishedWithin` to drop the old ones.
- **Salary is rare and only read from the text.** An amount in francs after a pay word ("Lohn", "Gehalt", "salaire", "salary"); a bonus, an allowance or a turnover figure is never read as pay. A single amount after "ab" fills `salaryMin` only, after "bis" `salaryMax` only. The period stays `null` unless the text says it (it was `null` on the Rocken® row in the sample).
- **`industry` is empty on most of the newest postings** (40 of the 50 of the pre-filled run); `category` is the field that is always there. `remote` and `employmentType` are only what the page or its text states, `unknown` otherwise.
- **A town search is the site's own.** It also returns postings in the surrounding area: the Zürich nurse test included Winterthur, Kloten and Richterswil. Read `city`, `region` and `postalCode` to keep only the places you want.
- **The same position can appear several times** (one posting per branch): each has its own `id`.
- **Postings "found on the web"** (`origin: "web"`) are collected by the site from other websites; their pages hold less structured data (no work load, contract kind or position level). None showed up in the tests behind this guide.
- **Descriptions** are the employers' text, in German, French, English or Italian. The Actor does not translate them. Names of contact persons are removed on a best-effort basis: a name inside a sentence with no contact word around it cannot be told from any other proper noun.
- **A posting that disappears** between the list page and its own page is skipped, with a warning in the log.
- This is an unofficial tool, not affiliated with JobScout24. Check the rules that apply to your use of the data, for example the GDPR and the Swiss data protection act.

## FAQ

**Do I need an account, cookies or a proxy for JobScout24?**  
No. The Actor reads publicly visible pages only, at most one request per second.

**Can I paste a search from the site?**  
Yes. Build the search on jobscout24.ch (a keyword page, a town page, or the filters of the search form), copy the address into `searchUrls`. The page and order parameters are ignored and the Actor reads newest first.

**Why are some `employmentType`, `remote` and salary fields empty or `unknown`?**  
Because the site has no field for them: the Actor only reports what the page or its text states, and says `unknown` or `null` otherwise.

**How many postings can one search return?**  
At most 2,000. Split a bigger need into several searches (towns, keywords, contract types, categories in a pasted URL).

**How do I get only new postings every day?**  
Set `publishedWithin` to `1`, tick `onlyNew` and schedule the Actor daily. Each run delivers, and charges, only what no earlier run of the same search already delivered.

**Can I export to Excel or a database?**  
Yes: JSON, CSV, Excel and XML downloads, the Apify API, or integrations such as Google Sheets, webhooks and Zapier.

## Auf Deutsch

**JobScout24 Scraper: die Stellenangebote von jobscout24.ch als Datensatz, mit Arbeitgeber, Kanton, Postleitzahl, Pensum, Vertragsart, genauer Veröffentlichungszeit und vollständigem Anzeigentext, ohne Login.** Exportiert die Schweizer Stellenangebote von JobScout24 nach JSON, CSV oder Excel: Titel, Firma, Ort, Kanton, PLZ, Pensum (`80% - 100%`) als Text und als Zahlen, Anstellungsart, Stufe der Position, Branche, Sprache der Anzeige, Veröffentlichungszeit und Anzeigentext, dazu ein Lohn in Franken, wenn der Text ihn nennt. Suche nach Stichwort (`keywords`), Ort oder Kanton (`location`), Anstellungsart und Pensum, füge die Adresse einer Suche von der Website ein und nutze `onlyNew`, um bei jedem täglichen Lauf nur die neuen Stellen zu erhalten und zu bezahlen.

## Related Actors

- [Jobup Scraper · Swiss Jobs, No Login](https://apify.com/bibian/jobup-jobs-scraper): the job board of French-speaking Switzerland, the same one-schema approach.
- [Jobs.ch Scraper · Swiss Jobs, No Login](https://apify.com/bibian/jobs-ch-jobs-scraper): job postings from jobs.ch, Switzerland.
- [Willhaben Jobs Scraper · Austria, No Login](https://apify.com/bibian/willhaben-jobs-scraper): job postings from Austria's largest classifieds portal.

## Feedback

Need another board, filter or field? Open an issue in the Issues tab of the Actor on Apify and say what you need: requests are read, and the most asked ones are built next.
