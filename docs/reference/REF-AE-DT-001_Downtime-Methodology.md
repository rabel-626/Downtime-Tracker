---
documentId: REF-AE-DT-001
title: Downtime Capture and Analysis Methodology
tool: Downtime-Tracker
documentVersion: 1.1
studyFileType: MPG_Downtime_Tracker_Study
studyFileVersion: 2
status: Review Draft
updated: 2026-09-08
---

# Downtime Tracker — Capture & Analysis Methodology

## 1. Purpose

This reference defines the measurement model and current calculation logic used by the Abel Engineering Downtime Tracker.

It explains how the tracker converts:

```text
Observed time
+ downtime events
+ reason-code inclusion rules
+ production speed
```

into:

```text
Included study scope
Included downtime
Included uptime
Study-scope utilization
Event frequency
Estimated production loss
Pareto rankings
```

This document describes the **current application logic**. It is not a universal downtime, OEE, reliability, or financial-loss standard.

---

# 2. Measurement Model

The tracker represents one study as four connected structures:

```text
Study
├── Study identity and context
├── Observed study time
│   └── Timer segments OR manual total duration
├── Downtime events
│   └── Category + Cause Code + Duration + Note
└── Reason-code definitions
    └── Include / Exclude analytical scope
```

The core rule is:

> A completed event remains part of the historical event log regardless of whether its reason code is included in summary analysis.

The **Include in Summary** setting determines whether that event belongs to included downtime analysis or is treated as time outside the included analytical scope.

---

# 3. Time Bases

## 3.1 Timer Capture

Timer Capture accumulates one or more study segments.

For segment `j`:

```text
Segment Duration_j
= Segment Stop_j − Segment Start_j
```

Total Logged Study Time is:

```text
T_logged
= Σ Segment Duration_j
  + Current Active Segment Duration, if running
```

The current implementation rounds timer durations to whole seconds.

Stopping the study while downtime is active closes the event and the study segment at the same observation timestamp.

## 3.2 Manual Entry

Manual Entry does not derive observed time from timestamps. The observer enters the total duration directly.

Supported study-duration interpretations are:

```text
H:MM:SS  → hours : minutes : seconds
MM:SS    → minutes : seconds
number   → minutes
```

Example:

```text
2:15:30 = 2 h + 15 min + 30 s = 8,130 s
```

Individual manual event durations follow a different rule:

```text
MM:SS    → minutes : seconds
number   → seconds
```

This distinction is intentional in the current implementation and requires careful manual-entry review.

---

# 4. Event Duration

## 4.1 Timer event

For a timer-captured event:

```text
D_i
= Event Stop Timestamp_i − Event Start Timestamp_i
```

The recorded value is rounded to the nearest whole second.

Only one timer-based downtime event can be active at a time.

## 4.2 Manual event

A manually entered event contains a directly entered duration rather than live start/stop evidence.

For analysis, the event is valid when the duration is numeric and nonnegative.

---

# 5. Reason-Code Classification

A reason-code definition contains:

```text
Category
Cause Code
Description
Include in Summary
```

The intended unique key is:

```text
Category + Cause Code
```

Categories are normalized to uppercase for comparison/grouping.

### Code lookup behavior

When an event has a code, the tracker attempts to find its reason-code definition using the code and category.

If no matching definition exists, the event is currently treated as **included** by default and is flagged through data-quality review when its description cannot be resolved.

If an event has no cause code, it is also treated as included by the current calculation logic and appears as `UNCODED` in the Pareto.

> **Review implication:** Uncoded and undefined-code events should normally be resolved before study approval because they can still affect included downtime totals.

---

# 6. Included and Excluded Analytical Scope

Let:

```text
T_logged     = total logged study time
T_excluded   = sum of completed excluded-event durations
T_scope      = included study time
D_included   = sum of completed included-event durations
T_uptime     = included uptime
```

## 6.1 Excluded Event Time

```text
T_excluded
= Σ duration of completed events whose code is excluded
```

## 6.2 Included Study Time

```text
T_scope
= max(0, T_logged − T_excluded)
```

This is an important semantic rule:

> **Excluded event time is removed from the analysis denominator. It is not reclassified as uptime.**

Examples of why a site might exclude a code include intentional off-scope time, planned observation interruptions, or another locally defined non-comparable condition. The tool does not decide whether an exclusion is appropriate.

## 6.3 Included Downtime

