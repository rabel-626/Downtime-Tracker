# Abel Engineering Downtime Tracker

Documentation snapshot: September 2, 2026

## Purpose

The Abel Engineering Downtime Tracker is a standalone browser-based tool for collecting manufacturing downtime studies. It records study context, observation time, downtime events, reason-code definitions, notes, action items, summary statistics, production impact, Pareto results, and an executive report.

The application is contained in one HTML file. It does not require installation, a web server, a database, or an internet connection for normal use.

Current application file:

`7c22aa1c-5b3f-4574-8cf5-737f0126f3a7.html`

## Recommended browser

- Use current desktop Microsoft Edge or Google Chrome.
- JavaScript must be enabled.
- Chrome or Edge is required for the best direct-folder save experience.
- Other modern browsers can run the core tracker, but directory selection, direct folder writing, sharing, and local-file storage behavior may differ.
- For PDF output, enable **Background graphics** in the browser print dialog to retain the complete Abel Engineering theme.

## Quick start

1. Open the HTML file in Edge or Chrome.
2. Complete **Study Information**, especially location, date, shift, line or machine, product or job, observer, and production speed.
3. Open **Downtime Code Setup** and load the supplied Slitter Study template, import a code CSV, paste a code table, or enter codes manually.
4. Choose **Timer Capture** for a live study or **Manual Entry** for an existing handwritten or spreadsheet study.
5. Record downtime events and add the applicable category, cause code, and note.
6. Review **Data Quality Checks**, **Observation Summary**, and **Action Items / Follow-Up**.
7. Review the statistics, production analysis, Pareto, and executive report.
8. Export the study as JSON before closing or resetting the tracker.

## Main navigation

The top and bottom navigation bars provide the same four views:

| Tab | Purpose |
| --- | --- |
| Information | Study identification and traceability fields. |
| Load / Save | Load, recover, validate, save, and export study data. |
| Study | Select the entry mode, record events, maintain reason codes, and enter notes or action items. |
| Results | Review calculated metrics, Pareto analysis, production impact, and the executive report. |

## Study information

The tracker captures the following primary context:

- Location
- Study date
- Shift
- Line or machine
- Product or job
- Observer
- Operator, line, or supplemental note
- Production speed in units per hour
- Study objective

These fields are carried into the JSON study file and applicable report exports.

## Entry modes

### Timer Capture

Use Timer Capture while observing a live process.

1. Select **Start Study** to begin observed time.
2. Select **Start Downtime** when downtime begins.
3. Select **Stop Downtime** when the event ends.
4. Assign the event category, reason code, and notes.
5. Stop and restart the study as needed. Each completed observation period is retained as a separate study segment.

An active downtime event requires the study timer to be running. If the study is stopped while downtime is active, the tracker closes the active event automatically.

### Manual Entry

Use Manual Entry to transcribe a completed study.

1. Enter the total **Actual Study Duration**.
2. Select **Add Manual Row** for each downtime event.
3. Enter the event duration, category, cause code, and notes.

Manual total-duration formats:

| Entry | Interpretation |
| --- | --- |
| `120` | 120 minutes |
| `120:00` | 120 minutes, 0 seconds |
| `2:00:00` | 2 hours, 0 minutes, 0 seconds |

Manual event-duration fields use `[MM]:SS`; a plain numeric event duration is interpreted as seconds.

## Downtime code setup

Each code definition can include:

- Code number
- Category
- Cause-code abbreviation
- Description
- Include-in-summary setting

The combination of category and cause code must be unique. The same abbreviation should not be entered twice under the same category.

Available setup methods:

- Load the built-in **Slitter Study** template.
- Add individual codes manually.
- Import a CSV file.
- Paste a comma-separated table or a tab-separated range copied from Excel.
- Export the current code table as CSV.

Accepted import headings include **Code #**, **Category** or **Group**, **Code Abbr**, **Code Desc**, and **Include in Summary**.

Codes with **Include in Summary** turned off remain in the event log but are excluded from the Tracker's included-downtime statistics, production analysis, and Pareto.

## Event log

The event log retains:

- Event number
- Start and stop display times
- Start and stop timestamps
- Downtime category
- Downtime cause code
- Code description
- Duration in formatted and numeric seconds
- Include/exclude status inherited from the code definition
- Reason or notes

The **Details** control allows category, cause, and notes to be edited together. Events can also be removed from the details dialog.

**Copy Selected Columns** creates an Excel-ready tabular copy. The column-selection menu controls which fields are copied.

## Data quality checks

Before saving the official study, review the Data Quality Checks panel. It verifies or flags:

- Whether observed study time exists
- Whether production speed is entered
- Completed events without reason codes
- Used codes without descriptions
- Duplicate category and cause-code combinations
- Completed events without notes
- Included downtime exceeding included study time
- A downtime event still running
- A study timer still running

Warnings do not automatically prevent export. Resolve them or document why the condition is acceptable.

## Calculations

| Metric | Current calculation |
| --- | --- |
| Total logged study time | Manual total in Manual Entry mode, or accumulated timer segments in Timer Capture mode. |
| Excluded event time | Sum of completed events whose code is excluded from summary. |
| Included study time | Total logged study time minus excluded event time, never below zero. |
| Included downtime | Sum of completed included-event durations. |
| Included uptime | Included study time minus included downtime, never below zero. |
| Study-scope utilization | Included uptime divided by included study time. |
| Included events per hour | Included event count divided by total observed study hours. |
| Estimated production loss | Production speed × included downtime hours. |
| Projected units, Timer Capture | Production speed × included uptime hours. |
| Projected units, Manual Entry | Production speed × included study hours. |

