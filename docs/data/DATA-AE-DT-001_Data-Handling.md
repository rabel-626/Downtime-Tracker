---
documentId: DATA-AE-DT-001
title: Downtime Tracker Data Handling
tool: Downtime-Tracker
documentVersion: 1.1
studyFileType: MPG_Downtime_Tracker_Study
studyFileVersion: 2
status: Review Draft
updated: 2026-09-08
---

# Downtime Tracker — Data Handling

## 1. Purpose

This document describes how the Abel Engineering Downtime Tracker stores, saves, transfers, reloads, verifies, and exports study data.

It distinguishes the **authoritative resumable study record** from browser recovery copies and report outputs.

---

# 2. Data-Handling Principles

The current workflow follows these priorities:

```text
1. Preserve a full-fidelity Study JSON
2. Prefer Study Repository storage when available
3. Use Mobile Save & Load for floor-device transfer
4. Treat browser autosave as crash recovery only
5. Treat CSV / Excel / PDF as report outputs
```

The tracker performs normal study collection and analysis in the browser. It does not require an Abel Engineering backend service for the core application.

---

# 3. Authoritative Study Record

The full-fidelity study payload uses:

```text
fileType: MPG_Downtime_Tracker_Study
version: 2
```

The legacy `MPG_Downtime_Tracker_Study` identifier is intentionally preserved for compatibility with older study files and downstream analysis tools.

Do not manually rename the `fileType` value inside a saved study.

---

# 4. Study JSON Contents

The version 2 payload can contain the following major structures:

| Structure | Purpose |
| --- | --- |
| `savedAt` | Save timestamp |
| `repository` | Repository record identity and route |
| `events` | Downtime event log |
| `downtimeCodes` | Reason-code definitions |
| `templateReference` | Controlled template identity when applicable |
| `actionItems` | Follow-up records |
| `studyInfo` | Location, date, shift, line, product, observer, objective, speed, etc. |
| `observationSummary` | Free-text study summary |
| `activeEventIndex` | Active event state in a live/local payload |
| `studySegments` | Completed timer observation segments |
| `currentStudyStartTimestamp` | Active segment start when running |
| `studyIsRunning` | Current timer state |
| `collectionMode` | Timer or manual |
| `eventLogViewMode` | Category/current or legacy view basis |
| `manualStudyDuration` | Entered manual total duration |
| `productionSpeed` | Production reference rate |
| `operatorName` | Supplemental operator/line identifier |
| `studyFileNameOverride` | Optional filename override |
| `generatedFileName` | Current generated filename |
| `suggestedFolder` | Suggested organization path |
| `formFields` | Captured form values used by the application |

Repository/mobile frozen saves may add metadata describing the snapshot and transfer state.

---

# 5. Study Repository Architecture

The Study Repository is a shared folder architecture used by Abel Engineering study tools.

Approved root names are:

```text
Engineering Study Hub - Study Repository
```

or:

```text
Study Repository
```

The repository is validated using:

```text
_SYSTEM/LIBRARY_SCHEMA.json
```

with:

```text
fileType: AbelEngineering.StudyRepository
schemaVersion: 1
```

The standard top-level routes are:

```text
01_CYCLE_TIME
02_DOWNTIME
03_TAKT_MATERIAL_FLOW
04_MULTI_STUDY_DOWNTIME
90_TEMPLATES
```

---

# 6. Downtime Repository Route

A Downtime study is routed to:

```text
02_DOWNTIME
└── LOCATION
    └── LINE
        └── YYYY
            └── YYYY-MM
                └── DT-YYYYMMDD-XXXX
```

The final record identifier has the form:

```text
DT-YYYYMMDD-XXXX
```

where the final four characters are generated as a short random identifier.

---

# 7. Repository Study Package

A normal repository save creates:

```text
DT-YYYYMMDD-XXXX__STUDY.json
DT-YYYYMMDD-XXXX__MANIFEST.json
```

## 7.1 Study JSON

The `__STUDY.json` file is the authoritative resumable record.

## 7.2 Manifest

The `__MANIFEST.json` file uses:

```text
fileType: AbelEngineering.StudyPackageManifest
schemaVersion: 1
recordType: DOWNTIME
```

It records information such as:

- record ID;
- study date;
- location;
- line;
- shift;
- product;
- save timestamp;
- relative repository path; and
- output-file verification metadata.

For the authoritative Study JSON, the output entry records:

- file name;
- byte size;
- SHA-256 hash when available; and
- role `authoritative_study`.

