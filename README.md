# Money Flow

**Find the value. Plan the work. Prove the savings.**

Money Flow reads what already flows through your Cribl Stream workspace, ranks every Cribl-built pack opportunity in dollars at your own destination prices, and turns the packs you pick into a dated rollout plan your change board can follow.

Find it with Money Flow. Prove it with Meter Reader.

> Status: v0.18.2 checkpoint build, with measured benchmarks and the Meter Reader handoff. Free to use, not open source (the Money Flow License). Read-only: Money Flow never writes Cribl configuration.

## What it is

New Cribl customers face a hundred packs and one question: which ones pay? Money Flow answers it with your numbers, not a brochure's:

- **Pack leaderboard** ranks every Cribl-built pack in the benchmarks by how much it cuts: the mid of its measured range, "Measured" with the kind of data behind it, Cribl's published range beside it, and what it removes, with a line on how it reduces where the benchmark recorded one ("Trims the syslog prefix", "Rewrites each XML event as compact JSON") for the packs that trim or reformat rather than drop fields. Packs with no reduction measured, or that need configuration, follow with their status, never as 0%. **Rank by: Dollars on your data** sums each pack's savings over the sources it is matched to in your workspace, so you see which packs carry the most weight here.
- **Cribl's published figure** stands in where nothing was measured yet: a pack with no measured result for a source type, whose Cribl README or Dispensary page states a range, is estimated from that range and labelled "Cribl's published figure (not yet measured)" wherever its dollars appear, exports included. A measured result always wins.
- **Opportunities** opens with what Money Flow measured in your Cribl Stream (sources, TB/day, worker groups, the last refresh), then ranks each source by what its matched Cribl pack could save per year at the price your destination charges, always as a range (low, mid, high), with "Money on the table" for the rows you check.
- **Your own inputs:** add a source Money Flow cannot measure yet (one you are onboarding, or another team's feed) as a **manual source**; it is matched, estimated and ranked like a measured one and marked "Manual". Pick another Cribl pack, or no pack, for any source; the row is marked "Overridden" and your pick stays when the benchmarks update.
- **Show the math** sits behind every dollar figure: the formula with that row's own numbers, the price model, the benchmark version and the dataset.
- **Plan** turns your picks into dated steps: build the change, approve it and push it to dev, deploy it to the dev workers, stream a sampled copy of production data to them, validate against a written checklist, production, measurement, and a rollback plan. Pick the pace (Low, Medium, High or Super), blackouts, engineers and change windows. A cumulative savings line shows the dollars per day as each pack goes live.
- **Saved plans:** keep up to 20 plans. Each keeps the estimates it was saved with, so it reopens exactly as saved; when the benchmarks change, it offers "Re-estimate with today's benchmarks".
- **Export** the plan to Excel, a Jira CSV, a ServiceNow CSV or a one-page Leadership PDF, or copy a plan summary (total, range, first production push) to paste into an email or a ticket.
- **Report card** sets each pack of a saved plan against what Meter Reader measured once it went live: On track, Ahead or Behind its planned range, with the planned line (dashed) against the measured one (solid). Import a file from Meter Reader, and set each pack's actual prod date in place.
- **Pack lab** shows where every reduction comes from: each Cribl-built pack in the benchmark feed, installed in a real Cribl Stream and run on the feed's datasets, with what each run measured (its mid and its range), what the pack does to the data, and, for a pack not measured yet, why ("Next run", or "Needs configuration" with what to set first). It reads the same signed feed as every other page.

![Opportunities on the sample workspace: Money on the table $84,326 a year ($63,099 – $104,002) at sample prices, sources ranked by dollars saved, each with its range](https://raw.githubusercontent.com/o11yinnovatorsnetwork/cribl-apps/main/cc-money-flow/app/docs/images/opportunities.png)

*The screenshot shows the sample workspace with a sample benchmark file (dated Oct 1, 2026), not the measured benchmarks: its pack names, versions and reduction bands are illustrative. Its dollars use sample prices: $2.50 per GB sent to the SIEM, and $0.023 per GB-month kept 12 months in the S3 archive. It was captured on v0.18.0; the Opportunities page is unchanged through v0.18.2.*

## Try it in 5 minutes

1. **Install.** Download `money-flow-<version>.tgz` from Money Flow's [releases](https://github.com/o11yinnovatorsnetwork/cribl-apps/releases), where its `SHA256SUMS` sits beside it. In Cribl.Cloud, open **Apps → Add App → Import from File** and choose it. The **Review App** step lists read grants only (see [What it reads and writes](#what-it-reads-and-writes)). Installing an App is an Organization administrator action.
2. **Tour with sample data.** The first time Money Flow opens with no prices set, a four-step tour runs on bundled sample data under a **SAMPLE DATA** band: Money on the table, What it removes and Show the math, Build the plan on Medium, and Export.
3. **Use my workspace.** The last tour step returns to your own sources. The first refresh reads your sources, routes and destinations and the last 24 hours of volume, usually in a few seconds.
4. **Set prices.** Open **Prices** and enter what each destination costs you. "Use example" fills a list price you then edit to your contract. Until a destination has a price, its rows show GB saved and **Set a price** instead of dollars.
5. **Check packs.** The top five rows with dollars start checked. Check or uncheck rows and watch Money on the table move.
6. **Build the plan.** Pick the pace, the start date, blackouts, engineers, the approval lead time and your change windows. Every date carries "Planning estimate".
7. **Export.** **Export ▾** gives the Excel workbook, the Jira CSV, the ServiceNow CSV and the Leadership PDF.

## What it reads and writes

**Reads only.** Every Cribl API grant is a read. Two of them are `POST` requests because they take a body, and both change nothing: the metrics query, and the preview, which runs a pipeline over events in a throwaway process and saves nothing. Money Flow sends the preview only an installed pack's pipeline, but Cribl's preview API also accepts an inline pipeline configuration, and the grant cannot be narrowed to installed packs, so approving it lets the App preview any pipeline configuration (still in a throwaway process that saves nothing). One more read, the list of installed Apps, asks whether Meter Reader is installed and which version; it is not a grant (Cribl does not allow one for it), only each App's id and version are read, and nothing is stored.

**Writes only its own App storage.** Money Flow keeps your prices, settings, choices and plans in the App's own key-value store, which the platform scopes to this App. It never writes Cribl configuration: no sources, routes, pipelines, packs or destinations are created, changed or deleted. Applying a pack stays your team's job, through your own change process.

What the **Review App** step lists for the release package, from the grants its `policies.yml` declares:

| Review App item | Release package |
|---|---|
| Cribl API grants | `GET` on 6 paths, `POST /m/:gid/preview`, `POST /system/metrics/query` |
| Backend functions | None |
| External hosts | None |

**No telemetry.** Nothing leaves your workspace. Exports are built in your browser and go only where you send them. Money Flow stores no secrets. The App's storage is per App, not per member: everyone in the workspace who can open Money Flow sees the same prices, choices and plans.

## How it uses the platform

Money Flow ships as two packages built from the same code:

- **Release** (`money-flow-<version>.tgz`, any plan): front end only. While Money Flow is open it refreshes volumes every 5 minutes, as the signed-in member, through the platform's fetch proxy.
- **Backend variant** (`money-flow-<version>-backend.tgz`, Enterprise plans with App backend compute): the same refresh also runs on a schedule as two backend functions, and the benchmark feed downloads daily from one declared host.

### Cribl API endpoints (every `policies.yml` grant)

| Method | Policy object | Why Money Flow needs it |
|---|---|---|
| `GET` | `/products/stream/groups` | Lists your Stream worker groups, so every group's sources are ranked. |
| `GET` | `/m/:gid/system/inputs` | Reads each Source's type and Fields (a `sourcetype` set there) and Datagen sample names, to match a source to a pack. |
| `GET` | `/m/:gid/system/outputs` | Reads each Destination's type, for the Prices page and the suggested price model. |
| `GET` | `/m/:gid/routes` | Reads the routing table, to know which destination each source's data reaches. |
| `GET` | `/m/:gid/packs` | Lists installed packs, a matching hint only. |
| `GET` | `/m/:gid/system/samples/:id/content` | Reads the events of a Datagen sample one of your Sources already uses, so a pack's effect can be checked on your own sample events. |
| `POST` | `/m/:gid/preview` | Runs an installed pack's pipeline over events you load, from one of those samples or a sample file you choose, and returns them: at most 200 events, under 90,000 bytes, sent as you to your own Leader. A dry run: the Leader uses a throwaway process and saves nothing. Money Flow sends only an installed pack's pipeline id; Cribl's preview API also accepts an inline pipeline configuration, and the grant cannot be narrowed to installed packs. |
| `POST` | `/system/metrics/query` | Reads per-source input bytes over 24 hours, and how each source's bytes split across routes. A read that takes a query body. |

**Meter Reader detection (`GET /apps`, no grant).** To find Meter Reader and its version, Money Flow lists the installed Apps at most once per page load, the first time a page needs the answer (Settings, the Report card, Send to Meter Reader, the Plan's step 7 while no Meter Reader results are imported, and Opportunities, the Pack leaderboard and Plan once Meter Reader has proven a pack). Only each App's id and version are read; nothing is stored. It is not in `policies.yml`, because the Leader refuses any Apps path there: installing a package that declared it answered "Apps management paths (/apps, /apps/...) are not allowed in object" (build org, October 3, 2026). So the call goes out without a grant, and a refusal reads as "not detected": Money Flow then offers the plan file instead of the link and leaves out the connected line, and nothing else changes.

App storage (`/kvstore/...`) is scoped to Money Flow and granted by the platform, so it is not declared in `policies.yml`.

### App storage (KV keys)

| Key | What it holds | Written when |
|---|---|---|
| `settings` | Volume basis, time zone, plan defaults, tier defaults, the security-relevant field list, excluded sources, whether Cribl's internal sources are included, tour done | You save Settings, or finish or skip the tour |
| `prices` | Your price per destination, by model | You press Save on Prices, or keep a Meter Reader price ticked when you import its file |
| `inventory` | Groups, sources, destinations, routes and installed packs | A refresh finds a change (hash compared) |
| `volumes` | Bytes per source over the last 24 hours, and each source's destinations | Every refresh |
| `meta` | Last refresh time, call count and duration, errors, rate-limit end | Every refresh |
| `lock/snapshot` | A 240-second lease so two refreshes never overlap | Every refresh |
| `feed`, `feed.lastGood` | A benchmark feed that passed its signature and schema checks | You upload a results file (or the backend variant downloads the feed) |
| `manualSources` | Sources you add by hand | You add, edit or delete one |
| `overrides` | Your pack choice for a source | You pick another pack, or none |
| `opportunities.selection` | The rows you checked | You check or uncheck rows |
| `plans.index`, `plan/<id>` | Saved plans (20 at most), with each pack's actual prod date | You save, duplicate or delete a plan, or set a pack's actual prod date on the Report card |
| `reportCard` | Meter Reader results you imported (savings per pipeline per day, and Meter Reader's Planned vs measured days for each plan it holds), Meter Reader's list of which pipeline runs which pack, whether they came by link or file, and the pipelines you chose for a pack | You import Meter Reader's results (its link, or a file), or choose the pipelines that measure a pack |

A value over 90 KB is stored gzip-compressed in chunks under `<key>/c/<n>`, with a small manifest at `<key>`, because the Leader caps one value near 100 KB. A document you write (settings, prices, plans, the Report card's data) alternates between two ranges of chunks, `<key>/c/0` on and `<key>/c/256` on, and its manifest is written last, so a write that fails part-way leaves the previous version whole. A stored document Money Flow cannot read is never written over: the page says why and saves nothing. About 35 keys in all, against a cap of 1,000.

### Backend functions and schedules (backend variant only)

| Function | Schedule (UTC) | Timeout · memory | What it does |
|---|---|---|---|
| `snapshot` | `hourly-snapshot`: `7 * * * *` (hourly at :07) | 120 s · 512 MB | Reads sources, routes, destinations and per-source volume; writes `volumes`, `inventory` and `meta`. Reads Cribl config; never writes it. |
| `feedRefresh` | `daily-feed`: `15 6 * * *` (daily at 06:15) | 60 s · 256 MB | Downloads the signed benchmark feed from the one declared host and stores it only if the signature and schema checks pass. |

The release declares no backend functions: App backend compute needs an Enterprise plan, so the release runs the same refresh in the page.

### The one proxy host (backend variant only)

| Host | Allowed path | Why |
|---|---|---|
| `raw.githubusercontent.com` | the feed folder only: `raw.githubusercontent.com/o11yinnovatorsnetwork/cribl-apps/main/cc-money-flow/feed/` | The signed benchmark feed (15 s timeout). |

The release package declares **no** external host: App proxies need an Enterprise plan. The release uses the signed benchmark snapshot bundled inside it, and **Settings → Upload results file** takes a newer one.

## Methodology: estimates, not promises

> *Estimates, not promises*
>
> Every dollar figure in Money Flow is an estimate shown as a range. Your real savings depend on your data, your contract and how each pack is configured. Meter Reader measures what each pack actually saved.

The in-App **Methodology** page (top bar) explains, in plain language: how volumes are read, how packs are matched, how reductions are measured (datasets, soak, p10/p50/p90), what the ranges mean, the pricing models, why estimates differ from reality, and how Meter Reader measures the real result. It shows the benchmark version in use, the dataset table with licenses, the sanity check against Cribl's published figures, all read from the feed rather than typed into the page, and a short note on each pack version the feed carries.

The estimate per source:

```text
GB/day saved   = GB/day × reduction              (low, mid and high: p10, p50 and p90)
$ per day      = GB/day saved × $ per GB saved   (per destination, split by route share)
$ per year     = $ per day × 365
```

- **Volume basis:** bytes arriving at the source over the last 24 hours (Cribl.Cloud's metrics store held only about 27 hours when measured, so a 7-day basis would silently cover less). The page says "24 h basis".
- **Unknowns are never $0 or 0%.** A row shows "No test data yet", "Needs configuration" followed by the reason, or "Based on the pack's own samples" instead. A pack whose output was about the same size or larger on the benchmark data reads "No reduction measured" with the reason, and a destination priced at zero reads "Set a price". Money is never shown negative.
- **Show the math links.** Opening a row's figure puts `?math=<group>/<source>` in the address, so the Cribl URL bar holds a link that opens the same receipt for whoever you send it to.

## Pricing models and your contract prices

Enter prices on the **Prices** page, one card per destination. Fields take dollars with up to three decimals, from 0.001 to 1,000,000. A suggested price appears as placeholder text labelled "Example list price, edit to your contract" and is never applied until you press **Use example**. Nothing is saved until you press **Save**.

| Model | You enter | $ per GB saved | In Money on the table? |
|---|---|---|---|
| Per GB ingest (for example Splunk Cloud ingest, Datadog Logs, Microsoft Sentinel) | $ per GB | your price | Yes |
| Storage × retention (for example S3, Azure Blob, Cribl Lake, Splunk DDAS) | $ per GB-month, months kept, compression ratio | $ per GB-month × months ÷ compression | Yes |
| Capacity or workload (for example Splunk SVC) | $ per unit per year, GB/day per unit | shown as "Capacity freed ≈ $X/yr avoided cost" | No, shown on its own line |

A source routed to several destinations is split by each route's measured share of its bytes; an unpriced destination's share shows GB saved and "Set a price", never a blended $0. The header says "N destinations are unpriced. Set a price" until each one has a price.

## Exports

**Export ▾** on the Plan page builds every file in your browser:

- **Excel workbook** (`money-flow-plan-<plan-name>-<YYYY-MM-DD>.xlsx`): four sheets, `Plan`, `Timeline`, `Opportunities` and `Assumptions`. Dates are real date cells and money cells are numbers formatted `"$"#,##0`, so sorting and sums work. Every Validate step carries the five-item validation checklist.
- **Jira CSV**, for Jira Cloud's CSV importer.
- **ServiceNow CSV**, for an import set.
- **Leadership PDF** (`.pdf`): one US Letter page with the plan's total planned savings and range, the cumulative savings line, the packs with their production dates and dollars (the ten with the highest planned savings when a plan has more, with the count of the rest), the tier and start, and the cost of a 4-week delay, all marked "Planning estimate". Fonts are embedded, so it prints and reads the same everywhere.
- **Copy plan summary** puts a short text on the clipboard: the plan's total and range per year, the first production push, the date by which every pack is live, and "Planning estimate".
- **Copy plan as CSV** copies the `Plan` sheet as CSV, for a browser that blocks downloads. When a CSV download is blocked, the note offers **Copy CSV** for that file.

### Jira Cloud

Columns, in order: `Work item ID, Work type, Summary, Description, Start date, Due date, Labels, Labels, Parent`. One `Epic` per pack, then that pack's `Task` rows, one per step, each with `Parent` set to its Epic's Work item ID. Both labels (`money-flow`, `planning-estimate`) use the repeated `Labels` column.

To import, use Jira Cloud's CSV importer ([Import data from a CSV file](https://support.atlassian.com/jira-cloud-administration/docs/import-data-from-a-csv-file/); [Keep work parent-child relationships during CSV import](https://support.atlassian.com/jira/kb/keep-issue-parent-child-mapping-during-csv-import-to-jira-cloud/)). The date format to enter is **`yyyy-MM-dd`**:

> Jira Cloud CSV import: at the wizard's date-format step enter yyyy-MM-dd. Map Work item ID, Work type and Parent so Jira builds the hierarchy: one Epic per pack, listed before its Tasks, and each Task's Parent is its Epic's Work item ID. Both Labels columns map to Labels (money-flow and planning-estimate). Summary is the only required field.

| CSV column | Jira field |
|---|---|
| `Work item ID` | Work item ID |
| `Work type` | Work type |
| `Summary` | Summary |
| `Description` | Description |
| `Start date` | Start date |
| `Due date` | Due date |
| `Labels` (both) | Labels |
| `Parent` | Parent |

### ServiceNow

Columns, in order: `short_description, description, planned_start_date, planned_end_date, assignment_group, parent, u_money_flow_pack, u_step`. One parent row per pack and one child row per step. RFC 4180 quoting. Dates are `yyyy-MM-dd HH:mm:ss` in the plan's team time zone (a morning runs 09:00 to 13:00, an afternoon 13:00 to 17:00).

To import ([Import sets](https://www.servicenow.com/docs/r/mGE8r5NBpLYtmri_sFVECw/ctav~0glbvOSsA82ZOISCg); [Create a File type data source](https://www.servicenow.com/docs/bundle/yokohama-integrate-applications/page/administer/import-sets/task/create-file-type-data-source.html); [Create a field map](https://www.servicenow.com/docs/r/mGE8r5NBpLYtmri_sFVECw/zSrsXPn9Ki2wGOgoJ_WB2A)), set the field map's Date format to **`yyyy-MM-dd HH:mm:ss`** and map `parent` by **`short_description`**:

> ServiceNow: load the CSV into an import set through a File data source (CSV delimiter: comma). In the transform map, set the Date format of planned_start_date and planned_end_date to yyyy-MM-dd HH:mm:ss; times are in the plan's team time zone. Set the parent field's Referenced value field name to short_description, so each step row points at its pack row. Map u_money_flow_pack and u_step to custom fields or into the description.

The same instructions are in the workbook's `Assumptions` sheet and in the note shown after each CSV download.

## Meter Reader

Meter Reader is a separate, free Cribl App (Apache-2.0) that measures what each pipeline actually saves, in dollars, once it is live. Money Flow finds the opportunities in your workspace and plans the work; Meter Reader proves it with the receipt. The two work together and never depend on each other: each App is complete alone, and neither reads the other's storage.

- **The plan:** step 7 of every pack is "Measure with Meter Reader", seven calendar days after production.
- **Send the plan to Meter Reader:** on a saved plan, **Export → Send to Meter Reader** (also in the Report card's plan menu) gives Meter Reader the plan: each pack, its prod date, its planning estimate and the benchmark band it used. Where Meter Reader 1.2.0 or later is installed in the workspace, **Open in Meter Reader** carries the plan in a link to Meter Reader's Plan page, which shows what it will keep and asks before it saves. **Download for Meter Reader** always gives the same plan as a file to import there. Nothing in Cribl changes.
- **The report card:** follows a saved plan and compares each pack's planned savings per day (with its range, "Planning estimate") with what Meter Reader measured ("Measured"), as Not live yet, Measuring (day N of 7), On track, Ahead, Behind or No measurement. From day 7 after its prod date a pack is judged on its measured days in the last 7: On track inside its planned range, Ahead above it, Behind below it. Each pack's prod date starts as the plan's and takes the actual date in place. A chart sets the planned line (dashed) against the measured one (solid), with a Table view.
- **Bring Meter Reader's results back:** an App cannot read another App's storage, so the results come to Money Flow in your browser, by link or by file. Meter Reader's **Send to Money Flow** opens the Report card with its measurements in the link, and the import dialog says where they came from, what they hold and what they change before anything is saved (Import, or Not now). On the Report card, **Import from Meter Reader** takes a file:
  - Meter Reader's own **Report card CSV** download. It gives each flow's savings at the last hour's rate, so each import adds one day per pipeline (the last hour × 24); download it each day to build a series.
  - A daily CSV with the columns `date, pipelineId, savedUsd`.
  - Meter Reader's daily savings export and its flow list (JSON), which name exactly which pipeline runs which pack.

  A link or file Money Flow cannot use lists each problem, and nothing is imported. Importing a day again replaces it, except that a whole measured day is never replaced by a run rate; the same export imported twice changes nothing the second time.
- **Proven in Meter Reader:** once a pack has a measured week that saved money, Opportunities, the Pack leaderboard and the Plan mark it "Proven in Meter Reader", and **See the receipt in Meter Reader** opens Meter Reader's Ledger on that pack's pipeline. Each pack on the Report card carries the same receipt link.
- **Prices:** an import offers the price per GB Meter Reader uses for each destination. A destination with no price in Money Flow is ticked to take it and shows "From Meter Reader" on Prices; changing it there asks "Change here only" first. Money Flow never writes to Meter Reader.
- **Connected:** where Meter Reader is installed, Settings shows "Meter Reader connected (vX.Y.Z)".

Until Meter Reader's results are imported, the Report card page shows one prompt, "Install Meter Reader to track actual savings.", with a link to Meter Reader, and each plan's step 7 carries the same line. Where Meter Reader is already installed, the prompt keeps its place and offers **Send this plan to Meter Reader** and **Import from Meter Reader** instead of the install link, and step 7 reads "Meter Reader is installed: it measures what this pack really saves." Without Meter Reader, nothing else changes.

The contract both Apps implement, field by field, is [docs/INTEGRATION.md](https://github.com/o11yinnovatorsnetwork/cribl-apps/blob/main/cc-money-flow/docs/INTEGRATION.md) in the repository.

## What exists, and what Money Flow adds

Cribl and its community already cover parts of this. Here is what each does today, checked against the Dispensary, Monitoring, Insights and CriblVision documentation:

| What exists | What it shows today | What it does not do |
|---|---|---|
| **Packs Dispensary** | Lists packs. Some pack READMEs publish a reduction figure (Palo Alto Networks: "15-30% reduction"). | No dollars, and no figure on your volumes or at your prices. |
| **Monitoring and Cribl Insights** | Volumes in bytes, per source and destination. | No dollar figure for data flows. |
| **CriblVision** (the App's Data Value tab, and the CriblVision for Stream pack's "Value Reduction GB" dashboard) | A dollar figure after the fact: one $ per GB times ingest minus egress, or a GB difference times a cost per GB you type. | No ranking of pack opportunities before install, no per-destination prices, no rollout plan. |
| **Meter Reader** | Dollars each pipeline saved after the fact, at per-destination prices. | Does not rank what to install next, or plan it. |
| **Money Flow** | Pack opportunities ranked in dollars at your per-destination prices, before you install; a dated rollout plan; planned vs measured with Meter Reader. | Does not install packs or change configuration. |

Money Flow adds three things none of them do: **a ranking of pack opportunities in dollars against your volumes and your prices, a dated rollout plan your change process can follow, and a planned-vs-actual report card.**

## Benchmarks

Each pack's reduction comes from a benchmark feed: per pack, version and source type, the low, mid and high reduction (p10, p50 and p90 over twelve 5-minute windows of a 60-minute soak), what the pack removes, and the dataset it ran on.

- **What this build ships:** the measured feed `2026.10.02-pilot.4`: Palo Alto Networks plus a 10-pack pilot, 11 Cribl-built packs and 12 results measured on Cribl Stream 4.20.1, each pack at its default configuration and under its display name ("AWS CloudTrail"). Nine are measured on synthetic data, CrowdStrike FDR on the pack's own sample files ("Based on the pack's own samples"), and Okta REST IO reads "Needs configuration" with its reason (your Okta domain and API token). Where the runner's own record of a run shows how the pack changed the bytes, the result carries a one-line note, which the Pack leaderboard and Show the math repeat. The tour still runs on its own illustrative sample figures, labelled sample data.
- **Datasets and licenses:** every scored result names its dataset, and the feed lists each dataset's license and link: DataTap 3.0.0's synthetic events (Apache-2.0), generated for each source type, and the packs' own bundled samples for the one pack no dataset covers. Cribl-built packs only.
- **Synthetic data, not your traffic:** the figures describe each pack on generated events, not on any customer's data. The bands are narrow because the test stream is steady: read them as "this pack on this data", and Cribl's published figure as the field estimate. The Methodology page carries a short note per pack on what the test data could not show.
- **Flagged results:** three packs land more than 10 points from Cribl's published figure and are flagged, with both numbers on the Methodology page: Fortinet FortiGate (−20.6% measured against Cribl's 30%, which assumes drop and sampling steps that ship turned off), Windows XML to JSON (50.4% against 30–35%; the test events carry no Message block) and Syslog Pre-processing (51.9% against 20–30%; the test messages are short). FortiGate and Zscaler make the test data larger as shipped, so they read "No reduction measured".
- **Isolation:** the benchmark runner is not part of this App and never touches your workspace. It runs on a build machine, one fresh Cribl container (`cribl/cribl:4.20.1`, pinned by digest) per pack on its own Docker network with no outside access, and refuses to run a pack if its preflight can reach the internet. No customer or employer data is used.
- **Cadence:** the runner's watcher reads the Packs Dispensary listing and queues any new Cribl-built pack or version for the next run; its daily schedule and the full Dispensary list are the next stage. The backend variant downloads the published feed daily at 06:15 UTC once it is published; until then it keeps the signed snapshot it ships with, as the release does, and either package takes a newer results file by upload.
- **Signed:** the feed is signed (Ed25519) with the benchmark runner's key, the only key the App trusts, and checked against a schema before it is used; a feed that fails keeps the last good one, and the footer shows "Benchmarks as of" the date of the feed in use.
- **Air-gapped:** **Settings → Upload results file** runs the same signature and schema checks on a file you download yourself.
- **Best fit for your data:** results describe how a pack fits your sources and prices. They are never a public ranking of packs. The feed is not published yet: results are shared with Cribl before any public feed. When a result differs from Cribl's published range by more than 10 points, the Methodology page shows both numbers.

## Engineering notes

- **Request budget.** A refresh makes at most 30 Leader API calls, App storage included, counted in code and kept in `meta.lastSnapshotCalls`. Measured live on Cribl 4.20.1 with one worker group: **17 calls** (10 App storage, 5 configuration reads, 2 metrics queries), in under a second. Two metrics queries cover every source in every group; the configuration is re-read every sixth refresh or on **Refresh now**, and more than three groups are read round-robin. The page refreshes every 5 minutes while open and pauses while hidden; **Refresh now** runs at most once per 60 seconds.
- **429 handling.** On a 429 the refresh stops at once, keeps the last good volumes, records the error and waits for the Leader's `Retry-After` (1 second to 1 hour; 60 seconds when absent). Until then no refresh, forced or timed, calls the Leader, and the footer reads "Rate limited by the Leader. Next refresh in m:ss."
- **Millicent money.** Every amount is an integer number of millicents (1 cent = 1,000; $1 = 100,000) from prices to totals; there is no floating-point money. Rounding happens once per row, and formatting happens in one module. 0.4 GB/day saved at $0.023 per GB is 920 millicents a day ($0.0092), never $0.00. A lint test fails the build on money divided by 100 or `toFixed` outside the formatting modules.
- **Deterministic scheduler.** The same plan inputs always give byte-identical plan JSON: stable ordering, no clock reads, dates as `YYYY-MM-DD` with a morning or afternoon half. Steps never land on a blackout or non-working day, a step that meets a blackout pauses and resumes with its duration kept, concurrency never exceeds the tier or the engineers, and a calendar with no working day for three years stops with an error instead of looping. Golden Low, Medium and Super plans and property tests hold it there.
- **`schemaVersion` migrations.** Every stored document carries a `schemaVersion`, checked with the document's shape on every read. Every document is version 1 today; when a version 2 ships, its upgrade step runs in that one read path before the shape check, and a document from a version this build does not know is set aside (treated as missing, with a warning) rather than misread or overwritten. Defaults are never written on load: nothing is saved before the first read has landed, so a slow read can never overwrite your settings.
- **Sandbox-friendly.** No browser storage, SVG charts only, fonts bundled, no code generated at run time (the feed's schema check is compiled ahead of time), and every package checked for size (5 MB), notices and forbidden strings before release.

### Dependencies

| Package | Version | License | Used for |
|---|---|---|---|
| `@capra/core`, `@capra/icons`, `@capra/theme`, `@capra/dx-tokens-postcss-plugin` | 1.16.0, 1.10.1, 1.6.0, 0.4.0 | Cribl Developer Agreement | Cribl's design system: controls, icons, theme tokens |
| `react`, `react-dom` | 19.3.0 | MIT | The user interface |
| `react-router-dom` | 7.18.4 | MIT | Pages and deep links, synced with the Cribl URL bar |
| `xlsx` (SheetJS) | 0.18.5 | Apache-2.0 | The Excel workbook |
| `papaparse` | 5.7.0 | MIT | CSV reading |
| `pdf-lib` | 1.17.1 | MIT | PDF export |
| `@noble/ed25519`, `@noble/hashes` | 3.2.0, 2.4.0 | MIT | Feed signature checks |
| `ajv` | 8.20.0 | MIT | The feed schema check, compiled ahead of time |
| `d3-shape`, `d3-sankey` | 3.2.0, 0.12.3 | ISC, BSD-3-Clause | Layout math for the SVG charts |

Fonts: Fraunces, IBM Plex Sans and IBM Plex Mono (SIL Open Font License 1.1), bundled. Every package ships `static/THIRD-PARTY-LICENSES.md` with the full notices.

## Accessibility and keyboard

- WCAG 2.2 AA in both Cribl themes; axe finds no serious or critical issue on any page, light or dark, in the test suite.
- Every dollar figure is one button whose spoken name gives the amount, its range and "Show the math"; closing the drawer returns focus to the figure.
- Every chart is SVG with a table view of the same numbers and `aria-describedby`; motion follows `prefers-reduced-motion`.
- A skip link, one main heading per page, and a visible focus ring.
- Keyboard shortcuts work whenever no text field has focus:

| Key | Action |
|---|---|
| `/` | Focus the filter |
| `j` / `k` | Next / previous row |
| `Space` | Check or uncheck the row |
| `Enter` | Expand the row |
| `b` | Build the plan |
| `e` | Open the Export menu |
| `?` | Show the shortcut sheet |

## Roadmap, license and credit

**Roadmap:** a Splunk companion app; third-party and community packs; opt-in, anonymized results shared back to improve the ranges; a "Re-estimate with today's benchmarks" comparison for saved plans; a link from each pack to its Dispensary page; a roll-up view across worker groups.

**License:** free to use, not open source. Money Flow is proprietary software, licensed at no cost under the [Money Flow License](https://github.com/o11yinnovatorsnetwork/cribl-apps/blob/main/cc-money-flow/app/LICENSE), which every package also ships as `static/LICENSE`. It lets you install and use Money Flow inside your organization; it does not let you modify, redistribute or resell it.

**Home:** Money Flow comes from the [Observability Innovators Network](https://o11yinnovatorsnetwork.com). Its public home is [github.com/o11yinnovatorsnetwork/cribl-apps](https://github.com/o11yinnovatorsnetwork/cribl-apps): this README, the license, the third-party licenses, the changelog, the integration contract (`docs/INTEGRATION.md`), the screenshot, the security policy and the releases. The source code is not published.

**Support:** open an issue at [github.com/o11yinnovatorsnetwork/cribl-apps/issues](https://github.com/o11yinnovatorsnetwork/cribl-apps/issues). Name the package (release or backend variant) and its version, and leave out workspace names, hostnames and data.

**Security:** report a vulnerability privately with GitHub's private vulnerability reporting: the repository's **Security** tab → **Report a vulnerability** ([open a private report](https://github.com/o11yinnovatorsnetwork/cribl-apps/security/advisories/new)). Please do not file it as a public issue.

**Known limitations:**

- Every savings figure Money Flow estimates is shown with its range. Your real savings depend on your data, your contract and how each pack is configured; Meter Reader measures what each pack actually saved.
- The benchmarks cover Cribl-built packs only, each at its default configuration, measured on generated test data (one pack on its own sample files, and one that needs configuration is not measured yet), not on your traffic. Three packs are flagged on the Methodology page, more than 10 points from Cribl's published figure. Third-party and community packs are not covered yet.
- Volumes are read over the last 24 hours: Cribl.Cloud's metrics store held only about 27 hours when measured, so one day is the longest whole window it can answer.
- The release runs on any plan and refreshes only while Money Flow is open. Scheduled refresh and the daily feed download need the backend variant, which needs an Enterprise plan with App backend compute and App proxies.
- The hosted benchmark feed is not published yet. Both packages ship with a signed snapshot and take a newer results file by upload; only the backend variant downloads the published feed, daily, once it exists.
- There is no roll-up view across worker groups yet.
- Money Flow plans the work and never changes Cribl configuration: your team installs and configures the packs.

Find it with Money Flow. Prove it with Meter Reader.

Built by Steve Koelpin · Join the Cribl Innovators Network: https://www.linkedin.com/groups/13052739
