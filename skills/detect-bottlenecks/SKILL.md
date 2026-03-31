---
name: detect-bottlenecks
description: "Identifies the production bottleneck by reading a CSV file and applying Theory of Constraints logic to find the slowest machine or station."
allowed-tools: cli read write
---

# Detect Bottlenecks

When this skill is invoked, immediately take action. Do not wait for further instructions. Use the `read` tool right now to open the file.

## Step 1 — Read the file immediately
Use the `read` tool to open the CSV file the user specified. If no file path was given, read `demo/production_data.csv`.

## Step 2 — Parse the headers
Identify which columns represent: machine ID, station name, cycle time (seconds), units produced, ideal cycle time, shift, planned time, and downtime.

## Step 3 — Calculate per-machine averages
For each unique machine_id, compute:
- Average cycle_time_sec across all shifts
- Average units_produced per shift
- Throughput = 3600 / avg_cycle_time (units per hour)
- Gap from ideal = avg_cycle_time - ideal_cycle_time_sec
- Gap percentage = (gap / ideal_cycle_time_sec) * 100

## Step 4 — Rank and identify bottleneck
Sort machines from highest to lowest average cycle time. The top machine is the bottleneck. Flag any machine within 10% of the bottleneck as a near-bottleneck.

## Step 5 — Print the full report

Print this report with real numbers filled in:

BOTTLENECK ANALYSIS REPORT
===========================
File: [filename] | Rows: [N] | Machines: [N] | Shifts: [N]

PRIMARY BOTTLENECK: [Machine ID] - [Station Name]
  Average cycle time : [X.X] sec  (ideal: [Y] sec)
  Throughput         : [X] units/hr
  Slowdown vs ideal  : +[X.X] sec ([X]% above ideal)

NEAR-BOTTLENECK: [Machine] at [X.X sec] — within [N]% of primary

ALL STATIONS RANKED (slowest to fastest):
  1. [M-XX] [Station]   [X.X sec]   [X/hr]   +[X]% vs ideal  <- BOTTLENECK
  2. [M-XX] [Station]   [X.X sec]   [X/hr]   +[X]% vs ideal  <- NEAR-BOTTLENECK
  3. [M-XX] [Station]   [X.X sec]   [X/hr]   +[X]% vs ideal
  ...

RECOMMENDATIONS:
  1. [Bottleneck machine]: [Specific action based on the data]
  2. [Near-bottleneck if within 15%]: [Specific action]
  3. [Any shift-level pattern noticed]: [Action]

Throughput improvement if bottleneck is fixed: ~[X]% increase in line output