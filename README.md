# Painless Project Management

A one-page way to run a project in Aftersales Parts without becoming a project manager.

Open `index.html` in any browser. No install, no build step, no dependencies beyond two Google Fonts. The page opens with three example projects loaded so you can see how it works; delete them from their project pages when you are ready to use it for real.

Three steps, one page each:

1. **Charter it.** One page: department, sponsor, lead, why, what, what we get, approved budget, CER yes/no, CLM yes/no, start and end dates. Until the page is complete, it is not a project.
2. **Run it.** Up to eight milestones. Three money numbers: approved, spent, forecast. A two-minute weekly update: schedule, budget, and whether you need help (and where).
3. **Report it.** Every project rolls up to one view sorted by who needs attention: help requests, off-track schedule or budget, projects that have gone quiet. An executive summary is written automatically, and the roll-up exports to CSV for monday.com or any other tracker.

## Rules of the road

- Every project has a sponsor and a lead. Nobody else needs a title.
- Eight milestones, maximum. If you need more, it is two projects.
- The weekly update takes two minutes. Post it on Friday, even when nothing changed.
- Red is not failure. Hiding red is.
- If you need help, say where. That is how help finds you.

## Where the data lives

This build keeps projects in the browser (`localStorage`), so it is single-user and single-device: good for trying the method, running your own projects, or showing it to someone. A banner at the top says so.

For a team, the same page runs against a shared database: every read and write goes through the small `Store` object in the script (`subscribe`, `set`, `remove`), so swapping browser storage for a backend is a change to that one object, not to the app.

## Data model

Each project is one JSON document:

| Field | Meaning |
| --- | --- |
| `name`, `dept`, `lead`, `sponsor` | The basics from the charter |
| `why`, `what`, `benefit` | The three business-case answers |
| `start`, `end` | ISO dates (`YYYY-MM-DD`) |
| `budget`, `spent`, `forecast` | Approved, spent to date, forecast at completion (USD) |
| `cerRequired`, `cerNumber`, `cerStatus` | Capital expenditure request: Not started, Submitted, Approved |
| `clmRequired`, `clmWith`, `clmStatus` | Contract lifecycle management: Not started, In CLM, Signed |
| `strategy` | Optional link to a strategy initiative |
| `status` | Active, On hold, Closed |
| `outcome` | Filled in at close: did we get what the business case said? |
| `milestones[]` | `{id, name, due, done}`, at most eight |
| `updates[]` | `{at, schedule, budget, help, where, note, by}`; `schedule` and `budget` are `green`, `amber`, or `red` |
| `createdAt`, `updatedAt` | ISO timestamps |

Health shown on the roll-up comes from the latest weekly update. If there is none, the page suggests a status from the data: an overdue milestone turns schedule amber (red past 14 days or past the end date), and a forecast over approved turns budget amber (red past 110%). A project with no update in 14 days is flagged as quiet.

## CSV export

The roll-up's **Export for monday.com** button downloads a CSV with one row per project and one column per field, including computed ones (schedule, budget health, milestones done, next milestone, needs help, help where, last update). Columns are flat on purpose so they map one-to-one to board columns.

## Changing things

- Department list: the `DEPTS` array near the top of the script.
- Milestone cap and quiet threshold: `MAX_MS` and `STALE_DAYS` next to it.
- Example projects: the `EXAMPLES` array below those.
- Colors and fonts: the `:root` token block at the top of the stylesheet. Status colors are separate from the brand red so a red status never reads as branding.
