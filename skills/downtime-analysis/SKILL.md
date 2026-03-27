---
name: downtime-analysis
description: "Analyzes machine downtime logs to classify idle time, identify chronic offenders, and compute MTBF/MTTR metrics for each machine."
allowed-tools: Bash Read Write
---

# Downtime Analysis

## Purpose
Downtime is lost money. This skill classifies every downtime event, finds which machines fail most often, and computes the key reliability metrics maintenance teams use to plan interventions.

## Expected Input
A CSV with at least:
- `machine_id` — which machine
- `downtime_start` and `downtime_end` (or `downtime_duration_min`) — when and how long
- `reason` or `downtime_type` — cause category (breakdown, changeover, planned maintenance, waiting for material, etc.)
- `shift` or `date` — time context

## Steps

1. **Parse and validate** — check for missing timestamps, overlapping events, or unknown reason codes. Report data quality issues.

2. **Classify downtime events**:
   - Planned: scheduled maintenance, changeover, breaks
   - Unplanned: breakdown, fault, power failure
   - Operational: waiting for material, waiting for operator, quality hold
   Label each row with its class.

3. **Calculate per-machine metrics**:
   - Total downtime (min) — split by planned / unplanned / operational
   - Downtime % = total downtime / total time window × 100
   - Number of unplanned events
   - MTBF (Mean Time Between Failures) = operating time / number of failures
   - MTTR (Mean Time To Repair) = total repair time / number of repairs

4. **Rank machines by unplanned downtime %** — this is the critical number.

5. **Identify patterns**:
   - Does downtime spike at shift change?
   - Is one machine responsible for >30% of all unplanned downtime?
   - Are there repeat failures on the same machine within 48 hours (indicating a bad repair)?

6. **Report format**:

```
DOWNTIME ANALYSIS
=================
Period: [start] to [end]  |  Total events: [N]  |  Total downtime: [X] hrs

By machine (ranked by unplanned downtime %):
  Machine    Unplanned%  MTBF     MTTR    Events
  -------    ----------  ----     ----    ------
  M-07       31.4%       4.2 hr   48 min  12
  M-03       18.7%       7.8 hr   31 min   8
  ...

Top failure reasons (unplanned only):
  1. Sensor fault         — 34% of events
  2. Mechanical jam       — 28% of events
  3. Overtemperature trip — 19% of events

Patterns detected:
  ⚠ M-07: 4 repeat failures within 48h — repair may not be resolving root cause
  ⚠ Downtime spikes between 14:00–15:00 across all machines (shift handover gap?)

Recommendations:
  → M-07: Escalate to root cause analysis. MTBF of 4.2h is critical threshold.
  → Shift handover: introduce 10-min overlap checklist to close the 14:00 gap.
```

## Notes
- If reason codes are missing, still compute duration metrics but flag classification as incomplete.
- Never conflate planned and unplanned downtime in the headline figure — they have completely different business implications.
