---
name: shift-report
description: "Generates a concise shift summary report in Markdown — key metrics, top issues, and next-shift recommendations — from a single shift's production CSV."
allowed-tools: Bash Read Write
---

# Shift Report

## Purpose
At the end of every shift, the outgoing team needs a crisp handover document. This skill generates it automatically from raw CSV data — saving 20–30 minutes of manual report writing and ensuring nothing gets missed.

## Expected Input
Any production CSV covering a single shift's data. Can combine outputs from other skills if already run.

## Steps

1. **Extract shift metadata** — shift number/name, date, start/end time, machines active.

2. **Compute headline metrics**:
   - Total units produced vs. target
   - Overall line OEE (if data allows)
   - Total unplanned downtime
   - Top defect rate

3. **Summarize top 3 issues** — the three things the incoming shift most needs to know about.

4. **List open actions** — any issues that were not resolved and need follow-up.

5. **Generate next-shift recommendations** — specific, actionable items for the incoming team.

6. **Output a clean Markdown report**:

```markdown
# Shift Report — [Shift Name] | [Date]

**Line:** [Line ID]  |  **Supervisor:** [if available]  |  **Duration:** [X hrs]

---

## Headline Numbers
| Metric | Actual | Target | Δ |
|--------|--------|--------|---|
| Units produced | 1,247 | 1,300 | -53 (-4.1%) |
| OEE | 71.3% | 85% | -13.7pp |
| Unplanned downtime | 42 min | 0 min | +42 min |
| Defect rate | 2.1% | <1% | +1.1pp |

---

## Top 3 Issues This Shift
1. **M-07 unplanned stop (23 min)** — sensor fault at 14:32. Temporary fix applied. Not resolved.
2. **Station 3 cycle time increase** — avg 38s vs. 29s ideal. Operator reports tooling wear.
3. **Defect spike on Batch #441** — 8 rejects, QC hold applied. Root cause TBD.

---

## Open Actions for Incoming Shift
- [ ] M-07: maintenance team notified, awaiting parts (ETA: 06:00)
- [ ] Station 3: tooling replacement needed before next run
- [ ] Batch #441: QC disposition required

---

## Recommendations for Incoming Shift
- Prioritize M-07 repair before 08:00 to recover line rate
- Check Station 3 tooling before startup
- Do not release Batch #441 until QC clears it
```

## Notes
- This report is for humans, not for further machine processing. Use plain language in the issues section.
- If target data is not in the CSV, omit the Δ column and note that targets are unavailable.
- Never fabricate a supervisor name or targets that are not in the data.
