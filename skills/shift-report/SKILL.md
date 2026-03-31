---
name: shift-report
description: "Generates a concise end-of-shift handover report in Markdown from production CSV data. Covers headline metrics, top issues, open actions, and next-shift recommendations."
allowed-tools: cli read write
---

# Shift Report

When this skill is invoked, immediately take action. Use the `read` tool right now to open the file. Do not wait for further instructions.

## Step 1 — Read the file immediately
Use the `read` tool to open the CSV file the user specified. If none specified, read `demo/production_data.csv`.

## Step 2 — Extract shift data
Identify what shifts are in the data. If the user asked for a specific shift, filter to that shift. Otherwise use the first shift found.

## Step 3 — Compute headline metrics for the shift
- Total units produced (sum across all machines)
- Total target units
- Attainment % = produced / target * 100
- Total downtime minutes across all machines
- Highest defect machine (most defect_count)
- Overall OEE estimate if data allows

## Step 4 — Identify top 3 issues
Look for: machines with highest downtime, biggest gaps from target, highest defect counts.

## Step 5 — Write and SAVE the report
Use the `write` tool to save the report to `demo/shift_report_output.md`.
Then also print the full report to the terminal.

The report format:

# Shift Report — [Shift Name] | [Date]

**Line:** [Line/Factory ID if available]  |  **Duration:** 480 min (8 hrs)

---

## Headline Numbers
| Metric | Actual | Target | Result |
|--------|--------|--------|--------|
| Units produced | [N] | [N] | [+/-N] ([X]%) |
| Machines running | [N] | [N] | — |
| Total downtime | [X] min | 0 min | +[X] min |
| Best machine | [M-XX] ([N] units) | — | — |
| Worst machine | [M-XX] ([N] units) | [N] | -[N] units |

---

## Top Issues This Shift
1. **[Machine]: [Problem]** — [specific numbers from the data]
2. **[Machine]: [Problem]** — [specific numbers]
3. **[Finding]** — [specific numbers]

---

## Open Actions for Incoming Shift
- [ ] [Specific action needed based on data]
- [ ] [Another action]

---

## Recommendations for Incoming Shift
- [Specific recommendation 1]
- [Specific recommendation 2]
- [Specific recommendation 3]