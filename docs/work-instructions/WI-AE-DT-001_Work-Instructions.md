---
documentId: WI-AE-DT-001
title: Downtime Tracker Work Instructions
tool: Downtime-Tracker
documentVersion: 1.1
studyFileType: MPG_Downtime_Tracker_Study
studyFileVersion: 2
status: Review Draft
updated: 2026-09-08
---

# Downtime Tracker — Work Instructions

## 1. Purpose

This work instruction defines the standard procedure for preparing, conducting, reviewing, saving, and reopening an Abel Engineering Downtime Tracker study.

The objective is to produce a traceable event log that can support:

- downtime Pareto analysis;
- study-scope utilization review;
- event-frequency analysis;
- estimated production-loss analysis;
- observation summaries and action tracking; and
- downstream multi-study analysis.

---

## 2. Scope

This procedure applies to manufacturing downtime observations performed with the browser-based Abel Engineering Downtime Tracker using either:

1. **Timer Capture** for live floor observation, or
2. **Manual Entry** for transcribing an existing downtime study.

This work instruction does not define machine operating procedures, safety procedures, maintenance procedures, or official OEE accounting rules.

---

## 3. Roles and Responsibilities

| Role | Responsibility |
| --- | --- |
| Observer / Study Owner | Configure the study, capture events, classify loss, document notes, and perform first-pass validation. |
| Reviewer / Engineer | Confirm scope, reason-code logic, duration reasonableness, exclusions, Pareto interpretation, and production-impact assumptions. |
| Process Owner | Confirm operational meaning of major causes and ownership of follow-up actions. |
| Repository / Standards Owner | Maintain controlled downtime templates and repository availability where applicable. |

One person may perform more than one role.

---

## 4. Definitions

| Term | Definition |
| --- | --- |
| Total Logged Study Time | Full observed duration: timer segments in Timer Capture or entered manual total in Manual Entry. |
| Included Event | Completed event whose reason-code definition is included in summary analysis. |
| Excluded Event | Completed event whose reason-code definition has Include in Summary turned off. |
| Excluded Event Time | Sum of completed excluded-event durations. |
| Included Study Time | Total logged study time minus excluded event time, floored at zero. |
| Included Downtime | Sum of completed included-event durations. |
| Included Uptime | Included study time minus included downtime, floored at zero. |
| Study-Scope Utilization | Included uptime divided by included study time. |
| Included Events / Hour | Count of included completed events divided by total observed study hours. |
| Reason Code | Category + cause-code definition used to classify a downtime event. |
| Study JSON | Full-fidelity resumable study record. |
| Repository Manifest | Package metadata generated with a repository save. |
| Frozen Snapshot | A saved copy closed through a defined timestamp so active timing does not continue while the file is offline. |

---

# 5. Prerequisites

Before beginning a study:

- Use a current supported browser.
- Confirm the device will remain powered and available throughout collection.
- Know whether the device can access the Study Repository.
- Determine the line/machine and study objective.
- Obtain the correct controlled downtime template when available.
- Confirm the production-speed basis if production-impact estimates are required.
- Follow all site safety requirements before beginning line-side observation.

> **Safety:** The tracker is an observation and analysis tool. Do not operate controls, bypass guarding, enter restricted areas, or divert attention from hazards in order to capture an event more precisely.

---

# 6. Select the Study Storage Method

## 6.1 Study Repository — preferred on connected desktop devices

Open **Repository** and connect to the approved root folder if necessary.

Approved repository root names are:

```text
Engineering Study Hub - Study Repository
```

or:

```text
Study Repository
```

The repository contains the standardized top-level routes:

```text
01_CYCLE_TIME
02_DOWNTIME
03_TAKT_MATERIAL_FLOW
04_MULTI_STUDY_DOWNTIME
90_TEMPLATES
```

Downtime studies are written under `02_DOWNTIME`.

## 6.2 Mobile Save & Load — floor phones / unsupported devices

Use **MOBILE SAVE & LOAD** if the device cannot access the repository folder directly.

The workflow is:

```text
Floor device
   ↓
Save Study JSON to device
   ↓
Transfer JSON to approved storage
   ↓
Load Study JSON on next device
```

Browser autosave must not be used as the only retained copy.

