---
documentId: LIM-AE-DT-001
title: Downtime Tracker Limitations
tool: Downtime-Tracker
documentVersion: 1.1
studyFileType: MPG_Downtime_Tracker_Study
studyFileVersion: 2
status: Review Draft
updated: 2026-09-08
---

# Downtime Tracker — Limitations

## 1. Purpose

This document defines the principal technical, analytical, operational, and data-handling limitations of the Abel Engineering Downtime Tracker.

The purpose is to prevent study outputs from being interpreted with more precision or authority than the underlying observations support.

---

# 2. Intended Use

The tracker is intended to support:

- structured manufacturing downtime observation;
- event timing and classification;
- duration and frequency review;
- Pareto prioritization;
- simple study-scope utilization analysis;
- approximate production-impact estimation;
- documentation of observations and follow-up actions; and
- creation of standardized source files for downstream multi-study analysis.

It is an engineering study aid, not a plant historian, MES, machine control system, or validated transactional database.

---

# 3. Observation Accuracy

## 3.1 Human reaction time

Timer Capture depends on the observer pressing controls near the true event boundaries. Start/stop times therefore contain human reaction and recognition error.

Short events are affected proportionally more than long events.

## 3.2 Observer attention

The observer may miss:

- brief stops;
- simultaneous conditions;
- causal changes during a stop; or
- events occurring outside the observer's field of view.

The tracker cannot detect a missed event automatically.

## 3.3 Event-boundary definition

Different observers may disagree about when downtime “starts” or “ends,” particularly when a machine slows, faults intermittently, starves gradually, or restarts through a recovery sequence.

A study program should define event-boundary rules consistently before comparing studies.

---

# 4. One Active Timer Event

Timer Capture permits one active downtime event at a time.

This supports a mutually exclusive event timeline but means the tracker does not natively represent concurrent root causes as separate overlapping timer events.

When multiple conditions occur together, the observer must apply the agreed primary-cause rule and document contributing conditions in notes.

Manual rows can be entered independently, so users must avoid double-counting overlapping durations unless overlapping exposure is intentionally being analyzed.

---

# 5. Reason-Code Quality

The analytical result is only as consistent as the classification standard.

Problems include:

- ambiguous code definitions;
- duplicate category/code pairs;
- overly broad categories;
- overly specific codes that fragment the Pareto;
- inconsistent inclusion/exclusion rules;
- operator-specific wording; and
- changes in code meaning between studies.

Controlled repository templates reduce this risk but do not eliminate the need for training and review.

---

# 6. Uncoded and Undefined Codes

In the current implementation:

- an event without a cause code is treated as included for calculation purposes;
- an unknown/undefined code is also treated as included unless a matching definition explicitly excludes it; and
- uncoded events appear as `UNCODED` in Pareto analysis.

Data Quality Checks flag these conditions, but they do not automatically remove the events.

> **Consequence:** Saving a study with unresolved uncoded events can change included downtime and Pareto results.

---

# 7. Exclusion Semantics

A reason code with **Include in Summary** turned off is treated as time outside the included analytical scope.

```text
Included Study Time
= Total Logged Study Time − Excluded Event Time
```

Excluded event time does **not** become uptime.

This is appropriate only when the excluded code truly represents time that should be removed from the study denominator under the intended analysis method.

Inconsistent exclusions across studies can make utilization and production-impact comparisons invalid.

---

# 8. Overlap and Double-Counting Risk

The calculation model sums event durations.

If manual events overlap in real time but are entered as separate durations, summed downtime may exceed the actual elapsed loss period.

The Data Quality panel can flag included downtime greater than included study time, but it cannot determine which overlapping rows are conceptually correct.

---

# 9. Manual Entry Limitations

Manual Entry has less timing evidence than live capture.

Risks include:

- transcription error;
- duplicate events;
- missing events;
- inconsistent study scope;
- ambiguous handwritten time formats; and
- duration-unit confusion.

A particularly important current behavior is:

```text
Manual total plain number  → minutes
Manual event plain number  → seconds
```

Manual study files require deliberate review before comparison with live studies.

---

# 10. Production-Speed Assumption

Estimated production loss assumes the entered production speed is a suitable constant reference rate.

```text
Estimated Units Lost
= Production Speed × Included Downtime Hours
```

The model does not automatically account for:

- product-specific rate changes within one study;
- planned rate changes;
- acceleration/deceleration;
- startup or restart ramp;
- micro-speed loss while running;
- scrap;
- rework;
- yield;
- downstream propagation;
- buffer effects; or
- actual counter output.

Production-impact results should therefore be treated as time-equivalent estimates unless independently validated.

---

# 11. Timer vs Manual Projected Units

The current application uses different projected-unit time bases:

```text
Timer Capture:
Projected Units = Production Speed × Included Uptime Hours

Manual Entry:
Projected Units = Production Speed × Included Study Hours
```

Therefore, the displayed Projected Included-Scope Units metric is not directly mode-equivalent.

Do not pool this metric across Timer and Manual studies without normalizing the methodology externally or updating the application under controlled revision.

---

# 12. Study-Scope Utilization Is Not OEE

The tracker calculates:

```text
Study-Scope Utilization
= Included Uptime / Included Study Time
```

This should not be labeled or interpreted as OEE unless an external method has demonstrated equivalence for the specific use case.

Traditional OEE frameworks may include separate Availability, Performance, and Quality factors. This tracker does not automatically calculate those complete factors.