```text
D_included
= Σ duration of completed included events
```

## 6.4 Included Uptime

```text
T_uptime
= max(0, T_scope − D_included)
```

---

# 7. Study-Scope Utilization

The tracker calculates:

```text
U
= T_uptime / T_scope
```

or, as a percent:

```text
Utilization %
= 100 × T_uptime / T_scope
```

when `T_scope > 0`.

Equivalent form when included downtime does not exceed included study time:

```text
Utilization %
= 100 × (1 − D_included / T_scope)
```

### Interpretation

This metric answers:

> “Within the time retained in the study's included scope, what fraction was not recorded as included downtime?”

It does **not** by itself measure:

- performance loss while running;
- quality loss;
- ideal-cycle-rate performance;
- OEE;
- scheduled vs unscheduled availability under an external standard.

---

# 8. Event Frequency

Included Events per Hour uses the full observed study duration, not Included Study Time.

Let:

```text
N_included = number of completed included events
H_observed = T_logged / 3600
```

Then:

```text
Included Events / Hour
= N_included / H_observed
```

This answers:

> “How many included downtime events occurred per observed hour?”

### Why the denominator differs from utilization

The tracker intentionally uses:

- **Included Study Time** for utilization and included-scope production calculations; but
- **Total Logged Study Time** for event frequency.

This prevents exclusions from artificially increasing event frequency simply by shrinking the denominator.

---

# 9. Included-Event Summary Statistics

The Summary Statistics panel uses completed **included** event durations only.

Let the included durations be:

```text
x_1, x_2, ..., x_n
```

## 9.1 Count

```text
n = number of included completed events
```

## 9.2 Total Downtime

```text
D_total
= Σ x_i
```

## 9.3 Mean Duration

```text
x̄
= (Σ x_i) / n
```

## 9.4 Minimum and Maximum

```text
x_min = min(x_i)
x_max = max(x_i)
```

## 9.5 Sample Standard Deviation

For `n > 1`:

```text
s
= √[ Σ(x_i − x̄)² / (n − 1) ]
```

The tracker displays standard deviation as `N/A` when fewer than two included events exist.

### Interpretation

Standard deviation describes dispersion of the included event-duration sample. It does not establish statistical control, capability, or a confidence interval by itself.

---

# 10. Production-Impact Model

Let:

```text
R = entered production speed, units/hour
```

The model assumes a constant reference rate during the analyzed period.

## 10.1 Estimated Units Lost

For both collection modes:

```text
Estimated Units Lost
= R × (D_included / 3600)
```

This is a time-equivalent production-loss estimate.

It is not an actual production counter and does not model:

- ramp-up after recovery;
- scrap or rework;
- reduced speed while technically running;
- downstream starvation/blocking propagation;
- buffer depletion/recovery;
- product-mix rate changes unless the entered rate is updated appropriately.

---

# 11. Projected Included-Scope Units

The current implementation intentionally uses different time bases for Timer Capture and Manual Entry.

## 11.1 Timer Capture

```text
Projected Included-Scope Units_timer
= R × (T_uptime / 3600)
```

Timer Capture therefore models units over the retained **uptime** portion of the observed study.

## 11.2 Manual Entry

```text
Projected Included-Scope Units_manual
= R × (T_scope / 3600)
```

Manual Entry therefore models units over the **included study scope before subtracting included downtime**. Estimated Units Lost is shown separately.

> **Important:** Do not compare the Projected Included-Scope Units field between Timer and Manual studies without understanding this mode-specific calculation basis.

If an organization requires a single standardized production-output equation across both modes, that should be addressed as a controlled application change rather than silently reinterpreting the current output.

---

# 12. Pareto Method

The Included-Cause Pareto uses completed included events only.

For coded events, the grouping identity is:

```text
Category / Cause Code
```

For uncoded events:

```text
UNCODED
```

For cause `k`:

```text
D_k = Σ included event duration assigned to cause k
N_k = count of included events assigned to cause k
```

Total included downtime is:

```text
D_total = Σ D_k
```

Cause share is:

```text
Cause Percent_k
= D_k / D_total
```

After sorting causes from largest to smallest duration:

```text
Cumulative Percent_k
= Σ Cause Percent_j, for j = 1 ... k
```

### Pareto interpretation

Duration Pareto answers:

> “Which included causes consumed the most observed downtime?”

It does not directly answer:

- which cause occurs most frequently;
- which cause is most expensive;
- which cause has the greatest safety risk;
- which cause is easiest to eliminate; or
- which cause produces the largest downstream disruption.

Review event count and notes with total duration.

---

# 13. Category and Cause Structure

The category/cause hierarchy provides two levels of classification:

```text
Category
└── Cause Code
```

A good category is broad enough to support cross-study aggregation but specific enough to preserve ownership or process meaning.

A good cause code identifies one repeatable observed failure mode or delay mechanism.

### Example structure

```text
MATERIAL
├── NO_MATERIAL
├── WRONG_MATERIAL
└── MATERIAL_JAM

AUTOMATION
├── SENSOR_FAULT
├── ROBOT_FAULT
└── PLC_STOP
```

The exact vocabulary should come from the controlled template, not this example.

---

# 14. Primary Cause and Concurrent Conditions

Timer Capture permits one active downtime event at a time. Consequently, the tracker is fundamentally a **mutually exclusive event timeline** for live capture.

When multiple contributing conditions exist during the same stop:

1. assign the agreed primary cause to the event; and
2. document contributing conditions in the event note.

Avoid duplicating the same elapsed seconds across several manual rows unless the analytical method explicitly intends overlapping exposure time rather than mutually exclusive downtime.

---

# 15. Data-Quality Logic

The current tracker surfaces nine main checks:

1. Included study time exists.
2. Production speed is entered.
3. Completed events have downtime codes.
4. Used codes have descriptions.
5. Category + Cause Code combinations are unique.
6. Completed events have notes.
7. Included downtime does not exceed included study time.
8. No downtime event remains active.
9. The study timer is stopped/not active.

The tool does not automatically block save/export for warnings. Engineering review remains required.

---

# 16. Frozen Snapshot Method

A repository or mobile save performed while timing is active can freeze the saved copy through one timestamp without stopping the live timers on the source device.

At snapshot time `t_s`:

### If downtime is active

```text
Saved Event Stop = t_s
Saved Event Duration = t_s − Event Start
Saved Active Event = none
```

### If the study is active

```text
Saved Study Segment Stop = t_s
Saved Study Timer State = paused
```

The live browser state remains active.

This method prevents elapsed transfer/offline time from becoming part of the reopened study.

---

# 17. Worked Example

Assume a two-hour Timer Capture study:

```text
Total Logged Study Time = 7,200 s
Excluded Event Time     =   300 s
Included Downtime       =   900 s
Included Event Count    =    12
Production Speed        = 1,800 units/hr
```

## Step 1 — Included Study Time

```text
T_scope
= 7,200 − 300
= 6,900 s
```

## Step 2 — Included Uptime

```text
T_uptime
= 6,900 − 900
= 6,000 s
```

## Step 3 — Study-Scope Utilization

```text
U
= 6,000 / 6,900
= 0.869565...
≈ 86.96%
```

## Step 4 — Included Events per Hour

Event frequency uses full observed hours:

```text
H_observed
= 7,200 / 3,600
= 2 hr

Events/hr
= 12 / 2
= 6.0 events/hr
```

## Step 5 — Estimated Units Lost

```text
Units Lost
= 1,800 × (900 / 3,600)
= 450 units
```

## Step 6 — Timer Projected Included-Scope Units

```text
Projected Units_timer
= 1,800 × (6,000 / 3,600)
= 3,000 units
```

If the same durations were entered as a Manual Entry study, the current tool would calculate:

```text
Projected Units_manual
= 1,800 × (6,900 / 3,600)
= 3,450 units
```

while still showing:

```text
Estimated Units Lost = 450 units
```

This illustrates why collection mode must remain visible when interpreting projected-unit outputs.

---

# 18. Recommended Engineering Interpretation

Use the tracker outputs in this order:

```text
1. Validate study scope and timing
2. Validate coding and exclusions
3. Review included downtime total
4. Review duration Pareto
5. Review event frequency
6. Inspect event notes for mechanism/context
7. Review production-impact estimate
8. Assign improvement actions
```

A Pareto chart should normally be the beginning of root-cause investigation, not the end of it.

---

# 19. Related Documents

- `QS-AE-DT-001` — Downtime Tracker Quick Start
- `WI-AE-DT-001` — Downtime Tracker Work Instructions
- `LIM-AE-DT-001` — Downtime Tracker Limitations
- `DATA-AE-DT-001` — Downtime Tracker Data Handling