---

# 8. Repository Write Verification

The tracker performs local verification before reporting a repository save as complete.

The current write sequence checks:

1. the selected repository is writable;
2. the written file size matches the generated payload size;
3. JSON can be parsed after writing;
4. stored JSON text matches the generated JSON text; and
5. a SHA-256 digest is produced for manifest metadata when browser cryptography is available.

> **Important:** This verifies the local/synchronized folder write. It does not guarantee that OneDrive or SharePoint has already finished cloud synchronization.

---

# 9. Repository Connection Persistence

The tracker can remember the selected repository directory handle using browser IndexedDB.

Current internal database identity:

```text
AbelEngineeringStudyRepositoryHandles
```

The remembered handle reduces repeated folder selection, but the browser may still require the user to re-grant permission.

If permission is unavailable, the application prompts for reconnection rather than assuming access.

---

# 10. Direct Repository Browser Support

Repository access requires the File System Access API.

The supported primary workflow is desktop:

- Microsoft Edge
- Google Chrome

On unsupported browsers/devices, use Mobile Save & Load.

---

# 11. Frozen Repository Snapshot

Saving while the study timer or downtime timer is active does not have to stop the live observation on the current device.

Instead, the tracker can create a frozen repository copy through the snapshot timestamp.

For an active downtime event, the saved copy:

- records the snapshot time as stop time;
- calculates duration through the snapshot;
- clears the active-event pointer.

For an active study segment, the saved copy:

- appends a segment stop at the snapshot timestamp;
- clears the current active study start; and
- sets the saved study to paused.

The live working browser state continues running.

Repository snapshot metadata records whether active downtime and/or active study timing was frozen.

---

# 12. Mobile Save & Load

Mobile Save & Load is the portable workflow for floor phones and other devices without repository access.

## 12.1 Mobile Save

Selecting **SAVE STUDY JSON TO DEVICE** downloads a full-fidelity JSON file.

If timing is active, the downloaded file can be frozen through the current time using the same paused-snapshot principle as repository save.

The mobile file includes transfer metadata such as:

```text
exportedAt
frozenSnapshot
activeDowntimeFrozen
activeStudyFrozen
```

and export metadata identifies it as a mobile-transfer file.

## 12.2 Required transfer

After download, move/upload the file to approved persistent storage before leaving the device.

Examples supported by the application guidance include:

- OneDrive
- SharePoint
- another approved storage location
- Teams file transfer

## 12.3 Mobile Load

Use **LOAD STUDY JSON FROM DEVICE** and select the transferred full Study JSON.

The tracker validates that the payload has the expected study file type before applying it.

---

# 13. Legacy CSV Recovery

Mobile Save & Load also exposes **Legacy CSV recovery**.

This is intended only for historical tracker CSV exports when the original Study JSON is unavailable.

CSV recovery can be incomplete because a flattened report does not necessarily contain all state required to reconstruct the original interactive study.

Use JSON whenever available.

---

# 14. JSON Load Protection

When a Study JSON is loaded, the tracker verifies:

- selected file type/extension is compatible with JSON; and
- parsed payload has the expected study type.

If the current tracker already contains events, codes, or actions, the user is prompted before replacement.

If the load is canceled or fails, the current study is left unchanged.

---

# 15. Browser Copy

The tracker can retain a full browser copy in local storage.

Current local-storage keys include:

```text
downtimeTrackerDataMPG
downtimeTrackerDataMPGBackup
```

Browser copy is useful for local continuation but is not as portable or reliable as Study JSON.

Potential failure causes include:

- private browsing;
- storage restrictions;
- quota limits;
- browser cleanup;
- browser/profile changes; and
- device changes.

---

# 16. Autosave Recovery

Autosave is separate from the normal browser copy and repository save.

Current recovery architecture uses:

```text
downtimeTrackerAutosaveIndexMPG_v2
downtimeTrackerAutosaveSessionMPG_v2_<session-id>
downtimeTrackerAutosaveActiveSessionMPG_v2
```

Legacy recovery keys are also recognized for backward compatibility.

### Current autosave behavior

- Input/change activity schedules an autosave after approximately **0.6 seconds** of inactivity.
- A periodic autosave runs approximately every **5 seconds**.
- The application attempts recovery saves on page hide and visibility loss.
- Each browser tab/session receives a recovery ID.
- Indexed recovery records can be viewed, loaded, exported, or deleted.

Autosave is **crash protection only**.

