---
name: detect-bottlenecks
description: "Identifies the production bottleneck — the stage with the longest cycle time or lowest throughput — using Theory of Constraints logic applied to CSV production data."
allowed-tools: Bash Read Write
---

# Detect Bottlenecks

## Purpose
Find the constraint that limits the entire production line's output. The bottleneck is the slowest stage. Every other optimization is secondary until the bottleneck is addressed.

## Expected Input
A CSV file with at least these columns (exact names may vary — infer from headers):
- `machine_id` or `station_id` — identifier for each production stage
- `cycle_time_sec` or equivalent — time to complete one unit
- `units_produced` — output count per shift or time window
- `shift` or `date` — time dimension (optional but useful)

## Steps

1. **Parse the CSV** — read headers, detect column types, report row count and time range covered.

2. **Calculate throughput per station** — for each machine/station:
   - Average cycle time
   - Units per hour (3600 / avg_cycle_time)
   - Coefficient of variation (std_dev / mean) — high CV means inconsistent output

3. **Identify the bottleneck** — the station with:
   - Highest average cycle time, OR
   - Lowest units per hour, OR
   - Highest queue buildup upstream (if queue data is present)

4. **Check for secondary bottlenecks** — rank all stations by cycle time. If station #2 is within 10% of the primary bottleneck, flag it as a near-bottleneck.

5. **Report findings** in this format:

```
BOTTLENECK ANALYSIS
===================
Primary bottleneck: [Station X]
  Avg cycle time:   [X.X sec]  (line average: [Y.Y sec])
  Throughput:       [X] units/hr  (line target: [Y] units/hr)
  Impact:           Every minute of downtime here = [Z] minutes of lost output line-wide

Near-bottleneck:   [Station Y] — [X.X sec], within [N]% of primary

Ranked stations (slowest → fastest):
  1. [Station X]  [X.X sec/unit]
  2. [Station Y]  [X.X sec/unit]
  ...

Recommendation:
  → [Specific, actionable next step for Station X]
  → [Secondary recommendation if near-bottleneck is close]
```

## Notes
- If cycle times are missing for some stations, flag them and exclude from ranking.
- If the CSV contains multiple shifts, report per-shift bottleneck separately — the bottleneck can shift between shifts.
- Do not recommend "speed up the bottleneck" generically. Suggest specific levers: additional operator, reduced changeover time, preventive maintenance schedule, etc.
