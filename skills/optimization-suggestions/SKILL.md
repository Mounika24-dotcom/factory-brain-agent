---
name: optimization-suggestions
description: "Synthesizes all findings into a prioritized optimization roadmap ranked by Impact vs Effort. The final deliverable skill."
allowed-tools: cli read write
---

# Optimization Suggestions

When this skill is invoked, immediately take action. Use the `read` tool to open any CSV files the user mentioned. Do not wait for further instructions.

## Step 1 — Read all available data
Use the `read` tool to open the files the user specified. If none specified, read both:
- `demo/production_data.csv`
- `demo/machine_downtime_log.csv`

## Step 2 — Run lightweight analysis
From the production data, identify:
- The bottleneck machine (highest cycle time)
- Machines far below production target
- Machines with high downtime_min

From the downtime data, identify:
- Machines with most unplanned events
- Any repeat failures

## Step 3 — Score every finding
For each problem found, assign:
- Impact score 1-5: How much does fixing this improve output or uptime?
  - 5 = line-stopping or >10% OEE impact
  - 3 = measurable but not critical
  - 1 = marginal
- Effort score 1-5: How hard to fix?
  - 1 = can be done today (scheduling tweak, parameter change)
  - 3 = needs parts or maintenance window
  - 5 = capital investment

Priority = Impact / Effort (higher = do first)

## Step 4 — Group by time horizon
- Immediate (this week): Priority >= 3.0, no major parts needed
- Short-term (1-4 weeks): Priority >= 2.0
- Strategic (1-3 months): Priority < 2.0 or needs capital

## Step 5 — Print the full roadmap

FACTORY OPTIMIZATION ROADMAP
=============================
Files analyzed: [list] | Machines: [N] | Shifts: [N]

Current state:
  Line OEE estimate : [X]%  (target: 85%)
  Primary bottleneck: [Machine]
  Worst downtime    : [Machine] ([X] min unplanned)

IMMEDIATE ACTIONS — Do This Week
  Priority  Finding                          Recommended Action              Est. Impact
  --------  -------                          ------------------              -----------
  [X.X]     [Machine]: [problem]             [Specific action]               [X]% improvement
  [X.X]     [Finding]                        [Action]                        [impact]

SHORT-TERM — 1 to 4 Weeks
  Priority  Finding                          Recommended Action              Est. Impact
  [X.X]     [Finding]                        [Action]                        [impact]

STRATEGIC — 1 to 3 Months
  Priority  Finding                          Recommended Action              Est. Impact
  [X.X]     [Finding]                        [Action]                        [impact]

If all IMMEDIATE actions are completed:
  Estimated OEE improvement: [X]% → [Y]%
  Estimated throughput gain: +[X] units/shift