---

# 7. Enter Study Information

Complete the study-identification fields in **Information**.

At minimum, enter:

1. Location
2. Study Date
3. Shift
4. Line / Machine
5. Product / Job
6. Observer
7. Production Speed, when production impact is required
8. Study Objective

Add an operator/line note when it materially improves interpretation.

### Production speed entry

Enter a positive rate in **units per hour**.

The tracker uses this reference only for modeled production-impact calculations. It does not change recorded event durations.

---

# 8. Establish the Downtime Code Basis

## 8.1 Preferred: controlled repository template

1. Connect the Study Repository.
2. Open **Downtime Code Setup**.
3. Select the appropriate template.
4. Review the suggested template if the tracker matched one to the Line field.
5. Select **LOAD TEMPLATE**.
6. Confirm that the correct template/reference is shown as the current code basis.

Repository templates are read from `90_TEMPLATES` and use the controlled template type:

```text
AbelEngineering.DowntimeTagTemplate
```

## 8.2 Alternate code setup

When a controlled repository template is unavailable, the tracker can:

- add codes manually;
- import a reason-code CSV;
- paste a CSV or Excel table; or
- export the current code list for review.

Treat ad-hoc code lists as uncontrolled until they are reviewed and approved under the applicable local process.

## 8.3 Code-definition requirements

Each active code should have:

- a unique Category + Cause Code combination;
- a meaningful description;
- a deliberate Include/Exclude setting.

Avoid using the same abbreviation for different causes within the same category.

---

# 9. Define Included vs Excluded Scope

The **Include in Summary** flag changes analytical scope.

### Included code

The event contributes to:

- Included Downtime
- Included-event summary statistics
- Study-Scope Utilization loss
- Estimated Production Loss
- Pareto

### Excluded code

The event remains in the event log, but its duration is counted as **Excluded Event Time** and is removed from Included Study Time.

```text
Included Study Time
= max(0, Total Logged Study Time − Excluded Event Time)
```

> **Engineering interpretation:** Exclusion means “outside the included analysis scope,” not “machine uptime.” Use exclusions deliberately and consistently across studies.

---

# 10. Timer Capture Procedure

## 10.1 Start the study

1. In **Study**, select **Timer Capture**.
2. Confirm the study identity and code basis.
3. Press **Start Study** at the beginning of the observation period.
4. Confirm the status shows the study as running.

The tracker can accumulate multiple study segments in one study.

## 10.2 Record each downtime event

When a downtime condition begins:

1. Press **Start Downtime** as close to the event onset as safely practical.
2. Observe the cause without interfering with the process.
3. Press **Stop Downtime** when the event ends.
4. Open **Details**.
5. Select the category and cause code.
6. Enter a concise event note.
7. Verify the duration is reasonable.

Recommended note pattern:

```text
Observed condition → immediate cause → recovery / consequence
```

Example:

```text
Carton failed to transfer at discharge; skewed carton blocked sensor;
operator cleared carton and restarted line.
```

Avoid notes such as `machine stopped` when a more specific observation is known.

## 10.3 One active timer event

The timer workflow supports one active downtime event at a time. If several conditions coexist, classify the event using the study's agreed primary-cause rule and document contributing conditions in the event note.

Do not invent overlapping event durations in order to assign the same time to multiple causes.

## 10.4 Pause the study

Press **Stop Study** when observation is intentionally paused.

If downtime is still active, the tracker:

1. stops the downtime event at the same timestamp;
2. adds an automatic explanatory note; and
3. closes the current study segment.

## 10.5 Resume the study

Press **Start Study Again** when observation resumes.

A new study segment is created. Total Logged Study Time is the sum of all completed segments plus any current live segment.

---

# 11. Manual Entry Procedure

Use Manual Entry only when the observation timing already exists outside the tracker.

## 11.1 Set Manual Entry mode

1. Select **Manual Entry**.
2. Enter **Actual Study Duration**.

Accepted total-duration formats:

```text
120       → 120 minutes
120:00    → 120 minutes, 0 seconds
2:00:00   → 2 hours, 0 minutes, 0 seconds
```

## 11.2 Enter events

For each observed downtime occurrence:

