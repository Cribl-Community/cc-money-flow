# Changelog

All notable changes to Money Flow are recorded here. Versions follow semantic versioning.

## [v0.18.2] - 2026-10-13
The Plan's dev steps now describe how a change is tested in Cribl: the change is approved and pushed to dev, the dev workers get it, and the prod workers stream a sampled copy of the source's real traffic to them for validation. Text only: every step keeps its number and duration, so every plan's dates are unchanged (the golden plans differ only in step names).

### Added
- The README's last section gives Money Flow's home, support (GitHub issues), security reporting (GitHub private vulnerability reporting) and known limitations, as Cribl's Marketplace guide asks.

### Changed
- Plan steps 2 to 4, on the Plan page and timeline and in the Excel, Jira CSV, ServiceNow CSV and Leadership PDF exports: "Submit PR to the dev leader" is now "Approve the change and push it to dev", "Test sample data against the dev leader" is now "Deploy to dev workers", and "Deploy to dev workers" is now "Stream sampled prod data to dev workers". Cribl moves data Worker to Worker, never through a Leader, and under GitOps the pull request is the promotion to prod, not a step toward dev.
- The timeline legend's step kinds read "Build and review" and "Deploy and stream to dev".
- The README, PRD FR-3.4 and SPEC 11.2 match. SPEC 11.2 lists the Cribl parts behind each step, quoted from docs.cribl.io: a Route with Final off, the Sampling Function, the Cribl HTTP or Cribl TCP pair (or generic HTTP, syslog or storage between on-prem Leaders on different licenses), and DevNull or a test index on dev.
- Money Flow's public home is the Observability Innovators Network's repository, github.com/o11yinnovatorsnetwork/cribl-apps: the README's links (screenshot, releases, license, the integration contract), the backend variant's feed address (`core/feedSource.ts`) and its proxy pin (`config/proxies.yml`) point there. The release package still fetches nothing.

### Fixed
- With reduced motion on, the App starts no transition at all (it used to set a 0.01 ms one), so a theme switch shows its final colours from the first frame. In Safari, the accessibility check could read the old theme's colours during the switch.
- In Safari, a menu opened from the keyboard near the bottom of the window could close itself when the page scrolled it into view. It now stays open.
- The step table's Depends on column names the earlier step from the plan itself, as the exports do, so a plan saved before 0.18.2 shows its own step names throughout instead of mixing in the new ones.
- Two tests read DECISIONS-archive/ after the decisions rollup moved entries there.

### Known
- A plan saved before 0.18.2 reopens exactly as saved (AC-3.7.1), so it shows the old step names until it is edited and saved again.

## [v0.18.1] - 2026-10-12
Money Flow's license changes from Apache-2.0 to the Money Flow License: Money Flow is free to use and is not open source. No code changes; the grants are unchanged (`config/policies.yml`). The 0.18.0 and older packages in `release/` still carry the Apache text and are never uploaded; none was released publicly.

### Changed
- `LICENSE` is the Money Flow License (copyright Steve Koelpin, all rights reserved): free install and use inside your organization, commercial use included; no modification, reverse engineering, redistribution, resale or hosting for others; no warranty; it covers only the version it ships with, so later versions may carry other terms, paid terms included. A drafted license, pending legal review before any paid tier (D1039).
- `package.json` declares `"license": "SEE LICENSE IN LICENSE"`; the packager stamps and checks that value (`LICENSE_FIELD` in `scripts/package.mjs`), and each package's `static/THIRD-PARTY-LICENSES.md` opens by naming the Money Flow License. The benchmark runner (in no package) declares `UNLICENSED`.
- Both READMEs say "free to use, not open source"; the Marketplace overview's license link opens the Money Flow License, which every package also ships as `static/LICENSE`.
- `tests/compliance.test.ts` checks the Money Flow License's clauses and refuses an Apache, MIT, BSD or GPL license text, and `tests/forbidden.txt` allows those two words only as "not open source".

## [v0.18.0] - 2026-10-05
A new look in both themes, the Pack lab page, the bundled enterprise sample on four Worker Groups, and the fixes from two rounds of visual review. The grants are unchanged: `config/policies.yml` is 0.17.3's, byte for byte. Still a checkpoint build.

### Added
- **Pack lab**, a page between the Report card and Prices: each Cribl-built pack in the benchmark feed, installed in a real Cribl Stream and run on the feed's datasets, with what each run measured (its mid and its range), what the pack does to the data and, for a pack not measured yet, why ("Next run", or "Needs configuration" with what to set first). It reads the same signed feed as every other page.
- The bundled enterprise sample workspace runs on four Worker Groups instead of one, sized by Cribl's published sizing guidance: 40 Worker Nodes in all, with room for a fifth of them to be down. Its dollar figures are unchanged.
- Benchmark runner (in no package): the pack-samples cross-check. `node runner/pilot.mjs --data pack-samples` runs the nine packs DataTap measured on their own bundled samples, with the same container, isolation, 60-minute soak and band as every result, into `bench/out/results-pack-samples/`; `node runner/cross-check.mjs` checks each result and sets it beside its DataTap figure, flagging a gap of more than 10 percentage points. A cross-check is never a feed result: the runner keeps it out of `bench/out/results` and the feed builder refuses it. The first run (2026-10-04) measured all nine (6 gaps above 10 points); the bundled benchmarks stay `2026.10.02-pilot.4`.
- Benchmark runner (in no package): the feed signing key is kept passphrase-encrypted. `runner/encrypt-key.mjs` encrypts it (a passphrase of at least 16 characters, asked twice), `runner/key-drill.mjs` checks that a key or its offline copy opens, signs and verifies, and `runner/keygen.mjs` writes new keys encrypted. The loader refuses a plain key and a key file open to any other user or group, and asks for the passphrase only on a terminal, so no unattended job can sign.

### Changed
- The look, in both themes. The background is Cribl's own: its page and panels, neutral borders and a low shadow, with no grid and no tint. Each colour has one meaning everywhere: jade is money saved, ice blue is data volume, amber is a caution, and a problem has its own colour (salmon in dark, rust in light). Capra's controls take the App's accent through Capra's own tokens.
- Dark: near-black cards, money saved in polished jade, data volume in ice, highlights in pale jade, and no glow.
- Light is redrawn to match dark: the same figures, labels and charts, in tones that read on white (deep jade for money saved, deep ice for data volume, amber for cautions, rust for problems).
- The X-ray's film looks the same in both themes.
- On a phone, the Money River opens at its far end, on the packs, the destinations and the Saved pool; its hint says to swipe back toward the sources.
- The Pack leaderboard's Show the math opens on the same result card as a row's: "Saved per year", with its range.
- An empty price field marks its example as one ("e.g. 2.50").
- An error's technical detail (a storage call, an HTTP status, the server's own text) sits behind a closed Details disclosure under the plain sentence.
- The Plan's side column, when it is taller than the window, sticks by its foot, so its end stays in view as the page scrolls.

