---
name: oee-calculator
description: "Calculates Overall Equipment Effectiveness (OEE) — the gold standard manufacturing KPI — broken down into Availability, Performance, and Quality components."
allowed-tools: Bash Read Write
---

# OEE Calculator

## Purpose
OEE (Overall Equipment Effectiveness) is the single most important metric in manufacturing. A world-class factory runs at 85% OEE. Most factories run at 40–60%. This skill computes OEE per machine and per shift, and decomposes it so engineers know exactly where the losses are.

## Formula
OEE = Availability × Performance × Quality

- **Availability** = (Planned production time − Downtime) / Planned production time
- **Performance** = (Actual output × Ideal cycle time) / Available time
- **Quality** = Good units / Total units produced

## Expected Input
A CSV with:
- `machine_id`
- `planned_time_min` — scheduled production window
- `downtime_min` — total downtime in that window
- `ideal_cycle_time_sec` — theoretical fastest cycle time
- `actual_output` — units produced
- `good_units` — units passing quality check
- `shift` or `date`

Alternatively, the skill can compute components separately if data comes from multiple CSV files.

## Steps

1. **Validate inputs** — check for impossible values (downtime > planned time, good_units > actual_output, etc.).

2. **Calculate the three components per machine per shift**:
   - Availability = (planned_time − downtime) / planned_time
   - Performance = (actual_output × ideal_cycle_time) / (available_time in seconds)
   - Quality = good_units / actual_output

3. **Calculate OEE** = Availability × Performance × Quality

4. **Classify against benchmarks**:
   - ≥ 85%: World class
   - 65–84%: Typical (room for improvement)
   - 40–64%: Low (significant losses)
   - < 40%: Critical

5. **Identify the dominant loss** — which of the three components drags OEE down most?

6. **Report format**:

```
OEE REPORT
==========
Period: [date range]  |  Machines: [N]  |  Shifts: [N]

Machine  Shift  Availability  Performance  Quality   OEE    Benchmark
-------  -----  ------------  -----------  -------   ---    ---------
M-01     S1     94.2%         81.3%        98.1%     75.1%  Typical
M-02     S1     71.4%         88.6%        96.3%     60.9%  Low ⚠
M-03     S1     89.7%         63.1%        97.4%     55.2%  Low ⚠
M-07     S1     61.2%         79.8%        95.1%     46.4%  Critical 🔴

Line OEE (weighted average): 59.2%  →  Target: 85%  →  Gap: 25.8pp

Top OEE loss categories (all machines):
  Availability losses:  38% of total gap  ← biggest lever
  Performance losses:   41% of total gap
  Quality losses:       21% of total gap

Recommendations by loss type:
  Availability → Address M-07 downtime (see downtime-analysis report)
  Performance  → Station 3 cycle time optimization (see bottleneck report)
  Quality      → M-02 defect rate at 3.7% — root cause investigation needed
```

## Notes
- OEE must never be reported as a single number without its three components. The components tell you where to focus.
- If ideal cycle time is not in the data, Performance cannot be calculated — report Availability and Quality only and flag the gap.
- OEE below 40% is a data quality flag as well as an operational one — verify inputs before reporting.