1. Select **Add Manual Row**.
2. Enter the event duration.
3. Select Category.
4. Select Cause Code.
5. Enter the event note.

Individual event duration accepts `[MM]:SS` or numeric seconds.

```text
75     → 75 seconds
1:15   → 75 seconds
```

> **Warning:** The total study field and individual event-duration fields interpret plain numbers differently. Verify manual entries before final save.

## 11.3 Manual-study integrity

Manual entries do not contain the same start/stop timestamp evidence as live timer capture. Confirm:

- the manual total represents the same scope as the entered events;
- events were not duplicated;
- excluded time was not double-counted; and
- event durations do not exceed the available included study time.

---

# 12. Event Review and Correction

Use the event **Details** control to review and edit event classification.

For each completed event, verify:

- Category
- Cause code
- Code definition
- Duration
- Notes
- Inclusion status inherited from the code definition

Events may be removed when they are confirmed capture/transcription errors. Document the reason externally when required by the local review process because deleting an event removes it from the active tracker record.

> **Important:** Changing a reason-code Include setting can change the analytical treatment of every event using that code.

---

# 13. Data Quality Review

Open **Data Quality Checks** before finalizing the study.

## 13.1 Required checks

| Check | Expected condition |
| --- | --- |
| Included study time | Greater than zero |
| Production speed | Positive when production impact is being used |
| Event coding | All completed events coded |
| Code descriptions | All used codes defined |
| Duplicate codes | No duplicate Category + Cause Code combinations |
| Event notes | Completed events documented |
| Duration integrity | Included downtime does not exceed included study time |
| Active downtime | None at finalization |
| Study timer | Stopped at finalization |

## 13.2 Uncoded events

A completed uncoded event is still treated as included by the current analysis logic and appears as `UNCODED` in Pareto analysis until corrected.

Therefore, an uncoded-event warning should normally be resolved before the study is approved.

---

# 14. Complete the Observation Summary

Use **Observation Summary** to record information that is important to interpretation but not efficiently represented by individual event rows.

Recommended content:

- overall operating condition;
- repeating patterns;
- unusual conditions;
- limitations in observation coverage;
- known causes not fully resolved during the study;
- production context that affects comparison with other studies.

Avoid using the summary to replace event-level coding.

---

# 15. Create Action Items / Follow-Up

Use **Action Items / Follow-Up** when a study identifies work requiring ownership.

Available fields include:

- Action number
- Action item
- Owner
- Priority
- Status
- Notes

Action items are tracking notes. They do not alter downtime calculations.

---

# 16. Review Results

## 16.1 Summary Statistics

The current summary statistics use **completed included events** only.

Review:

- event count;
- total included downtime;
- mean duration;
- minimum duration;
- maximum duration; and
- sample standard deviation when at least two included events exist.

## 16.2 Study-Scope Production Analysis

Review:

- Total Logged Study Time
- Excluded Event Time
- Included Study Time
- Included Downtime
- Included Uptime
- Study-Scope Utilization
- Production Speed
- Projected Included-Scope Units
- Estimated Units Lost
- Included Events / Hour

Do not interpret these as actual counter data unless the input rate and study assumptions justify that interpretation.

## 16.3 Pareto

Review both:

- **total downtime by cause**, and
- **event count by cause**.

A frequent short-stop cause and an infrequent long-stop cause may require different corrective strategies even if their total downtime is similar.

---

# 17. Save a Repository Study

For connected desktop devices:

1. Stop or intentionally snapshot active timing.
2. Select **SAVE STUDY**.
3. If active timing exists, review the paused-snapshot warning.
4. Confirm the repository path.
5. Wait for the **Saved & verified** status.

The tracker creates a record ID:

```text
DT-YYYYMMDD-XXXX
```

and writes:

```text
DT-YYYYMMDD-XXXX__STUDY.json
DT-YYYYMMDD-XXXX__MANIFEST.json
```

The write process verifies:

- written file size;
- JSON parseability;
- generated/stored JSON equality; and
- SHA-256 metadata for the Study JSON in the manifest when browser cryptography is available.

### Saving while timing is active

If the study timer or downtime timer is active, the repository save can create a **frozen copy through the save timestamp**.

The live timer continues on the current device, while the saved JSON:

