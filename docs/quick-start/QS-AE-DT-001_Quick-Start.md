---
documentId: QS-AE-DT-001
title: Downtime Tracker Quick Start
tool: Downtime-Tracker
documentVersion: 1.1
studyFileType: MPG_Downtime_Tracker_Study
studyFileVersion: 2
status: Review Draft
updated: 2026-09-08
---

# Downtime Tracker — Quick Start

> **Purpose:** Capture a manufacturing downtime study, classify each loss event consistently, validate the study, and preserve a resumable study record for later analysis.

The Abel Engineering Downtime Tracker supports two collection methods:

- **Timer Capture** — live observation using study and downtime timers.
- **Manual Entry** — transcription of a study collected elsewhere.

The tracker separates **observed study time**, **included analytical scope**, **included downtime**, and **excluded event time** so that reason-code inclusion choices affect downstream utilization, production-impact, and Pareto calculations consistently.

---

## 1. Before You Start

For a normal study, have the following available:

| Item | Why it matters |
| --- | --- |
| Location | Study traceability and repository routing |
| Study date | Study traceability and repository routing |
| Shift | Comparison and reporting context |
| Line / machine | Identifies the equipment or process being observed |
| Product / job | Provides production context |
| Observer | Identifies the study owner or collector |
| Production speed | Required for estimated production-impact metrics |
| Downtime reason-code template | Standardizes category/cause classification |
| Study objective | Defines what question the observation is intended to answer |

> **Recommended browser:** Use current Microsoft Edge or Google Chrome. Direct Study Repository access requires the File System Access API and is intended for desktop Edge/Chrome. Floor phones and other unsupported devices should use **Mobile Save & Load**.

---

## 2. Choose the Storage Workflow

### Connected desktop workflow

Use the **Study Repository** when the device can access the approved repository folder.

1. Open **Repository**.
2. Select **REPOSITORY CONNECTION / RECONNECT** if needed.
3. Choose the approved repository root:

```text
Engineering Study Hub - Study Repository
```

or:

```text
Study Repository
```

4. Use **SAVE STUDY** for the authoritative repository record.
5. Use **OPEN STUDY** to resume a repository study.

Downtime studies are routed to:

```text
Study Repository
└── 02_DOWNTIME
    └── LOCATION
        └── LINE
            └── YYYY
                └── YYYY-MM
                    └── DT-YYYYMMDD-XXXX
```

### Floor phone / non-repository workflow

Use **MOBILE SAVE & LOAD**.

1. Select **SAVE STUDY JSON TO DEVICE**.
2. Move the downloaded `_DOWNTIME_STUDY.json` file to OneDrive, SharePoint, another approved storage location, or transfer it through Teams.
3. On the next device, select **LOAD STUDY JSON FROM DEVICE**.

> **Warning:** Browser autosave is crash recovery, not a portable study record. Do not leave a floor device until the downloaded Study JSON has been transferred to approved storage.

---

## 3. Complete Study Information

In **Information**, enter the identifying fields before collection whenever practical:

- Location
- Study Date
- Shift
- Line / Machine
- Product / Job
- Observer
- Operator / line note, if applicable
- Production Speed in units/hour
- Study Objective

Production speed does **not** affect recorded downtime duration. It is used to estimate production impact.

---

## 4. Load or Confirm the Reason-Code Standard

In **Study → Downtime Code Setup**:

1. Select the appropriate repository template when available.
2. Review the category, cause-code abbreviation, description, and **Include** status.
3. Select **LOAD TEMPLATE** to apply it.

A controlled downtime code normally contains:

| Field | Meaning |
| --- | --- |
| Code # | Reference sequence or identifier |
| Category | Broad loss family |
| Cause code | Short reason-code abbreviation |
| Description | Human-readable definition |
| Include in Summary | Whether the event belongs in included downtime analysis |

The combination of **Category + Cause Code** should be unique.

> **Important:** A code marked **excluded** remains in the event log, but its time is removed from the included analytical study scope and is omitted from included downtime, Pareto, and production-loss calculations.

---

# 5. Timer Capture — Live Study

Use this mode when observing the process in real time.

## 5.1 Start the observation

1. Select **Timer Capture**.
2. Press **Start Study** when observation begins.
3. Confirm the study status indicates that the study timer is running.

The study may be stopped and restarted. Each completed observation period is stored as a separate segment and added to total logged study time.

## 5.2 Capture a downtime event

When downtime begins:

1. Press **Start Downtime**.
2. Observe the condition.
3. Press **Stop Downtime** when the event ends.
4. Open **Details** for the event.
5. Assign:
   - Category
   - Cause code
   - Event note
6. Verify the recorded duration is reasonable.

Only one timer-based downtime event can be active at a time.

> **Important:** The study timer must be running before a downtime event can start.

## 5.3 Pause or end the study

