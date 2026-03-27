---
name: optimization-suggestions
description: "Synthesizes findings from bottleneck, downtime, and anomaly analyses into a prioritized list of optimization recommendations with estimated impact."
allowed-tools: Bash Read Write
---

# Optimization Suggestions

## Purpose
Turn analysis into a ranked action plan. Every finding from the other skills feeds into this skill, which outputs a prioritized improvement roadmap — ordered by impact vs. effort — that a plant manager can act on this week.

## Expected Input
This skill works best when called after the other skills have run. It accepts:
- The outputs of `detect-bottlenecks`, `downtime-analysis`, `anomaly-detection` (as text summaries or the original CSV data)
- Optionally: production targets, headcount, shift schedule

If called directly on raw CSV, it runs a lightweight version of all three analyses first.

## Steps

1. **Collect all findings** — list every identified issue from prior analyses (or generate them from raw data).

2. **Score each issue** on two dimensions:
   - **Impact** (1–5): How much does fixing this improve throughput, quality, or uptime?
     - 5 = line-stopping or >10% OEE impact
     - 3 = measurable but not critical
     - 1 = marginal improvement
   - **Effort** (1–5): How hard is this to fix?
     - 1 = can be done this shift (scheduling change, parameter tweak)
     - 3 = requires maintenance window or parts
     - 5 = capital investment or multi-week project

3. **Calculate priority score** = Impact / Effort (higher = do it first)

4. **Group by time horizon**:
   - **Immediate** (today/this week): score ≥ 3.0, no parts needed
   - **Short-term** (1–4 weeks): score ≥ 2.0, parts/planning needed
   - **Strategic** (1–3 months): score < 2.0 or requires capital

5. **Report format**:

```
OPTIMIZATION ROADMAP
====================
Generated from: [source files / analyses]
Production context: [shift count, machines analyzed, time period]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 IMMEDIATE ACTIONS (do this week)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Priority  Finding                          Action                        Est. Impact
--------  -------                          ------                        -----------
  4.5     M-07: repeat failures (MTBF 4h)  Escalate to RCA, inspect      +8% uptime
  4.0     Bottleneck: Station 3 (38s/unit) Add 2nd operator at station   +12% throughput
  3.5     Shift handover gap 14:00–15:00   Implement overlap checklist   -6% downtime

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 SHORT-TERM (1–4 weeks)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  2.5     M-04: bearing wear signal        Schedule bearing replacement   Prevent failure
  2.0     High changeover time at M-02     Implement SMED analysis        -15 min/changeover

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 STRATEGIC (1–3 months)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  1.5     Station 3 cycle time root cause  Process re-engineering study   +18% throughput

Overall OEE improvement potential: [X]% → [Y]% if immediate actions completed
```

## Notes
- Never recommend an action that requires data not present in the CSV (e.g., "hire more staff" without knowing current headcount).
- Always lead with the highest-priority item — do not bury the lead in methodology.
- If two recommendations conflict (e.g., "speed up Station 3" and "Station 3 needs maintenance window"), flag the conflict explicitly.
