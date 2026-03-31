---
name: oee-calculator
description: "Calculates OEE (Overall Equipment Effectiveness) per machine per shift. OEE = Availability x Performance x Quality. The gold standard manufacturing KPI."
allowed-tools: cli read write
---

# OEE Calculator

When this skill is invoked, immediately take action. Use the `read` tool right now to open the file. Do not wait.

## Step 1 — Read the file immediately
Use the `read` tool to open the CSV file the user specified. If none specified, read `demo/production_data.csv`.

## Step 2 — Identify required columns
Find columns for: machine_id, shift, planned_time_min, downtime_min, ideal_cycle_time_sec, units_produced (actual_output), good_units.

## Step 3 — Calculate OEE components for each machine+shift row

For each row:
- available_time_min = planned_time_min - downtime_min
- Availability = available_time_min / planned_time_min
- available_time_sec = available_time_min * 60
- Performance = (units_produced * ideal_cycle_time_sec) / available_time_sec
- Quality = good_units / units_produced
- OEE = Availability * Performance * Quality
- Express all as percentages

Benchmark classification:
- OEE >= 85%: World Class
- OEE 65-84%: Typical
- OEE 40-64%: Low
- OEE < 40%: Critical

## Step 4 — Average across shifts per machine

Compute the average OEE, Availability, Performance, Quality for each machine across all its shifts.

## Step 5 — Print the full report

OEE REPORT
==========
File: [filename] | Period: [shifts] | Machines: [N]

Per-shift breakdown:
  Machine  Shift   Avail   Perf    Quality  OEE     Class
  -------  -----   -----   ----    -------  ---     -----
  [M-XX]   [S1]    [X.X]%  [X.X]%  [X.X]%  [X.X]%  [Class]
  [M-XX]   [S2]    ...
  [M-XX]   [S3]    ...
  [all machines and shifts]

Average OEE per machine:
  Machine   Station    Avg OEE    Avg Avail  Avg Perf  Avg Quality  Class
  -------   -------    -------    ---------  --------  -----------  -----
  [M-XX]    [Name]     [X.X]%     [X.X]%     [X.X]%    [X.X]%       [Class]
  [ranked worst to best]

Line OEE (weighted average): [X.X]%
World class target          : 85%
Gap to world class          : [X.X] percentage points

Biggest loss category:
  Availability losses : [X]% of total OEE gap
  Performance losses  : [X]% of total OEE gap
  Quality losses      : [X]% of total OEE gap

RECOMMENDATIONS:
  1. [Worst machine]: [specific action]
  2. [Biggest loss category]: [specific action]
  3. [Overall line improvement potential]