---
name: downtime-analysis
description: "Analyzes machine downtime logs to classify idle time, identify chronic failure machines, and compute MTBF and MTTR reliability metrics."
allowed-tools: cli read write
---

# Downtime Analysis

When this skill is invoked, immediately take action. Use the `read` tool right now. Do not wait for further instructions.

## Step 1 — Read the file immediately
Use the `read` tool to open the file the user specified. If none specified, read `demo/machine_downtime_log.csv`.

## Step 2 — Classify each downtime event
Read each row and classify the downtime_type or reason into:
- Unplanned: breakdown, fault, failure, emergency, overtemperature, jam
- Planned: scheduled maintenance, changeover, lubrication, PM
- Operational: waiting for material, waiting for operator, quality hold

## Step 3 — Compute per-machine metrics
For each machine_id:
- Total downtime minutes (all types)
- Unplanned downtime minutes
- Unplanned downtime % = unplanned_min / total_planned_time * 100
  (assume 480 min per shift, multiply by number of shifts if not given)
- Number of unplanned events
- MTBF = available operating time / number of unplanned failures
- MTTR = total unplanned downtime / number of unplanned events
- Check: are there repeat failures on same machine within 48 hours? (bad repair signal)

## Step 4 — Print the full report

DOWNTIME ANALYSIS REPORT
=========================
File: [filename] | Events: [N] | Period: [date range]

All events summary:
  Total events     : [N]
  Unplanned        : [N] events — [X] min total
  Planned          : [N] events — [X] min total
  Operational      : [N] events — [X] min total

Per-machine ranking (worst unplanned downtime first):
  Machine  Unplanned%  Unplanned Min  MTBF      MTTR      Repeat Failures
  -------  ----------  -------------  ----      ----      ---------------
  [M-XX]   [X.X]%      [X] min        [X.X] hr  [X] min   [N] (WARNING if >1)
  [all machines]

Top failure reasons (unplanned only):
  1. [Reason]  — [N] events ([X]%)
  2. [Reason]  — [N] events ([X]%)
  3. [Reason]  — [N] events ([X]%)

Patterns detected:
  [Any machine with repeat failures within 48h — flag as BAD REPAIR]
  [Any time-of-day pattern across machines]
  [Any machine responsible for >30% of all unplanned downtime]

RECOMMENDATIONS:
  1. [Worst machine]: [specific action based on failure reason]
  2. [Repeat failure machine]: Escalate to root cause analysis
  3. [Pattern finding]: [specific operational change]