---

# 17. Controlled Downtime Templates

Repository reason-code templates use:

```text
fileType: AbelEngineering.DowntimeTagTemplate
schemaVersion: 1
```

Controlled templates are stored under:

```text
90_TEMPLATES/DOWNTIME/
```

with filenames such as:

```text
DOWNTIME_TEMPLATE__TEMPLATE_ID.json
```

A template can contain:

- Template ID
- Label
- Description
- Revision
- Line-match keywords
- Reason-code definitions
- Updated timestamp
- Creating application identity

Reason-code rows preserve:

```text
Code Number
Category
Abbreviation
Description
Include in Summary
```

---

# 18. Template Auto-Suggestion

When repository templates are available, the tracker can compare the Study Line text with each template's configured `lineContains` keywords.

The highest matching template can be suggested to the user.

A suggestion is not automatically applied; the user must select/load the intended template.

This separation reduces accidental code-basis changes.

---

# 19. Report and Data Exports

**ADDITIONAL OPTIONS** provides:

| Output | Purpose | Full reload? |
| --- | --- | --- |
| Event Log CSV | Spreadsheet/data exchange | No; legacy recovery may be partial |
| Excel Workbook | Formatted report and offline analysis | No |
| Full Study PDF | Printable study record | No |
| Executive Report PDF | Condensed stakeholder report | No |

These outputs should not replace the authoritative Study JSON.

---

# 20. Study JSON vs Report Outputs

Use this hierarchy:

```text
Authoritative / resumable
    Study JSON
        ↓
Portable but secondary / partial recovery
    CSV
        ↓
Presentation / analysis only
    Excel
    Full Study PDF
    Executive Report PDF
```

The Study JSON preserves interactive state that flattened report formats may not contain.

---

# 21. Reset Behavior

**RESET CURRENT WORKING STUDY** clears the current browser working state and starts a new recovery identity.

It does not delete:

- previously written repository records;
- transferred/downloaded Study JSON files; or
- previously exported CSV, Excel, or PDF files.

Reset clears the current record ID so the next repository study begins as a new record.

---

# 22. Privacy and Network Behavior

Normal calculation and event capture occur in the browser.

The application does not require a dedicated Abel Engineering web-service upload for the core study workflow.

Data leaves the active browser through deliberate user actions such as:

- repository save;
- file download;
- file load;
- clipboard copy;
- device share sheet;
- print/PDF; or
- transfer through an external approved service.

When the selected Study Repository is located in OneDrive/SharePoint synchronization, cloud transfer is handled by the user's synchronized storage environment, not by tracker calculation code.

---

# 23. Sensitive Information Guidance

Do not enter unnecessary sensitive information into:

- Observer fields
- Operator notes
- Event notes
- Observation Summary
- Action Item Owner/Notes

Prefer work identifiers or approved aliases where they satisfy the engineering need.

Study records may contain operationally sensitive information even when they contain no personal data.

Follow company policy for storage, access, retention, and sharing.

---

# 24. Recommended Retention Practice

For a completed study:

1. Preserve the authoritative Study JSON or verified repository package.
2. Preserve the reason-code template revision or template reference used for the study.
3. Preserve report outputs only when they serve a defined review/approval purpose.
4. Avoid renaming internal repository package files independently of their manifest.
5. Do not edit JSON by hand unless performing a controlled recovery/migration.
6. Keep historical studies immutable when they have already been used for formal reporting; create a revised study record if policy requires traceability.

---

# 25. Recommended Backup Rule

A useful minimum rule is:

```text
Working copy ≠ backup
Browser autosave ≠ archive
Downloaded mobile file ≠ safe until transferred
Repository save ≠ cloud-synced until synchronization completes
```

For important studies, maintain at least one approved persistent copy outside the original collection device.

---

# 26. Compatibility Requirement

Changes to the Study JSON schema, legacy file-type identifier, duration parsing, event coding, or inclusion semantics should be coordinated with downstream tools, particularly the Multi-Study Downtime Totalizer.

The `MPG_Downtime_Tracker_Study` identifier should remain unchanged unless a controlled migration is implemented across the toolchain.

---

# 27. Related Documents

- `QS-AE-DT-001` — Downtime Tracker Quick Start
- `WI-AE-DT-001` — Downtime Tracker Work Instructions
- `REF-AE-DT-001` — Downtime Capture & Analysis Methodology
- `LIM-AE-DT-001` — Downtime Tracker Limitations