- closes the active event at the snapshot timestamp;
- closes the active study segment at the snapshot timestamp;
- clears the active-event pointer; and
- reopens in a paused state.

This prevents time spent transferring or reopening the file from becoming artificial study time.

---

# 18. Mobile Save & Load Procedure

Use this workflow on floor phones or other devices without repository access.

## 18.1 Save

1. Open **MOBILE SAVE & LOAD**.
2. Select **SAVE STUDY JSON TO DEVICE**.
3. If timing is active, accept or cancel the frozen-snapshot prompt.
4. Confirm the downloaded filename.
5. Immediately transfer the file to approved storage.

## 18.2 Transfer

Move/upload the file to one of the approved destinations available to the user, such as:

- OneDrive
- SharePoint
- an approved company storage location
- Teams file transfer

## 18.3 Reload

1. Open the tracker on the destination device.
2. Open **MOBILE SAVE & LOAD**.
3. Select **LOAD STUDY JSON FROM DEVICE**.
4. Select the transferred `_DOWNTIME_STUDY.json` file.
5. Confirm the loaded study identity before continuing.

Legacy CSV recovery exists only for historical recovery when the original Study JSON is unavailable.

---

# 19. Additional Report Outputs

Open **ADDITIONAL OPTIONS** for non-authoritative exports.

| Output | Use | Resumable study? |
| --- | --- | --- |
| Event Log CSV | Spreadsheet/data exchange | No — limited recovery only |
| Excel Workbook | Formatted offline review | No |
| Full Study PDF | Complete printable report | No |
| Executive Report PDF | Management summary | No |

Use the Study JSON for continuation and controlled archival.

---

# 20. Reopen a Study

## Repository study

1. Select **OPEN STUDY**.
2. Search by location, line, date, product, observer, or record ID.
3. Select the required record.
4. Confirm the loaded identity before editing.

If loading is canceled or fails, the current working study is left unchanged.

## Mobile / file study

Use **LOAD STUDY JSON FROM DEVICE** and select a valid `MPG_Downtime_Tracker_Study` JSON file.

---

# 21. Browser Recovery

Browser recovery is intended for interruption/crash protection.

Current behavior includes:

- debounced autosave after input/change activity;
- periodic autosave approximately every 5 seconds;
- save attempts when the page is hidden or closed;
- a separate recovery ID for each tab/session; and
- multiple indexed recovery records.

Use autosave only to recover work that was not yet preserved through the normal repository/mobile workflow.

---

# 22. Reset Current Working Study

Use **ADDITIONAL OPTIONS → RESET CURRENT WORKING STUDY** only after the required study record has been saved.

Reset clears the current working tracker state, including:

- events;
- reason codes;
- action items;
- study information;
- observation summary;
- active timers;
- study segments;
- manual duration; and
- the current repository record ID.

It does **not** delete an already saved repository package or previously exported report file.

> **Warning:** Reset creates a new working-session recovery identity. Save the study first.

---

# 23. Final Acceptance Checklist

A completed study should satisfy the following before being used as an engineering baseline:

- [ ] Study objective and scope are clear.
- [ ] Location, date, shift, line/machine, product/job, and observer are correct.
- [ ] Production-speed reference is documented if production impact is used.
- [ ] Correct reason-code basis is identified.
- [ ] Category + Cause Code definitions are unique.
- [ ] Included/excluded scope is deliberate.
- [ ] All completed events are reviewed and coded.
- [ ] Event notes are sufficient to understand the observed condition.
- [ ] No unintended active timers remain.
- [ ] Included downtime does not exceed included study time.
- [ ] Pareto and event frequency were reviewed together.
- [ ] Observation Summary documents material context.
- [ ] Action items have owners where appropriate.
- [ ] Authoritative Study JSON has been saved and verified or transferred off the floor device.
- [ ] Report outputs are treated as reports, not as the master study record.

---

# 24. Related Documents

- `QS-AE-DT-001` — Downtime Tracker Quick Start
- `REF-AE-DT-001` — Downtime Capture & Analysis Methodology
- `LIM-AE-DT-001` — Downtime Tracker Limitations
- `DATA-AE-DT-001` — Downtime Tracker Data Handling