The Pareto ranks included category/cause combinations by total downtime and calculates event count, total duration, percentage of included downtime, and cumulative percentage.

## Saving and exports

| Output | Intended use | Reload support |
| --- | --- | --- |
| JSON study | Authoritative portable study file and preferred backup. | Full reload; recommended. |
| Direct folder JSON | Writes the same study payload to a selected local or synchronized folder. | Full reload. |
| Browser copy | Convenient same-browser working copy. | Full reload on the same browser/profile. |
| Autosave recovery | Automatic local recovery snapshots for interrupted work. | Load or export from the recovery list. |
| CSV report | Human-readable data exchange, Excel review, and limited recovery. | Supported, but may not restore every field perfectly. |
| Excel-compatible `.xls` | Formatted dashboard and detailed study tables. | Report only; do not use as a reload file. |
| Full-page PDF | Printable record of the current tracker view. | Report only. |
| Executive Report PDF | Focused management report with production metrics and Pareto charts. | Report only. |
| Clipboard JSON | Quick transfer into a text file, message, or another storage location. | Save as `.json` before reloading. |

### Recommended recordkeeping

- Treat the exported JSON as the source-of-truth study file.
- Store JSON files in a controlled folder organized by location, line, year, and month.
- Use CSV, Excel, and PDF as review or presentation outputs rather than primary backups.
- Do not rely exclusively on browser storage; it is tied to the current browser, profile, device, and local-file origin.
- Export before resetting the tracker, clearing browser data, changing computers, or replacing the HTML application.

## JSON compatibility

The current study payload uses:

- `fileType`: `MPG_Downtime_Tracker_Study`
- `version`: `2`

The legacy `MPG` identifiers are intentionally retained for compatibility with existing study files, browser saves, folder conventions, and downstream analyzer logic. They do not indicate the current visual brand and should not be manually changed inside saved JSON files.

The version 2 payload contains the event log, reason-code definitions, action items, study information, observation summary, timer segments, active timer state, entry mode, manual duration, production speed, operator note, filename information, suggested folder, and form values.

Older tracker JSON files are supported. Files that predate category data may open in a legacy reason-code-only event view.

## Autosave and browser storage

- Changes schedule an autosave after approximately 0.6 seconds of inactivity.
- An additional recovery save runs approximately every 5 seconds while meaningful data exists.
- The tool also attempts to save when the page becomes hidden or is closed.
- Each browser tab receives its own recovery identifier.
- Up to 60 indexed recovery records are retained.
- Recovery copies can be viewed, loaded, exported, or deleted from **Load / Recover**.

Private browsing, storage restrictions, browser cleanup, moving the file, or changing browser profiles may make local recovery copies unavailable. JSON export remains the safest portable backup.

## Privacy and network behavior

Normal study collection and analysis occur locally in the browser. The application does not upload studies to an Abel Engineering server and does not call an external data API.

Data leaves the browser only through a user-selected action such as downloading an export, saving to a selected folder, copying to the clipboard, printing, or opening the device share sheet.

## Troubleshooting

### The study will not start

- Confirm **Timer Capture** is selected.
- If Manual Entry is active, timer controls are intentionally hidden.

### Downtime will not start

- Start the study timer first.
- Confirm another downtime event is not already active.

### Direct folder save is unavailable

- Use desktop Edge or Chrome.
- Re-select the folder and grant read/write permission.
- Use **Export JSON** if folder access remains unavailable.

### A JSON file will not load

- Confirm it is a valid Tracker study JSON file.
- Do not load an Excel or PDF report through the JSON loader.
- If it is an older export, try the legacy view offered by the tracker.
- Use CSV recovery only when the original JSON is unavailable.

### Results show missing production metrics

- Enter a positive production speed in units per hour.
- Confirm the study has observed time.
- Confirm completed events have valid durations.

### Included downtime exceeds study time

- Check the manual total or timer segments.
- Look for a missed stop click or an incorrectly entered event duration.
- Review which reason codes are included or excluded.

### PDF colors or charts are missing

- Enable **Background graphics** in the print dialog.
- Use Edge or Chrome and choose landscape orientation when appropriate.

## Branding

The September 2026 visual update applies the Abel Engineering identity throughout the interface and visible report headers:

- Embedded Abel Engineering logo
- Carbon-black interface surfaces
- White and silver typography
- Electrical-violet accents and chart palette

The logo is embedded in the HTML, so no separate image file is required during normal use.

## Change record

### September 2, 2026

- Replaced the previous visual branding with Abel Engineering branding.
- Embedded the supplied logo directly in the standalone HTML file.
- Reworked interface and visible report colors to the Abel Engineering black, white, silver, and violet palette.
- Preserved application behavior, study schema, browser-storage keys, legacy filenames, and analyzer compatibility.

## Maintenance notes

- Preserve the `MPG_Downtime_Tracker_Study` file-type value unless a coordinated migration is implemented in both the Tracker and Totalizer.
- Preserve backward-compatibility handling when changing the JSON structure.
- Validate Timer Capture, Manual Entry, JSON round-trip, CSV recovery, browser recovery, all exports, and Totalizer import after functional changes.
- Update this README whenever fields, calculations, import rules, export contents, or browser-storage behavior change.