---

# 13. Pareto Limitations

The Pareto ranks causes by **total included downtime duration**.

A duration Pareto can underemphasize:

- highly frequent microstops;
- causes with high scrap impact;
- safety-critical failures;
- costly maintenance failures;
- low-frequency catastrophic events; or
- issues that propagate beyond the observed line.

Review duration, frequency, notes, operational consequence, and risk together.

---

# 14. Statistical Limitations

The tracker reports descriptive statistics for included event duration, including sample standard deviation.

These outputs do not establish:

- process normality;
- statistical control;
- confidence bounds;
- process capability;
- Weibull/reliability parameters;
- MTBF/MTTR compliance; or
- causal significance.

A standard deviation computed from a small or multimodal event population can be especially misleading.

---

# 15. Causality Limitation

The tracker records the reason assigned to an event. It does not prove root cause.

An observed fault message, blocked sensor, or material jam may be a symptom of an upstream mechanism.

Use appropriate root-cause methods when corrective action requires causal certainty.

---

# 16. Action Items Are Not a Controlled CAPA System

The Action Items / Follow-Up table is a convenience feature.

It does not provide, by itself:

- approval workflow;
- electronic signatures;
- due-date enforcement;
- audit-controlled status history;
- validation evidence; or
- formal corrective/preventive-action governance.

Use the organization's controlled action-tracking system when required.

---

# 17. Study Duration and Representativeness

A study captures only the observed period.

Results may not represent:

- other shifts;
- other crews;
- other products;
- startup/changeover periods;
- different raw-material lots;
- maintenance condition;
- seasonal variation;
- long-cycle intermittent failures; or
- rare events.

Do not extrapolate a short observation to annual performance without an appropriate sampling basis.

---

# 18. Repository Browser Limitations

Direct Study Repository access depends on browser support for the File System Access API.

The current implementation is intended for desktop Microsoft Edge or Google Chrome.

Other browsers and mobile devices may not expose compatible directory access even though the core tracker runs normally.

Use Mobile Save & Load when direct repository access is unavailable.

---

# 19. File Synchronization Is External

The tracker writes to the selected local/synchronized repository folder and verifies the local write.

It does not control or guarantee:

- OneDrive synchronization timing;
- SharePoint availability;
- Teams synchronization;
- network connectivity;
- conflict resolution; or
- cloud-retention policy.

A locally verified repository save can still be awaiting external synchronization.

---

# 20. Mobile Transfer Risk

Mobile Save & Load downloads a Study JSON to the device.

Until that file is moved to approved persistent storage, it can be lost through:

- sign-out;
- browser cleanup;
- device reset;
- managed-device profile changes;
- accidental deletion; or
- download-folder cleanup.

The application explicitly warns users to transfer the file before leaving the device.

---

# 21. Browser Autosave Limitations

Autosave is device/browser-local recovery.

It may become unavailable after:

- clearing site/browser data;
- private-browsing termination;
- browser/profile change;
- device change;
- origin/path changes in some local-file workflows; or
- storage quota/security restrictions.

Autosave should not be treated as the authoritative study archive.

---

# 22. Report Export Limitations

CSV, Excel, and PDF are downstream representations.

They may not preserve the complete resumable tracker state.

- **CSV** supports limited recovery but may not restore every field.
- **Excel** is a report artifact, not a reload file.
- **PDF** is a presentation/record artifact, not a reload file.

Browser print settings can affect PDF colors, pagination, and scaling.

---

# 23. Legacy Compatibility

The current Study JSON intentionally retains:

```text
fileType: MPG_Downtime_Tracker_Study
version: 2
```

for compatibility with historical tracker studies and downstream tools.

Changing legacy identifiers or field semantics without coordinated migration can break existing files or multi-study analysis.

Older files may contain less category/detail information than current studies.

---

# 24. Data Privacy and Security

The tracker can store free-text notes, observer identifiers, action owners, and operational details.

Users are responsible for following company policy for:

- personal information;
- confidential production information;
- customer/product information;
- controlled technical information; and
- approved storage destinations.

Where possible, use non-sensitive identifiers rather than unnecessary personal data.

---

# 25. Safety and Control-System Boundary

The Downtime Tracker is not intended to function as:

- a machine safety system;
- safety PLC logic;
- a protective device;
- an interlock;
- an alarm-management system;
- a machine-control system; or
- a real-time production-control system.

Do not use tracker state, browser timing, or exported calculations as a safety-rated signal.

---

# 26. Engineering Use Disclaimer

> Abel Engineering tools are provided as engineering analysis and planning aids. Results depend on user-entered data, observations, assumptions, and configuration and should be independently reviewed before being used for production, safety, staffing, financial, regulatory, or equipment-design decisions. These tools do not replace professional engineering judgment, applicable standards, manufacturer requirements, site procedures, or required safety reviews.

Users are responsible for verifying calculations, inputs, outputs, and suitability for their intended application.

These applications are not intended to function as machine safety systems, safety PLC logic, protective devices, or real-time process-control systems.

---

# 27. Recommended Review Rule

Before using a result for a significant decision, confirm:

```text
Scope correct?
  ↓
Timing credible?
  ↓
Codes consistent?
  ↓
Exclusions justified?
  ↓
Production-rate assumption valid?
  ↓
Study representative?
  ↓
Result independently reviewed?
```

If any answer is uncertain, treat the output as preliminary.
