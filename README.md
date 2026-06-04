MPG Downtime Tracker
A standalone browser-based downtime study tool for capturing, categorizing, validating, analyzing, saving, and exporting manufacturing downtime events.
The tracker is designed for MPG downtime studies where an observer needs to capture downtime events in real time, assign reason codes, review data quality, estimate production impact, and export clean study records for reporting or later multi-study analysis.
The app runs as a single HTML file and does not require Excel macros, a database, a server, or external software dependencies.
---
Purpose
The MPG Downtime Tracker is used to perform individual downtime studies on production equipment or manufacturing lines.
It supports both real-time timer capture and manual study entry, allowing users to record downtime events, assign standardized downtime codes, evaluate production impact, and produce export files that can be reviewed individually or loaded into the MPG Multi-Study Downtime Totalizer.
Primary use cases include:
Conducting line-side downtime observations
Capturing start/stop timing for downtime events
Assigning reason codes and cause notes
Reviewing quality checks before saving or exporting
Estimating utilization and production loss
Creating a clean JSON study file for long-term storage
Exporting CSV, Excel, and executive PDF reports
Producing source files for multi-study Pareto analysis
---
Key Features
Study Information
The tracker captures study context for traceability and reporting.
Study fields include:
Location
Study date
Shift
Line / machine
Product / job
Observer
Study objective
Operator / line / note
Production speed
This information is included in saved study files and exported reports.
---
Study Timer
The study timer captures total observed study time.
Supported behavior:
Start and stop the study multiple times
Add multiple observation segments into one total observed time
Prevent downtime capture before the study timer is started
Automatically stop active downtime when the study is stopped
Preserve study timing segments in exported JSON and CSV records
This allows users to pause and resume a study while still calculating total observed time accurately.
---
Downtime Event Capture
Downtime events can be captured in real time using the Start/Stop Downtime controls.
The event log records:
Event number
Start time
Stop time
Downtime code
Category / cause information
Duration
Duration seconds
Start timestamp
Stop timestamp
Notes
The event log includes full-width downtime capture bars near the event table so users can start and stop events without scrolling back to the top of the page.
---
Manual Entry Mode
The tracker supports manual downtime study entry when timer-based capture is not practical.
Manual mode is useful when:
Downtime data was collected elsewhere
The observer needs to enter summary events after the fact
Study duration is known manually
Timer capture was not used during the original observation
Manual entries are still included in the summary, Pareto, production analysis, and exports.
---
Downtime Code Setup
The Downtime Code Setup section allows users to define standardized downtime codes before or during a study.
Each code can include:
Code number
Code abbreviation
Category
Cause / code description
Include / exclude setting
Optional notes
The tracker can also import and export downtime code lists, helping maintain consistency across studies.
Supported code-management options include:
Add downtime code
Load a template
Export code CSV
Import code CSV
Paste code CSV
Clear reason codes
The data quality checks flag duplicate reason-code/tag abbreviations so each code remains unique.
---
Category / Cause Structure
The tracker supports a structured downtime-code format with category and cause fields.
This helps organize downtime events into more consistent buckets, such as:
Robot / automation
Material handling
Feeder issues
Transfer section
Quality / inspection
Setup / changeover
Operator / process delay
The category/cause structure makes Pareto review cleaner and supports more consistent reporting.
---
Event Notes
Event notes can be added directly to downtime events.
The tracker includes a compact note editor/modal so users can write or revise event notes without expanding the table too heavily.
Notes are included in:
Event log
CSV export
Excel export
JSON study file
Multi-study analysis input
---
Load / Recover
The tracker includes tools for recovering or reloading previous work.
Supported recovery options include:
Load Browser Copy
Autosave recovery list
Delete autosave recoveries
Drag-and-drop JSON loader
Load saved study JSON
Load exported CSV backup
The browser copy and autosave features are useful for local recovery, while the exported JSON file is the preferred portable backup format.
---
Save / Export
The Save / Export section provides official output options.
Supported outputs include:
Save to selected folder
Share JSON
Copy JSON
Export JSON
Export CSV
Export Excel
Print Full Page PDF
Save Browser Copy
The preferred long-term save format is the exported JSON study file because it preserves the complete tracker state and can be loaded later or used by the multi-study analyzer.
Exports warn the user if the study timer or a downtime event is still running.
---
JSON Study File
The exported JSON study file is the recommended official backup and transfer format.
It preserves:
Study information
Study timing segments
Collection mode
Manual study duration
Production speed
Operator / line notes
Downtime events
Downtime codes
Action items
Observation summary
File name override
Form fields
This file can be reopened in the Downtime Tracker or loaded into the MPG Multi-Study Downtime Totalizer.
---
CSV Export
The CSV export provides a spreadsheet-readable event record.
The CSV includes:
Study information
Study timing
Downtime summary
Production impact
Top loss summary
Study observation segments
Downtime code definitions
Included-cause Pareto
Event log
Action items
CSV export is useful for quick review, backup recovery, and manual analysis.
---
Excel Export
The Excel export creates an Excel-compatible report with formatted sections and dashboard-style tables.
The Excel export includes:
Study information
Downtime summary
Production impact
Top loss summary
Study timing segments
Downtime code definitions
Pareto details
Event log
Action items
Data quality checks
This export is intended for reporting, review, and offline analysis.
---
Data Quality Checks
The Data Quality Checks section flags common issues before final save or export.
Checks include items such as:
Study timer not started
Production speed missing
Events missing downtime codes
Used codes missing descriptions
Duplicate reason-code/tag abbreviations
Events missing notes
Downtime exceeding observed study time
Active downtime event still running
Study timer still running
Warnings do not block export, but they help the observer clean up the study before saving official records.
---
Observation Summary
The Observation Summary section provides a free-text field for the observer to summarize the study.
This section is useful for documenting:
Patterns noticed during the observation
Likely causes of repeated downtime
Operator or process observations
Conditions affecting the study
Context not obvious from raw event data
The observation summary is included in JSON, CSV, and Excel outputs.
---
Action Items / Follow-Up
The tracker includes an action item table to connect downtime observations to improvement work.
Each action item can include:
Action number
Action item
Owner
Priority
Status
Notes
This helps turn a downtime study into a practical follow-up record.
---
Summary Statistics
The Summary Statistics section provides an overview of captured downtime activity.
Summary metrics include:
Total events
Total downtime
Average duration
Minimum duration
Maximum duration
Sample standard deviation
Included downtime
Included events per hour
These metrics update as events are added, excluded, edited, or cleared.
---
Top Included Downtime Causes
The tracker summarizes the largest included downtime contributors.
This section helps identify the most important downtime causes without needing to read the entire event log or Pareto table.
Typical values include:
Top downtime code
Code description
Total duration
Percent of included downtime
---
Study-Scope Production Analysis
The production analysis section calculates downtime impact using observed time, included downtime, and production speed.
Metrics include:
Total logged study time
Excluded event time
Included study time
Included uptime
Study-scope utilization
Production speed
Projected included-scope units
Estimated included-scope production loss
Included events per hour
This section helps connect downtime activity to production impact.
---
Included-Cause Pareto
The Included-Cause Pareto ranks included downtime codes by total duration.
The Pareto includes:
Rank
Downtime code
Description
Event count
Total duration
Percent of included downtime
Cumulative percent
Visual bar indicator
This helps identify the largest downtime causes and supports prioritization for improvement work.
---
Executive Report
The Executive Report section creates a simplified stakeholder view.
It includes only:
Line / machine name
Date
Shift
Study-scope production analysis
Included-cause Pareto
Pareto graph
Pareto summary table
The Executive Report can be exported as a PDF through the browser print dialog.
For best appearance, enable Background graphics when saving the PDF.
---
Full Page PDF
The tracker includes a print-specific layout for full-page PDF export.
The print layout:
Uses landscape orientation
Preserves dark MPG dashboard colors
Expands scrollable tables
Disables sticky controls during print
Reduces table text size to prevent clipping
Attempts to avoid cutting off content
For best results, use landscape mode and enable Background graphics in the browser print dialog.
---
Recommended Workflow
Open the tracker in a modern browser.
Enter study information.
Select or confirm study location, date, shift, and line.
Enter production speed if production impact is needed.
Load or create the downtime code list.
Start the study timer.
Start and stop downtime events as they occur.
Assign downtime codes and notes to each event.
Stop the study timer when observation is complete.
Review Data Quality Checks.
Add observation summary and action items.
Review summary statistics, production analysis, and Pareto results.
Export JSON as the official study file.
Export CSV, Excel, or PDF reports as needed.
Load JSON files into the Multi-Study Downtime Totalizer for combined analysis.
---
Browser Compatibility
Recommended browsers:
Microsoft Edge
Google Chrome
Supported with possible limitations:
Safari
Firefox
Mobile Chrome
Mobile Safari
Some features, such as saving directly to a selected folder, work best in Chromium-based browsers like Edge and Chrome.
---
File and Data Handling
The app runs locally in the user’s browser.
Data can be stored or transferred using:
Browser local storage
Autosave recovery
Exported JSON study file
Exported CSV
Exported Excel-compatible file
Printed PDF
The app does not require a backend database unless it is modified to connect to one.
---
Deployment Options
Because the tracker is a static HTML file, it can be deployed through:
GitHub Pages
SharePoint
OneDrive shared link
Internal company web server
Local HTML file
Company intranet
For mobile use, an HTTPS-hosted version is recommended so users can open it in Safari, Chrome, or Edge and add it to their home screen.
---
Suggested Repository Structure
```text
downtime-tracker/
├── index.html
├── README.md
└── templates/
    └── downtime-code-template.csv
```
Use `index.html` as the main GitHub Pages entry file.
---
GitHub Pages Setup
Rename the tracker HTML file to `index.html`.
Add `index.html` and `README.md` to the repository.
Go to Settings > Pages.
Select Deploy from branch.
Choose the `main` branch and `/root` folder.
Save.
Use the generated HTTPS link to access the tracker.
---
Recommended Save Strategy
Use the following save strategy for reliable records:
Export JSON for the official study file.
Export CSV for spreadsheet review.
Export Excel for formatted reporting.
Export Executive Report PDF for stakeholder summary.
Keep JSON files organized by location, line, date, and shift.
Suggested file naming format:
```text
Location_Line_Date_Shift_DowntimeStudy.json
```
Example:
```text
Weck_Slitter24_2026-05-29_BlackDay_DowntimeStudy.json
```
---
Known Limitations
This is a standalone browser app, not a database-backed system.
Browser local storage can be cleared by the user or browser settings.
Autosave recovery is local to the device and browser.
Direct folder save may not work in all browsers.
PDF output depends on browser print settings.
Excel export is browser-generated and may vary by Excel version.
Mobile browsers may restrict some file save/load behavior.
Accurate production impact depends on correct production speed and observed study time.
---
Maintenance Notes
When updating the tracker:
Keep a backup of the previous HTML file.
Replace `index.html` with the new version.
Update this README when major features change.
Test timer capture and manual entry mode.
Test JSON export and reload.
Test CSV, Excel, full-page PDF, and executive PDF exports.
Test at least one code import and code export.
Confirm data quality checks still trigger correctly.
---
Related Tool
This tracker is designed to pair with the MPG Multi-Study Downtime Totalizer.
Recommended workflow:
Use the Downtime Tracker to capture individual studies.
Export each completed study as JSON.
Load multiple JSON files into the Multi-Study Downtime Totalizer.
Review combined Pareto and production impact across studies.
---
Project Status
Current status: standalone browser-based downtime study tracker.
The tool supports individual downtime study capture, data validation, production analysis, official JSON saving, spreadsheet export, and executive reporting.