Press **Stop Study** when the observation period ends or must be paused.

If a downtime event is still active, the tracker automatically closes that event at the study stop time and records an explanatory note.

---

# 6. Manual Entry — Existing Study

Use Manual Entry when downtime was collected outside the tracker.

1. Select **Manual Entry**.
2. Enter **Actual Study Duration**.
3. Add one manual row per downtime event.
4. Enter the duration, category, cause code, and notes.

### Manual total study-duration formats

| Entry | Interpretation |
| --- | --- |
| `120` | 120 minutes |
| `120:00` | 120 minutes, 0 seconds |
| `2:00:00` | 2 hours, 0 minutes, 0 seconds |

### Manual event-duration formats

Event durations use `[MM]:SS` or numeric seconds.

| Entry | Interpretation |
| --- | --- |
| `75` | 75 seconds |
| `1:15` | 1 minute, 15 seconds |
| `125:30` | 125 minutes, 30 seconds |

> **Important:** A plain number means **minutes** in the total study-duration field but **seconds** in an individual event-duration field.

---

# 7. Review Data Quality Before Saving

Open **Repository → Data Quality Checks** and resolve or document warnings.

The current tracker checks for:

- Missing included study time
- Missing production speed
- Completed events without downtime codes
- Used codes without descriptions
- Duplicate Category + Cause Code combinations
- Completed events without notes
- Included downtime greater than included study time
- An active downtime event
- A running study timer

Warnings do not automatically block save/export. They are review prompts.

### Recommended completion check

Before treating a study as complete:

- [ ] Study identity is complete.
- [ ] Correct reason-code standard was used.
- [ ] All completed events have a category/cause code.
- [ ] All completed events have useful notes.
- [ ] No unintended duplicate codes exist.
- [ ] Included/excluded settings were reviewed.
- [ ] No timer is unintentionally still running.
- [ ] Included downtime does not exceed included study time.
- [ ] Observation Summary is complete.
- [ ] Action Items are entered when follow-up is required.

---

# 8. Read the Core Results

## Included Study Time

```text
Included Study Time
= max(0, Total Logged Study Time − Excluded Event Time)
```

Excluded events are treated as time outside the included analytical scope.

## Included Uptime

```text
Included Uptime
= max(0, Included Study Time − Included Downtime)
```

## Study-Scope Utilization

```text
Utilization
= Included Uptime / Included Study Time
```

This is a **study-scope uptime ratio**, not OEE.

## Included Events per Hour

```text
Included Events / Hour
= Included Event Count / Total Observed Study Hours
```

Event frequency uses the full observed study duration as its denominator.

## Estimated Production Loss

```text
Estimated Units Lost
= Production Speed × Included Downtime Hours
```

This assumes the entered production speed is an appropriate constant reference rate for the study.

---

# 9. Review the Pareto

The **Included-Cause Pareto** groups completed included events by category/cause combination and sorts them by total downtime.

For each cause, review:

- Event count
- Total downtime
- Percent of included downtime
- Cumulative percent

Use the Pareto to identify the largest contributors by **duration**, then use event count and event notes to distinguish chronic short stops from fewer long stops.

---

# 10. Save the Study

## Repository-connected device

Select **SAVE STUDY**.

The tracker writes and verifies:

```text
DT-YYYYMMDD-XXXX__STUDY.json
DT-YYYYMMDD-XXXX__MANIFEST.json
```

The Study JSON is the authoritative resumable study record. The manifest records the package identity and verification metadata.

If a timer is active, the tracker can save a **paused snapshot through the current time** while allowing the live timers to continue on the current device. The saved copy reopens paused so transfer or offline time is not added accidentally.

## Mobile device

Select:

```text
MOBILE SAVE & LOAD
→ SAVE STUDY JSON TO DEVICE
```

Then transfer the file off the device before leaving it.

---

# 11. Additional Outputs

Under **ADDITIONAL OPTIONS**:

| Output | Primary use |
| --- | --- |
| Event Log CSV | Spreadsheet review / data exchange |
| Excel Workbook | Formatted study review |
| Full Study PDF | Complete printable study record |
| Executive Report PDF | Condensed stakeholder view |

These are **report outputs**, not substitutes for the resumable Study JSON.

---

# 12. Final Study Workflow

```text
Identify study
      ↓
Connect repository / choose mobile transfer workflow
      ↓
Load controlled reason-code template
      ↓
Choose Timer Capture or Manual Entry
      ↓
Capture / enter downtime events
      ↓
Code events + add notes
      ↓
Review Data Quality Checks
      ↓
Review statistics + production impact + Pareto
      ↓
Add observation summary / action items
      ↓
SAVE STUDY or Mobile Save JSON
      ↓
Create report exports as needed
```

> **Related reference:** See `REF-AE-DT-001 — Downtime Capture & Analysis Methodology` for calculation definitions and interpretation guidance.