### Fixed
- While saved prices cannot be read (an error or a refusal), Prices no longer reads them as missing: the summary says "Unknown until prices load", its counts are a dash, each card says "Price not loaded" with no "needs a price" stripe, and no price field shows an example that could read as a saved price.
- The X-ray and a row's details panel on Opportunities no longer run past the table's edge in windows wider than about 1,376 px.
- A toast no longer covers a dollar figure when the other end of the window is clear (the tour's first celebration covered the figure it celebrates on a phone, and three yearly ranges on a desktop). On a phone, toasts dock full width at the foot.
- On a phone, a table wider than its box says so ("Scroll sideways to see every column →"); it was cut at the right edge with no cue.
- On a touch screen, the Security-relevant chip opens its note with a tap. Its hover tooltip never opened on a phone, so the note ("Confirm no detection, dashboard or alert uses this field before production.") was never seen there.
- The Plan's Timeline keeps its lane names in view while its weeks scroll (the bars lost their names once scrolled), and a change of pace brings the week row back into view.
- Methodology's sanity check fits its card in both themes; in light its verdict column was cut at the card's edge.

## [v0.17.3] - 2026-10-04
Fixes from the production review's third verification round (0.17.2). Still a checkpoint build.

### Changed
- The README and the `policies.yml` comment say what the preview grant allows, not only how Money Flow uses it: Cribl's preview API also accepts an inline pipeline configuration, and the grant cannot be narrowed to installed packs. Money Flow still sends only an installed pack's pipeline.

### Fixed
- A CSV import (Meter Reader's report CSV or the daily `date,pipelineId,savedUsd` CSV) of more than 100,000 data rows is refused before any row is read, as Meter Reader's daily-savings file is. The 32 MB file cap alone let about 1.6 million rows through, each a pipeline of the shared report card, parsed and merged on the member's page.

## [v0.17.2] - 2026-10-04
Fixes from the production review's second re-check (0.17.1). Still a checkpoint build.

### Changed
- With three or more sessions open, a refresh that finds another session refreshing now waits for that session's own snapshot (it looks again every 20 seconds while that session holds the lock), instead of settling for any newer copy, which could be a third session's older one and leave the page up to 10 minutes behind.
- The README's screenshot is today's Opportunities page (it showed v0.10.0).

### Fixed
- A route, pipeline or destination named like a built-in JavaScript name (`constructor`, `toString`, `valueOf`, …) reads as that word. The Report card's import dialog crashed on one, and Meter Reader labels and price matching showed or compared the text of a built-in function.

## [v0.17.1] - 2026-10-03
Fixes from the production review's re-check of 0.17.0. Still a checkpoint build.

### Added
- A release step, `npm run check:readme-links` (`scripts/check-readme-links.mjs`): after the push and before the tag, every absolute link and image in the README (the App's overview in Cribl) must answer, an image as an image. The CI release job runs it too.

### Changed
- A plan sent to Meter Reader values each saved GB at the worth its estimate used, kept with each pack when the plan is saved, never at today's prices: after a price edit, a pack that met its plan exactly read Behind in both Apps and Meter Reader opened Below plan alerts. A plan saved by 0.17.0 or earlier is sent only while its prices are the ones it was saved with; otherwise the Send panel says to re-estimate it on the Plan page and save it first.
- A Meter Reader link refused because its figures cannot be stored here no longer suggests importing Meter Reader's file (which holds the same figures); it says so and points to Meter Reader. A link that cannot be read still offers the file.

### Fixed
- Show the math on a phone: its "Low, mid and high" and selected-sources tables are focusable regions, so where they scroll (wider fonts, as on Linux) the keyboard reaches them (axe: scrollable-region-focusable).
- The dev server's `/__open-in-editor` (Vite's) answers only the App's own page, for a file inside the App: any website could make it open any file on disk in the developer's editor. No package is affected (the dev server is not shipped).

## [v0.17.0] - 2026-10-03
Fixes from the production review of 0.16.0 and Meter Reader's integration branch (its 34 items, docs/review/2026-10-03-prod-review.md), and the contract's revision for the release pair Money Flow 0.17.0 and Meter Reader 1.2.0 (docs/INTEGRATION.md section 12: like-for-like measurement, the part day, a window's age, the file caps, a link's length). Still a checkpoint build.

### Added
- Planned vs measured, like for like: when Meter Reader 1.2.0's export carries its Planned vs measured days for the plan, the Report card, its headline and chart, Money Rain and the Proven chips judge each pack on exactly those days: the plan's own sources on the pack's pipeline, each saved GB valued as the plan valued it. An S3-bound pack that met its plan read Behind (its pipeline was valued at a twelfth of what the plan counted a saved GB worth), and a pipeline that also carried an unplanned source read Ahead. Without those days (an older export, a CSV, Meter Reader holding another copy of the plan, or pipelines you picked) the Report card adds the pipelines as before, and Show the math says which and why.
- A plan sent to Meter Reader says what one saved GB to each destination is worth in its estimate: the price per GB, or a storage price × months kept ÷ compression.
- Settings says when its saved settings cannot be read ("Money Flow could not read your saved settings.", with why); Money Flow then runs on the defaults and saves nothing there until it can read them.
- A CI workflow for cribl-apps-public (`.github/workflows/money-flow-ci.yml`, in no package): on a pull request that touches `cc-money-flow/` it runs every suite of `npm run test:all` on GitHub's Ubuntu 24.04 runners (one job per browser project, the phone, Firefox and WebKit included, the screenshots, compared in Playwright's own image against Linux baselines recorded there, and the suite again with Meter Reader absent and with its detection refused) and the benchmark runner's tests; on a `money-flow-vX.Y.Z` tag it then rebuilds both packages from the tagged commit, holds them to the lines `release/SHA256SUMS` records, and keeps them with a `SHA256SUMS` of their own. It publishes nothing.

### Changed
- A pack's first measured day (its go-live, or the day metering started) holds only part of a day, so it is never averaged: a pack exactly on plan read Behind on day 7. Show the math names the day it leaves out.
- A measured window whose newest day is more than 28 days old is not judged: the pack reads No measurement and its cell says when it was last measured ("Last measured Nov 24"), as Meter Reader's Plan does.
- The import reads Meter Reader's files up to 32 MB (13 months of a busy workspace was refused unread at 5 MB). A link longer than 6,000 characters is refused before it is decoded, and a link is inflated 1 KB at a time.
- Show the math: every formula line works out as printed, or says "≈" (the Money River, Prices, the Plan's per-source line, Cost of waiting, the Send panel and the celebration).
- Show the math and the toasts open above any dialog: the Send panel's planned total opens its math in front of the panel, reachable from the keyboard, and the panel's "Downloaded" toast is announced.
- "Proven in Meter Reader" shows only on the plan's own sources (never on another source matched to the pack, or on a manual one); its Show the math names the pipelines it was measured on and where the days come from, and each chip's accessible name carries its pack and figure.
- Prices: the impact card is no longer a live region; one status line says its figure once typing has paused for a second.
- The README (the Marketplace overview) links only what exists (its screenshot, the license and the contract are absolute links), and its Review App and storage tables say what the package does.

### Fixed
- The package installs again: the Leader refuses an Apps path in `policies.yml` ("Apps management paths (/apps, /apps/...) are not allowed in object"), so the `GET /apps` grant 0.16.0 added kept 0.16.0, 0.16.1 and the first 0.17.0 packages from installing or upgrading. The grant is gone; Meter Reader detection still makes its one `GET /apps` per page load, and a refusal reads as "not detected" (the plan file instead of the link, no connected line). The Review App step lists `GET` on 6 paths, `POST /m/:gid/preview` and `POST /system/metrics/query`.
- Two members, or two tabs: every save reads what is stored again and applies only your change (prices and pack choices by key, sources by id, settings by field, plans by id and by the version you opened). A plan changed or deleted in another session is never overwritten or brought back; the Report card's import, mapping and prod date write onto what is stored now, and an import never sets a price someone else changed meanwhile.
- A stored document Money Flow cannot read (written by a newer version, damaged or half-written) is never treated as empty and written over: the page shows a read error and nothing is saved. A large document is written to the chunk range its last version does not use, so a write that fails part-way leaves that version whole, and pressing Import again lands.
- Menus run from the keyboard: Enter or Space on an item of Export, Send to Meter Reader and the Plan, Money River and Report card plan menus now acts (it closed the menu and did nothing); Home and End stay in the menu, and a menu near the bottom of the window lifts the page so every item can be reached.
- A Meter Reader link opened on a first run (`?mfx=` on the Report card, `?plan=` on Plan) goes to its page instead of the welcome tour.
- Plan: saved plans stay reachable, and Meter Reader's `?plan=` link opens them, when nothing can be planned today; an open saved plan keeps its steps when a refresh turns its sources idle; a failed volumes read says so with Retry.
- A refresh that finds another session refreshing shows that session's snapshot within seconds (other sessions lagged 10 to 15 minutes).
- Unsaved edits parked on a saved plan survive a prod date set for it on the Report card.
- An import whose repeated figures add up past what can be stored, or that names a pipeline `__proto__`, is refused with a problem line (it was previewed, then lost).
- Focus returns to the Report card's Import button when a dialog a link opened is dismissed; the import dialog's hidden file input is no longer a Tab stop.
- A chunked stored document is inflated no further than its manifest says (a crafted one could take hundreds of megabytes in every member's tab), and no document over 64 MB is stored.
- The X-ray counts a field named `constructor`, `toString` or `__proto__` like any other (its combined share read as no number), and a rendered Windows `Constructor:` line names its field.
- Dev tooling (in no package): the dev server binds 127.0.0.1, sends no CORS header and serves no tool cache or dotfile; the build org's token is cached in `~/.cache/money-flow/`; Live Preview's hooks (`?init=`, `/ws/compute`, `/package.tgz`) answer only with `CRIBL_LIVE_PREVIEW=1` and only a Cribl origin.
- A menu opened from the keyboard near the bottom of the window could close the moment it opened (the page lifted under it and the browser reported that scroll late); it now opens once the lift has gone by.
- `e` on the Plan page with the Export button scrolled out of view could open the Export menu and close it at once; it now opens once the page has scrolled.
- With reduced motion on, the Plan's Export menu ran off the window instead of lining up under its button.
- The packages carry nothing of the machine that packed them: no macOS metadata (a package built on a Mac whose files carry extended attributes held a hidden `._` twin of every file), no builder's account, no file times or modes of their own; the same commit gives the same bytes on any machine, macOS or Linux, whatever zlib its Node was built with (the gzip is pako's, pinned, where it was Node's own, whose bytes differ between builds). `release/SHA256SUMS` keeps every earlier version's line when a clean checkout builds the current one.
- Tests (in no package): `npm run test:visual` compares screenshots of every page and state in both themes at 1280 × 960, 1440 and a phone; `npm run test:all` runs every suite in the release process's order (docs/DEPLOY.md section 7): lint, format, typecheck, unit tests with coverage (`test:unit` now holds `core/` to its thresholds; `test:coverage` is the same run), integration, smoke, end to end in Chromium and the phone, Firefox and WebKit, the screenshots, then compliance. The wall-clock render budget runs after every other unit file, so it times the App and not the machine; the integration vector's exact compressed bytes are held where the runtime's zlib is the one that made them, and decoding back to the identical plan everywhere.
- Packaging (in no package): `scripts/package.mjs --ref` stages the App folder wherever the repository holds it; in cribl-apps-public (`cc-money-flow/app`) it asked git for `app/` at the top and stopped, so the release build there could not run.
- Tests (in no package): the compliance test, the documentation checks and the benchmark runner's forbidden-string test also run in a `git archive` export of the repository (what a release's "Source code" download is), which has no `.git`; the compliance test's tag check reads cribl-apps-public's `money-flow-vX.Y.Z` tags as well as `vX.Y.Z`; and the package-time test no longer depends on the repository it runs in, so it passes in a shallow clone and in cribl-apps-public's history.

## [v0.16.1] - 2026-10-03
Fixes from the verification of 0.16.0 and Meter Reader's integration branch, and the contract's revision for the release pair Money Flow 0.16.1 and Meter Reader 1.2.0 (docs/INTEGRATION.md: INT-P8's named exception, INT-VAL4's years, INT-CALC9, INT-MF7, INT-WORDS). Still a checkpoint build.

### Fixed
- Show the math: each formula line now prints the figures its result is worked from, so it holds when multiplied as printed ("15,000 GB/day × 24.48% = 3,672 GB/day", "1,945.5 GB/day × $2.46 = $4,785.93"). A reduction prints to the basis point the benchmark carries, GB a day to the thousandth, a price to the millicent; a figure that had to be rounded (a route share measured from bytes) carries "≈" and the math says the results use the unrounded figures. The points line reads "24.45% / 24.48% / 24.5%" when the three differ. Everywhere else figures print as before.
- A date field refuses a year outside 1970 to 2100. A two-digit year typed into the Report card's prod date ("10/27/26" arrives as year 0026) was saved, and from then on the Report card could not draw and emptied the whole App; the field now says "Use a four-digit year, 1970 to 2100." and saves nothing, a date already stored that way is ignored and dropped on the next save, and the chart can no longer pad past the window. The plan's start, a blackout's dates and the import's "Measured on" take the same years, and so does the check every plan goes through before Money Flow offers it to Meter Reader (it now refuses what Meter Reader refuses).
- A page that fails to draw shows what failed in its place, with Try again; the header, the navigation and the footer stay (a failure used to unmount the whole App). The Report card's chart has its own fallback, pointing at the Table view.
- Plan: a step that meets a blackout is drawn as work, a pause across the band (a dashed, hollow stretch with a pause mark) and work again, instead of one solid bar through the freeze; the legend names the pause and the Table view says "paused for <blackout>".
- Plan: the working plan is called "Working plan" on the Plan page, as on the Money River and in the plan menu (its export files keep their names). The plan menu's descriptions wrap instead of being cut mid-word, and the Export menu lines up with its button's right edge.
- Money River: weeks are numbered as the Plan's grid numbers them ("Week 9 · Dec 7, 2026" beside the Plan's "W9 Dec 7"); its legend fits one line at 1280 × 960.
- Plan: pack versions print without a "v" ("Palo Alto Networks 1.1.8"), as everywhere else.
- Send to Meter Reader dates the benchmarks ("As of Oct 2, 2026"), never the build id.

### Changed
- Plan, step 7 "Measure with Meter Reader": while Meter Reader is detected installed it reads "Meter Reader is installed: it measures what this pack really saves." instead of asking to install it (INT-MF7); without Meter Reader it reads as before.
- Status chips carry a glyph before their word, as Meter Reader's do: ▲ Ahead, ✓ On track, ▼ Behind (INT-CALC9).
- Report card: packs are listed in the Plan's order (prod date, then planned savings, highest first), as Meter Reader lists them; its eyebrow, chart and table say "Planned vs measured", Meter Reader's words for the same comparison; a measured figure's Show the math says it counts everything the pack's pipeline carries (Meter Reader's own comparison counts only the plan's sources).
- Two Export menu notes are shorter, so neither ends on one word.

## [v0.16.0] - 2026-10-02
Money Flow and Meter Reader work together: Money Flow finds the opportunities in your workspace and plans them, Meter Reader proves them with the receipt, and each App stays complete alone (docs/INTEGRATION.md, the contract both Apps implement). Every screen the v2 videos film reads whole in their window, 1280 × 960. Still a checkpoint build; v1.0.0 is reserved for the approved release.

### Added
- Send to Meter Reader: on a saved plan, Export → Send to Meter Reader (and the Report card's plan menu) opens a panel that gives Meter Reader the plan as `money-flow/plan` v1 (INT-PLAN): each pack, its prod date (the actual date where set), the plan's own planning estimate, its effort, its security-relevant removals and, per planned source, its ids, destinations, bytes a day on the volume basis and the benchmark band the estimate used (whole basis points; "measured" with the dataset's kind, or Cribl's published figure with where Cribl says so), in canonical JSON with no float, URL, org id or member name. Where Meter Reader 1.2.0 or later is detected, "Open in Meter Reader" is a real link (target _top) to Meter Reader's Plan page carrying the plan as `?mfx=` (raw DEFLATE, base64url, at most 6,000 characters); "Download for Meter Reader" always gives the same JSON as `money-flow-plan-<name>-<date>.json`. When there is no link the panel says why in one line. Every payload is checked with Meter Reader's own rules before it is offered. The working plan's item says "Save the plan to send it"; an edited plan sends the saved version and says so.
- Meter Reader's link on the Report card: `/report-card?mfx=…` (or `#mfx=…`) is read once, decoded defensively (inflate capped at 256 KiB, UTF-8 checked) and checked in full before anything shows, then opens the import dialog with the link as its source: "From Meter Reader 1.2.0, by link", when it was made, what it holds, what it does to the data here ("New", the days it adds and replaces, "Already imported"), the plan it answers, and "Nothing in Cribl changes." Import writes Money Flow's own report card data and the prices you tick; Not now writes nothing; either way `mfx` leaves the address. A link that cannot be read opens "This link couldn’t be read" with its problems and the file import beside them.
- Meter Reader's flow list (`meter-reader/flows`, alone or inside its daily savings): which pipeline runs which pack. Money Flow keeps the newest list and maps each planned pack to the pipelines it names before any name matching; your own mapping still wins. A list that arrives before any completed day says so on the card.
- Proven in Meter Reader: a pack with a measured week that saved money, in the plan the Report card follows, wears the chip on Opportunities, the Pack leaderboard and the Plan ("$9,825/day measured (Dec 2), Ahead" in its tooltip; Show the math on press), with "See the receipt in Meter Reader" into Meter Reader's Ledger for the pack's pipeline. The Report card's live packs carry the same receipt link.
- Meter Reader detection: one `GET /apps` per page load, only where it matters (Settings, the Report card, Send to Meter Reader, a receipt), raced against 5 s, paged, read for each App's id and version only. Settings says "Meter Reader connected (vX.Y.Z)"; while Meter Reader is detected, the Report card's prompt keeps its place and offers "Send this plan to Meter Reader" and "Import from Meter Reader" instead of the install link. A refusal is "not detected", never an error (one diagnostics line in Settings).
- `?plan=<id>` on the Plan page and the Report card opens or selects that saved plan (Meter Reader's "Open the plan in Money Flow"); an unknown id keeps the usual choice.
- Tests: the shared conformance vectors copied from Meter Reader (encoding, plan, calc, replay) and Meter Reader's own payloads (`tests/fixtures/integration/`), unit tests for the payload, the link codec, detection, the parsers and the surfaces, and `tests/e2e/meter-reader-handoff.spec.ts` (both directions, both themes, axe, the video window).

### Changed
- The `GET /apps` grant joins `policies.yml` (both packages), with the contract's comment; it shows in the Review App step.
- Meter Reader's JSON is checked to the contract: a day saves 0 to 10,000,000,000,000 millicents, a price is above zero, ids and names have no control character, lists stay within their caps, and a newer `schemaVersion` stops the read with "Made by a newer Meter Reader (vX). Update Money Flow to read it." One pipeline's days from several worker groups are added together with a note; a true repeat still is, with its own note.
- The report card document keeps Meter Reader's flow list and how each import arrived (`flows`, `imported.via`); no new storage key.
- Every import reads the stored report card data again just before it writes and merges into that copy, so an import made in another tab meanwhile is kept; the same export already there writes nothing.

### Fixed
- Plan: the timeline fits its card whenever it can. A plan of up to 17 weeks shows every lane, every step and every go-live in the card at 1280 × 960 (a 12-week plan drew past the card's edge, its last pack's push to production out of view); a week too narrow for its full date gets a compact header (the month at each month's first day, the week, the day). Each blackout is named at 12 px on its own label above the lanes. Only a plan that cannot fit scrolls sideways, and then the card says so: a fade at the edge it continues past and a line under the chart.
- Plan: pack names are fitted to the label column by their measured width ("Microsoft Windows Events" prints whole).
- Opportunities: the ranked table fits its card at every desktop width. The flags (Security-relevant, Manual, Overridden, Outside published range, Cribl's published figure) sit under the source's own lines, so they are in view; a long pack name keeps its words together and only its version may wrap under it.
- Report card: the headline compares the same packs. Under "Measured per day" (each pack from day 7) it names the planned figure for exactly those packs, with its range and Show the math, and the live packs not counted yet ("Cisco ASA counts from Dec 8"); under "Planned per day today", which packs it adds. The chart says what its measured dots add.
- Report card: the measured column is headed "Measured per day"; each cell still names its window ("7-day average to Nov 24", "Run rate on Dec 2"). A pack measured on its own pipeline says "Measured on this pack's pipeline" instead of Meter Reader's words for the id; Meter Reader's label stays in Show the math and the dialogs.
- Money River: no source or pack name is cut (the source column is as wide as its widest label, measured), and the pack tags no longer touch.

### Changed
- Cost of Waiting: "Every day you wait" is set as a card figure, larger than the live counter's cents.
- Pack leaderboard: the page's eyebrow reads "Benchmarks as of <date>", as the footer does; the benchmark version stays on Methodology and in Settings.
- A needs-configuration reason written by the benchmark runner reads in a few words in a cell or a row ("Needs configuration: your Okta domain and API token (pack variables okta_domain, okta_token)"), made from its own clauses; the whole reason is in the title, the row's details, the plan's configure step and every export. Any other reason is shown whole.

## [v0.15.1] - 2026-10-02
Polish for what the v2 videos show: every pack under a human name, a line on how each pack reduces, ranges that never repeat a value, the X-ray's field matching, and the Cost of waiting in a day's dollars. Still a checkpoint build; v1.0.0 is reserved for the approved release.

### Added
- How it reduces: a measured result in the benchmarks can carry one line (at most 90 characters) on how the pack changed the bytes, written by the benchmark runner only from its own record of the run (a before/after sample, the soak's events and bytes, the field diff). The Pack leaderboard's "What it removes" shows it under the removed fields, or in their place for the packs that trim or reformat rather than drop fields (Cisco ASA "Trims the syslog prefix (timestamp and host before %ASA-)", AWS CloudTrail "Drops read-only calls (Describe, List, Get): 27% of events", Microsoft Windows Events "Rewrites each XML event as compact JSON"); a pack whose output grew says how. Opportunities' "What it removes" and Show the math carry the same line. With no note and no field listed, the cell says only what the measured diff proves ("Reformats events (no fields dropped)"), never a guess. The feed schema and its validator gain the optional `how` field; an unmeasured result can never carry one.
- Beside the live "Since you opened Money Flow" counter, the rate it runs at: "Every day you wait" with the checked rows' savings per day and its range, as a dollar figure with its own Show the math.

### Changed
- The bundled benchmarks are feed `2026.10.02-pilot.4`: the same results, match rules, datasets and numbers as `pilot.3`, with a display name for every pack ("AWS CloudTrail", which the Dispensary lists under its id) and the nine notes on how each pack reduces.
- A range whose two ends print alike prints its value once, everywhere: "24.5% smaller", not "24.5% – 24.5% smaller"; a single measured day's "$7,859 /day", not "$7,859 – $7,859"; three points alike read "24.5% at p10, p50 and p90". Figures, toasts, Show the math, charts, the counter and every export follow the one rule; a range of two different values prints as before.
- The X-ray matches a pack's removed fields to the sample's case- and separator-insensitively, and knows the names Cribl's Palo Alto Networks pack gives each PAN-OS traffic column: on the Palo Alto sample every one of the pack's 19 removed fields now finds its column (6 did), and the share reads 28% where it read 12%.
- Methodology's dataset links read as words ("DataTap 3.0.0 on GitHub"); the address behind them is unchanged.

## [v0.15.0] - 2026-10-02
The story the App tells, made visible: which Cribl packs cut the most, what flows through your Cribl Stream, and which packs carry the most weight in dollars. Still a checkpoint build; v1.0.0 is reserved for the approved release.

### Added
- Pack leaderboard (`/packs`, first in the nav): every pack in the benchmarks ranked by reduction (the mid of its band, best first, with the band, a bar, "Measured" and the kind of data, Cribl's published range beside it and the "Outside published range" flag, what it removes, effort, and a link to the sources it matches on Opportunities). Packs with no reduction measured, that need configuration or have no test data follow with their status, never as 0%. "Rank by: Dollars on your data" sums each pack's savings over the sources it is matched to in this workspace (each source counted once) with its range, Show the math, sources, GB/day, share of the total and the reduction those dollars were made from: the packs that carry the most weight.
- Cribl's published figure stands in where nothing was measured: a matched pack with no measured result for the source type, whose result or pack carries Cribl's published range, is estimated from it (low and high as published, mid the midpoint), labelled "Cribl's published figure (not yet measured)". It ranks, totals and plans like a measured row; a measured result always wins. The feed may carry a pack-level `published` range (schema and validator updated).
- The label follows the money everywhere it appears: the Opportunities row (a chip and a note) and its Show the math (source, Low (published) / Midpoint / High (published)), the Pack leaderboard, the manual-source preview and the pack choice dialog, Methodology, the X-ray's benchmark line ("20% – 40% smaller · Cribl's published figure (not yet measured)"; it said "No test data yet"), the Plan's pack list and step table (a chip beside the pack's planned $/day) with their Show the math and the savings sentence's, the Cost of Waiting's Show the math, the Report card's planned figure and its Show the math, the Money River table and its Show the math (per stream and on the reservoir's per-pack lines), the Money Rain toast and its Show the math, and every export: the Excel Opportunities sheet (status, or a flag on an unpriced row) and Plan sheet (and so the plan CSV), the Jira and ServiceNow descriptions, the copied plan summary and the Leadership PDF (a line under its table naming the packs). A planned pack carries `publishedEstimate: true` only when one of its sources is estimated this way, so stored and golden plans keep their shape.
- "Measured in Cribl Stream" on Opportunities: the live sources, TB/day and worker groups Money Flow measured, the last refresh and the basis, with the three steps (Pack leaderboard → Your streams → Dollars).
- Opportunities `?pack=<id>` lists the sources matched to one pack.
- Mock scenario `published` and its fixture feed (the fixture feed plus two packs with only Cribl's published range); unit and e2e tests for the leaderboard, the fallback and the label on every surface, and an e2e pass over `/packs` (both views) and Opportunities in the fixture, enterprise, unpriced, empty, adds-bytes and published workspaces, in both themes (no unknown as $0 or 0%, never negative money, a range on every figure, axe clean, no sideways scroll).

### Changed
- Show the math prints a published range that starts at zero as "up to N%" everywhere (it read "0% to N%" on a row).
- The Pack leaderboard prints a band once when it reads the same as its mid ("24.5%", not "24.5%" twice), and its dollar notes say "if this pack ships" (or "if every pack ships" for the total): it sums every matched source, checked or not, so Opportunities' "if the selected packs ship" did not fit there.

### Fixed
- The leaderboard's dollars view and its hero printed the pack's best reduction for any source type beside dollars that may rest on another (a pack measured on one source type and estimated from Cribl's published range on the one this workspace has): they now print the reduction those dollars were made from, with its basis.

## [v0.14.0] - 2026-10-02
Measured benchmarks: Palo Alto plus the 10-pack pilot. The App now ships the benchmark runner's signed feed and trusts only its key. Still a checkpoint build; v1.0.0 is reserved for the approved release, and the public feed waits for approval (results are shared with Cribl first).

### Added
- The bundled benchmarks are measured, not illustrative: feed `2026.10.02-pilot.3` (`config/feed-snapshot.json`, byte for byte the runner's `bench/out/money-flow-feed.json`) holds Palo Alto Networks and the 10-pack pilot: 11 Cribl-built packs, 12 results, each a 60-minute soak in its own isolated Cribl Stream 4.20.1 container on DataTap 3.0.0 synthetic data (Apache-2.0). CrowdStrike FDR is measured on the pack's own samples ("Based on the pack's own samples") and Okta REST IO reads "Needs configuration" with its reason. FortiGate and Zscaler make the test data larger as shipped and read "No reduction measured".
- The benchmark runner is in the repository (`bench/`, not part of the App package), with its results, run logs, feeds and `SUMMARY.md`, and its method in `docs/BENCH.md`.
- Methodology: "Notes on these results", one short note per pack version in the feed in use (what the test data could not show); it shows nothing for a feed that does not carry that pack version.
- Tests: `tests/unit/release-feed.test.ts` (the bundled feed is the runner's bytes, verifies only with the release key, and matches the build org's Sources) and `tests/unit/methodology-release.test.tsx` (Methodology and Settings on the release feed).

### Changed
- `config/feed-public-key.pem` and the backend's compiled key are the benchmark runner's public key. A feed signed with the old fixture key (an earlier upload, say) is set aside with a reason, and the bundled feed is used.
- The PRD 9.1 fixture feed and its FIXTURE_ONLY key moved to `tests/fixtures/feed/`. The mock layer loads them only in mock builds (the dev server and Playwright), so every e2e world, upload test and AC-9.3 total is unchanged; the tour keeps its own embedded copy.
- Matching: a feed field rule on `sourcetype` also reads the sourcetype Money Flow derives for a Source with no sourcetype field (an HEC token's, or a Datagen file's name). With the pilot feed's rules, which take the vendor sourcetypes and the Datagen file-name form, Palo Alto, Windows and VPC flow Sources match their packs; Windows Event Forwarding and Edge Windows Event Logs Sources match Windows Events by type.
- Methodology's sanity check prints a steady band once (−20.6%) and a published range from zero as Cribl's "up to 80%", never a 0% low end.

### Fixed
- The packages no longer carry the FIXTURE_ONLY feed key (the hardening review's finding): packaging fails any package, at any version, that carries the fixture key or its seed, or whose JavaScript lacks the release key, and compliance checks the built packages.

## [v0.13.0] - 2026-10-02
Hardening: packs that add bytes (H1) and the fixes from the money, plan, security and UX review. Still a checkpoint build; v1.0.0 is reserved for the approved release.

### Added
- Packs that add bytes (H1): a benchmark reduction can now be negative (−1 to 1 in the feed schema). A pack whose mid reduction is zero or below reads "No reduction measured: this pack's output was about N% larger on the benchmark data" (or "about the same size"): never a dollar, never $0, never negative money. It ranks after the unpriced rows, never enters a total, the Cost of Waiting or Money Rain, leaves its Money River stream unnarrowed with the reason in the table, and can still be planned with "No reduction measured: dates are planned, savings aren't estimated." in the Plan and every export. A pack whose mid is a cut but whose low end grew is priced with that end at nothing saved ("$0 – $X") and says so in Show the math.
- The fixture feed carries two such packs (Web proxy and NetFlow), and the mock `adds-bytes` workspace shows them; `tests/e2e/adds-bytes.spec.ts` and `tests/unit/adds-bytes.test.ts` cover them end to end.
- Opportunities: checked rows priced only by capacity get their own header ("Capacity freed, not dollars") with the Capacity freed figure, and their Show the math leads with "Capacity freed per year" instead of an empty status.
- A release gate: a package of version 1.0.0 or later fails its checks while any shipped file still carries the FIXTURE_ONLY feed key (the runner's real key replaces it at T2b).
- Tests: `tests/e2e/hardening.spec.ts` (keyboard focus after deletes, imports, prod dates, Show the math from a toast and Prices saves; live regions; the X-ray dry-run notice; capacity-only rows) and `tests/unit/report-card-page.test.tsx`, plus unit cases beside every fix below.

### Changed
- A benchmark mid of exactly 0% is "No reduction measured" instead of a scored row worth $0.
- A price of zero is no price: the destination reads "Set a price" (Prices shows "Price unusable"), and a Meter Reader import never offers, ticks or writes a zero price, or one above $1,000,000 a GB.
- A row whose dollars round to under a millicent a day says "Saves less than a thousandth of a cent a day at your prices" instead of "$0 /yr ($0 – $0)".
- Money Rain's net new savings never shows a negative low end: when rows or packs were swapped, the range starts at $0 and Show the math says that end gained nothing. A measured celebration counts a pack only when its average saved money.
- The Report card never prints negative money: a measured average at or below zero reads "Cost more than it saved" (or "Nothing saved"), a day that cost more says "Cost $3.50 more than it saved", and a range whose lowest day lost money starts at $0 with a note.
- Excel shows a positive amount under $1 with its decimals ("$0.0092"), never "$0".
- A saved plan's pack receipt in Show the math lists its sources only while they still add up to the saved figure; otherwise it says the figure is the estimate saved with the plan.
- Choosing a Meter Reader pipeline for a pack moves it there: it no longer also counts for the pack it matched on its own, so measured totals never count one pipeline twice.
- The saved-plan pack picker checks and toggles by pack id; a pack the plan still has but today's rows no longer offer is listed so it can be removed, and a pack whose sources another planned pack already covers is disabled ("Planned under …").
- Changing a tier default in Settings no longer reschedules an open saved plan (or one parked in the session).
- The X-ray tab says before "Dry run" that the dry run sends up to 200 events to your Leader's preview, and its privacy line says so too.
- Countdowns are no longer live regions: Settings' refresh hint and the footer's rate limit tick in plain text, and screen readers hear a change of state once.

### Fixed
- After a failed read, no whole document is written from defaults: the overrides, prices, manual sources and plans index refuse the write ("could not read your saved …, so nothing was changed"); the inline "Use this pack" waits for the overrides read; a Meter Reader import offers no prices when the prices could not be read; the Plan page shows no "prices changed" banner and turns Save and Re-estimate off when the prices could not be read.
- Quick successive writes of one document take turns, so two pack changes, or two Report card prod dates, never drop each other.
- A 429 that also hits KV still backs Money Flow off until the Leader's Retry-After, with the footer countdown and Refresh now off.
- Deleting a saved plan removes its list entry first, so a failed delete can never leave an entry that cannot be opened or removed.
- Upload results file refuses a file older than the benchmarks in use (the bundled ones on a fresh install), and a stored last-good feed that does not verify is never the baseline.
- KV keys never hold a "." or ".." segment, and a saved plan's id must be one key segment that matches the document it names, so no plan action can address anything outside the App's KV.
- Jira, ServiceNow and plan-sheet CSV fields that start like a formula get a leading apostrophe.
- Keyboard focus never falls to the page body when the control that held it goes away: after deleting a manual source, the first Meter Reader import, setting or resetting a prod date, closing Show the math opened from a Money Rain toast (the toast now waits while that drawer is open), and Save or Discard on Prices.

## [v0.12.0] - 2026-10-02
Wave 5: Tier 2 on the App side. Still a checkpoint build; v1.0.0 is reserved for the approved release.

### Added
- Report card (T2a, PRD FR-5): follows a saved plan (the member's pick, else the plan open on Plan, else the newest) and sets each pack's planned $/day, with its range and "Planning estimate", against what Meter Reader measured, labelled "Measured": Not live yet, Measuring (day N of 7), On track, Ahead, Behind or No measurement (`core/reportCard.ts`), plus "Not estimated" for a measured pack the plan has no estimate for. Each pack's prod date starts as the plan's and takes the actual date in place (`plan.actualProdDates`; "Use the plan's date" clears it). A chart on the vault card sets the planned cumulative $/day (gilt, dashed, with its band) against the measured line (mint, solid), with a scrubber, Show the math per date and a Table view. Packs that turn On track or Ahead celebrate once, "Measured".
- Meter Reader import (T2a, import mode, SPIKE (d)): "Import from Meter Reader" reads Meter Reader's own Report card CSV, the daily CSV `date,pipelineId,savedUsd`, or the proposed MR-1 daily savings JSON in the browser (`core/meterReader.ts`). A file Money Flow cannot use lists every problem by line and writes nothing; imports merge by pipeline and day into KV `reportCard`; a pack's pipelines are matched automatically and can be chosen by hand.
- Not connected (AC-5.1.1): exactly one prompt on the Report card ("Install Meter Reader to track actual savings.", the install link in a new tab, the pairing line, the import button) and one line on each plan's step 7. Settings shows a status: "Not connected", what was imported and when, and "Meter Reader connected (vX.Y.Z)" when the file names Meter Reader's version.
- Prices from Meter Reader (AC-2.5): an import offers Meter Reader's per-GB prices, ticked only for destinations with no price here; those show "From Meter Reader" on Prices, and changing one asks "Change here only" first. Nothing is ever written to Meter Reader.
- Leadership PDF (T2c, AC-4.4.1): Export ▾ → Leadership PDF, one US Letter page drawn in the browser with pdf-lib: the plan's total planned savings and range, the cumulative savings line as vector paths, the packs with production dates and dollars (top ten, then "and N more"), the tier, start and the cost of a 4-week delay, all "Planning estimate", with five embedded fonts (IBM Plex Sans and Mono as IBM's own Latin1 WOFF files, a WinAnsi instance of Fraunces SemiBold). The same plan gives the same bytes.
- Tests: `tests/e2e/report-card.spec.ts` (S19, both states), `leadership-pdf.spec.ts`; unit `meter-reader`, `report-card`, `report-card-ui-model`, `export-pdf`, `leadership-pdf-export`. The sweep adds ten Report card states in both themes (a saved plan not connected; after the report CSV; the MR-1 chart with its tooltip and its Table view; an actual prod date; the import dialog's preview and its problems; the mapping dialog; Prices with "Change here only"; Settings connected) and holds dialog text to the no-$0 rules; the census counts and presses every Report card figure before and after an import; `screens.spec.ts` writes the Report card screens at 1440 and 400 px.

### Changed
- `vite.config.ts` stubs pdf-lib's standard fonts and PNG decoder, which the PDF never uses (about 245 KB less in the package).
- A Plan-page Save keeps the actual prod dates recorded on the Report card for the packs still in the plan.

### Fixed
- The Report card chart's description named the planned dollars per day with no range and no Show the math; it now names dates only, as the Plan page's chart does (the sweep's new chart state caught it).
- Importing Meter Reader's report CSV and its daily export for the same days counted each pipeline twice (the CSV names a pipeline by its label, the export by its id): a label is now read as the one route or planned pack pipeline it names, and a whole measured day is never replaced by a run rate.
- The Report card chart's Table view drew its row rules out of line in the Packs column (a table cell laid out as flex).

## [v0.11.0] - 2026-10-02
Wave 4: the Epic features (D23, D26). Still a checkpoint build; v1.0.0 is reserved for the approved release. README and the tour do not mention these yet: `CHECKPOINT.md` reveals them.

### Added
- Money River (E1): `/river`, "Money River" in the nav after Opportunities. The workspace as an animated flow map on the vault card, sources to packs to destinations, stream width by GB/day and colour by what the stream costs per day at the destination price. A week scrubber and a play button roll the plan out in about 8 s, peeling each planned stream into a gold Saved reservoir whose figure at the last week is the plan's final cumulative mid; reduced motion steps whole weeks; a Table view lists every stream; a plan picker reads any saved plan (read-only). `core/river.ts` is pure and deterministic, with conservation checked per pack node in GB/day and in pixels. A thin river strip under the Opportunities hero links to it.
- Cost of Waiting (E2): "Since you opened Money Flow" in the Money on the table card, the checked rows' savings accrued by the second with their range and Show the math (no timer while the tab is hidden; every 10 s under reduced motion). On Plan, a "Cost of waiting" panel: a 0–26 week slider, the savings forgone over the next 12 months as a range, an SVG bar sparkline with a Table view, and "Waiting until after <blackout> costs …" when a blackout pushes the plan. The Excel Assumptions sheet and Copy plan summary carry the cost of a 4-week delay. `core/delay.ts` is integer millicents, zero at 0 weeks and non-decreasing in weeks.
- Bytes X-ray (E3): an "X-ray" tab in every row's expand and the full `/xray?row=<key>` page. Drop a sample (JSON lines or arrays, Cribl sample events, Windows XML or rendered text, PAN-OS CSV, key=value, CSV) or pick a Datagen sample the group already has; it is parsed in the browser into an SVG treemap of bytes by field, the matched pack's removed fields in gold and security-relevant ones ringed, with "On your sample, the fields this pack removes are N% of the bytes" beside the benchmark range. With the pack installed, "Dry run with the installed pack" measures bytes in and out through the Leader's preview, which saves nothing. Malformed lines are skipped and counted; files over 10 MB are refused before reading.
- Money Rain (E4): when checks, Build the plan or Save plan lock in net new savings (at least $1,000/yr above the session's high, debounced 600 ms, at most once per 10 s), a "Net new savings" toast with its range, label and Show the math, and a 2.5 s rain of gold notes and coins (at most 60 SVG pieces, CSS transforms, pointer-events none). Reduced motion shows the toast only; Settings → Celebrations turns it off. The tour's step 1 celebrates once; a report-card hook is ready for T2a.
- Show the math labels a measured receipt "Measured" (solid outline, mint dot), as it labels a planned one "Planning estimate".
- `tests/e2e/epic.spec.ts`: the four features together (one toast, the ticker at the same $/day, the river's gold threads and reservoir, Celebrations off silencing only the toast, and no request carrying a dropped sample until the dry run).
- `tests/e2e/sweep.spec.ts` covers `/xray` and the Epic states in both themes; `math-links.spec.ts` counts the live ticker as a figure and a link; `screens.spec.ts` adds the Epic screens at 1440 and 400 px.

### Changed
- `screens.spec.ts` refreshes the README image only with `MF_README_IMAGE=1`.
- The X-ray's synthetic `.log` fixtures are tracked (they were caught by `*.log`).

## [v0.10.0] - 2026-10-02
Wave 3: Tier 1 and Tier 1.5. Still a checkpoint build; v1.0.0 is reserved for the approved release.

### Added
- Manual sources (T1a, FR-1.2): add, edit and delete a source Money Flow cannot measure yet (name, source type from the benchmarks, GB/day, destination). It is matched, estimated and ranked exactly like a measured source, marked "Manual", and kept in KV `manualSources`. GB/day accepts 0.001 to 1,000,000 with up to three decimals; anything else shows "Enter a number between 0.001 and 1,000,000." and writes nothing. Delete runs only from a confirmation that names the source. A "Manual sources" card sits under the ranked table, and in the empty workspace.
- Pack choice (T1a, FR-1.3.2): pick any Cribl pack in the feed, or "No pack", from a row's Pack cell. The pick is stored in KV `overrides` (`packId`, `at`, `by` = the Cribl username), the row shows "Overridden", and the pick survives a feed refresh. The dialog previews the row's Show the math for the pick.
- Money on the table (T1a, FR-1.7): checking or unchecking moves the total and its range within 100 ms (measured with performance marks, also at 200 sources). Capacity-priced savings show on their own "Capacity freed ≈ $X/yr" line and never enter the dollar total.
- Jira CSV and ServiceNow CSV in Export ▾ (T1b), with a "Download" / "Copy" sectioned menu, the importer settings in the note after each download and in a new "Importing" section of the workbook's Assumptions sheet (`core/export/importNotes.ts`), and "Copy CSV" offered when a browser blocks the download.
- Copy plan summary (T1b, AC-T15.3): total, range, first production push, the "By <date>" sentence and "Planning estimate" on the clipboard.
- Saved plans (T1c, FR-3.7): save up to 20 plans (`plans.index`, `plan/<id>`), open, duplicate, rename and delete (behind a confirmation naming the plan and its key). A saved plan keeps its estimates snapshot and reopens identical after a reload or a feed refresh, with "Re-estimate with today's benchmarks" when the feed or prices changed.
- Cumulative savings chart (T1c, FR-3.6): an SVG line of dollars per day by go-live date with its low–high band, a keyboard scrubber that opens each step's Show the math, a table view, and "Planning estimate" on every date.
- Show the math deep links (T1d, AC-T15.1): `?math=<rowKey>` opens a row's receipt and stays in step with the drawer (replace navigation, other parameters kept), so the Cribl URL bar can share it; a link to a row the workspace does not have says so. `tests/e2e/math-links.spec.ts` counts dollar figures = Show the math links on every route, workspace and tour step.
- What it removes (T1d, FR-1.6): a one-line summary, the security-relevant fields first in their own group with the SPEC 17 sentence, what each reduction method does, and a counts note for sampling, suppression and aggregation.
- README (T1d, AC-T15.2): the full Marketplace overview (13 PRD §13 sections in order, the what-exists table, the platform-usage tables, Jira and ServiceNow import steps); the root README points to it.
- `tests/e2e/sweep.spec.ts` (W3-INT): wave 3 states in both themes (a deep link, a missing row, the Capacity freed line, the manual-source form on an unpriced workspace, the note after a CSV download), and the pointer resting on a row's dollar figure before every axe pass.
- `tests/e2e/screens.spec.ts` (W3-INT): every page and wave 3 state at 1440 and 400 px, light and dark, written to `evidence/screens/` with `MF_EVIDENCE=1`.

### Changed
- "By <date> you'll be saving" is dated by the plan's last production push everywhere (`core/savingsBy.ts`): the Plan page, Copy plan summary and the tour (supersedes D200).
- Compliance README checks hold both platform tables to `policies.yml`, the KV keys, the backend-variant config, the feed host, the export headers, the shortcuts and the dependencies, and the 13 sections in order (AC-T15.2).
- Delete confirmations (saved plans, manual sources) use Capra's danger primary, darkened App-wide to AA in `ui/styles/capra.css`.
- The cumulative chart's Chart | Table toggle takes the vault card's palette (gilt selected) instead of Capra's light panel and blue.
- A plan saved with no prices stored prints "No prices set" in the workbook's Assumptions sheet (`NO_PRICES_AT` moved to `core/types.ts`).
- `Override.by` is the Cribl username (the display name only when there is none).

### Fixed
- A refresh in flight could write `meta` back over a rejected results-file upload, so the footer stayed "ok" after a tampered upload: the two meta writers now take turns (`ui/data/runtime.ts`).
- Closing Show the math before its `?math=` write landed reopened the drawer: location writes are tracked as an ordered queue, so the App's own late write is never read as a pasted link (`ui/components/mathLink.ts`).
- Dark theme: hovering a dollar figure dropped gold text to 3.5:1 on the gilt wash; the hover now keeps the figure's own ground (5:1 or better) and draws a gilt hairline 3 px outside it.
- Light theme: a neutral figure on the vault card (the "Capacity freed" amount) kept the page's ink colour and nearly vanished on the dark card; the vault card now sets its neutral money colour too, and the sweep checks every vault-card figure's lightness (axe cannot rate text on the card's gradient).
- A Show the math write skipped because a navigation was landing is forgotten, so a later link to that row still opens it.
- "24 h basis" no longer splits across lines in Show the math receipts on a phone; a missing row's drawer is titled by its input id, not "source".

## [v0.9.0] - 2026-10-02
Checkpoint build: the first package installed on the build org (`money-flow` 0.9.0, front-end release). v1.0.0 is reserved for the approved release.

### Added
- Packaging (U6): `scripts/package.mjs` builds `release/money-flow-X.Y.Z.tgz` (front-end release: `policies.yml` only) and `release/money-flow-X.Y.Z-backend.tgz` (adds `backend.yml`, `schedules.yml`, the two backend bundles and `proxies.yml`), from a staged copy with the mock layer and MSW worker removed, and refreshes `release/SHA256SUMS`. Every package carries `static/LICENSE` and `static/THIRD-PARTY-LICENSES.md` (the bundled npm packages from Vite's license list, and the OFL texts of the five font families it ships).
- `scripts/deploy.mjs`: upload, pre-install check, install or upgrade through the Apps API, with org ids and hosts redacted from every log.
- `tests/compliance.test.ts` and `tests/forbidden.txt`: Apache-2.0 LICENSE, README title/tagline/credit, version = CHANGELOG = package, GET-only grants plus the two body reads, no write call in code, exactly one proxy host, forbidden strings with the D12 URL allowlist in every publishable file and every package file, no org id, org host or credential shape, bundles ≤ 5 MB, no run-time code generation, no mock layer, notices and checksums.
- `core/feedSource.ts`: the published feed's URL (`raw.githubusercontent.com`, the `cribl-apps-public` feed folder), shared by `backend/feedRefresh.ts` and the compliance test.
- `scripts/live-checks.ts`: the live AC-1.1.1, AC-1.1.2 and AC-2.1 checks against the installed App (evidence in `evidence/logs/`).
- `tests/unit/perf.test.tsx` (AC-P3): 200 sources render from KV, a 20-pack plan, and the feed size, with `MF_EVIDENCE=1` writing `evidence/logs/ac-p3.json`.
- Wave 1 core: integer-millicent money, formatting, pricing, source estimates, ranking and totals (`core/money.ts`, `format.ts`, `pricing.ts`, `estimate.ts`, `presets.ts`).
- Signed benchmark feed verification (Ed25519 + JSON schema), SPEC 9 matching, the security field list, the KV document store and the counting HTTP wrapper (`core/feed.ts`, `match.ts`, `securityFields.ts`, `kv.ts`, `http.ts`, `canonical.ts`), with a fixture-only signed feed snapshot.
- Rollout scheduler with business calendar, blackouts, tiers and golden Low/Medium/Super plans (`core/calendar.ts`, `plan.ts`).
- Exports: four-sheet xlsx, Jira Cloud CSV (importer layout), ServiceNow import-set CSV, plan summary text (`core/export/*`).
- Spike findings (`SPIKE.md`), the read-only `config/policies.yml`, scrubbed Cribl API fixtures and `scripts/capture-fixtures.sh`.
- `tests/integration/wave1.test.ts`: the full chain from the bundled feed to every export on the PRD 9.1 fixture.
- App shell (wave 2, U0): router with a Safari MemoryRouter fallback, header with the 28 px icon and nav, PRD 8 footer with the D13 credit line, the CRIBL_APP_LAYOUT theme bridge, and page stubs for Opportunities, Plan, Report card, Prices, Methodology and Settings (`ui/App.tsx`, `ui/components/*`, `ui/pages/*`).
- Brand system: DESIGN §1 tokens for both themes, the type scale, and bundled Fraunces 600 / IBM Plex Mono / IBM Plex Sans (OFL texts in `licenses/`).
- Shared components: `Money` (mid + range, one Show the math trigger), `Range`, the Show the math drawer, `PlanningEstimate`, `StatusChip`, `EmptyState`, skeletons, `InlineError`, a toast host, and the DESIGN §9 keyboard map with its `?` sheet.
- Data layer: a KV-backed runtime with the hydration gate, the bundled-or-stored signed feed, `useEstimates()` (match → estimate → rank), and the refresh controller (every 5 min while open, Refresh now at most once per 60 s); `core/refresh.ts` is a stub until U1.
- Mock layer for dev and e2e (MSW): scenarios `fixture`, `firstrun`, `empty`, `unpriced`, `429`, `many200`, plus e2e helpers (theme, scenarios, KV reload, axe) and `tests/e2e/shell.spec.ts`.
- `core/format.ts`: `fmtUsdPrecise` (exact formula amounts) and `fmtPct` (reductions, never 0% for an unknown).
- Volumes runtime (wave 2, U1): the real `refreshVolumes` snapshot (group listing, round-robin inventory, two metrics queries, ≤ 30 calls, the `lock/snapshot` lease, the 429 path), `core/adapters/{inventory,volumes}.ts`, and the backend variant (`backend/snapshot.ts`, `backend/feedRefresh.ts`, `config/backend-variant/*`).
- Opportunities (U2): the Money on the table vault card, the unpriced banner, the ranked table with per-status dollars or reasons, row details (what it removes, inline Show the math, alternatives), idle sources, the empty workspace and the DESIGN §9 keys.
- Prices (U3): one card per destination with the three price models, presets as placeholders ("Use example"), SPEC 17 validation, one all-or-nothing save, and a per-destination savings preview.
- Plan (U4): attributes with tier edits, a pack picker, the SVG week-grid timeline with a table toggle, the step table with validation checklists, the savings card, the Super banner, and Export > Excel workbook / Copy plan as CSV.
- First-run tour, Methodology and Settings (U5), with `scripts/readability.mjs` (grade 4.1).
- Report card: the not-connected state (one vault card: "Install Meter Reader to track actual savings.").
- `config/proxies.yml` declares the one feed host (`raw.githubusercontent.com`, pinned to the feed folder).
- `tests/e2e/sweep.spec.ts`: every route × five workspaces × both themes for no $0/0%, ranges on every figure, axe and sideways scroll.

### Changed
- The feed schema is compiled ahead of time (`scripts/build-feed-validator.ts` → `core/feedValidator.generated.ts`, ajv standalone; the schema moved to `core/feedSchema.ts`), so no bundle generates code at run time under the App frame's content security policy; the release bundle no longer carries the ajv compiler.
- The release package declares no external host: the build org's Standard plan refused it with `proxies.yml` ("App proxies require an enterprise license/plan"). `config/proxies.yml` ships in the backend variant, which needs an Enterprise plan anyway; the release uses the bundled signed feed and Settings → Upload results file.
- The tour gate is mounted once in the shell, so a first open shows the tour on whichever route the member lands on.
- The checked rows are kept in KV (`opportunities.selection`) by the data runtime for both Opportunities and Plan; the first-visit pre-check writes nothing.
- Capra's accent text, primary and secondary buttons are re-pointed once, App-wide, for WCAG AA in both themes (`ui/styles/capra.css`).
- "1 destination is unpriced. Set a price" reads in the singular; "Set a price" links open that destination's card on Prices.
- The Plan's step table sits beside the attributes; table dollar figures stack their range under the mid.
- Money lint now exempts exactly `core/format.ts` and `core/export/units.ts`, and also bans dividing by `100_000`/`MC_PER_DOLLAR` elsewhere.

### Fixed
- xlsx status no longer doubles "Needs configuration:"; zero-length plan steps export as points instead of overlapping 4-hour spans.
- A visually hidden label in a scrolling table widened the phone layout (390 px measured 501 px); scroll boxes now contain it.
- Show the math fits a phone (Capra's 400 px drawer floor clipped it at 390 px); toasts no longer fade (axe read their text mid-fade).
- Security-relevant chips are real buttons (react-aria warned on a focusable span); dark-theme row dividers are visible on cards.

## [v0.1.0] - 2026-10-01
### Added
- Scaffolded the Cribl App with `@cribl/apps` 1.1.0 (Vite, React 19, TypeScript strict, Capra), front-end only.
- Shared contract `core/types.ts` (money is integer millicents).
- Money lint test: no `/ 100` or `toFixed` in `core/` outside `core/format.ts`.
- Toolchain: Vitest, fast-check, Playwright, MSW, oxlint, prettier.
- `scripts/cribl-api.mjs`, a small Cribl.Cloud API client that reads credentials from `MF_ENV_FILE`.
