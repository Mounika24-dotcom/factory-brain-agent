---
name: anomaly-detection
description: "Detects statistically unusual patterns in production or sensor data using Z-score and IQR methods. Flags values that deviate significantly from historical norms."
allowed-tools: Bash Read Write
---

# Anomaly Detection

## Purpose
Catch what humans miss. Unusual temperature readings, output drops, yield spikes, or timing irregularities that don't trigger hard alarms but signal a developing problem. Early detection = cheaper fix.

## Expected Input
A CSV with time-series or batch data. Common formats:
- Sensor data: `timestamp`, `machine_id`, `temperature`, `vibration`, `pressure`, `rpm`
- Production data: `shift`, `machine_id`, `units_produced`, `defect_count`, `cycle_time_sec`
- Quality data: `batch_id`, `yield_pct`, `defect_rate`, `test_result`

## Steps

1. **Profile the data** — for each numeric column, compute:
   - Mean, median, std deviation, min, max
   - Time range and sampling frequency (if timestamped)
   - % of missing values

2. **Apply anomaly detection — two methods**:

   **Z-score method** (for normally distributed data):
   - Flag any value where |z| > 3.0 as a strong anomaly
   - Flag |z| > 2.5 as a warning
   - Formula: z = (value − mean) / std_dev

   **IQR method** (for skewed or non-normal data):
   - Q1 = 25th percentile, Q3 = 75th percentile, IQR = Q3 − Q1
   - Flag values below Q1 − 1.5×IQR or above Q3 + 1.5×IQR
   - Use this when the column has outliers that skew the mean

3. **Cross-column correlation check**:
   - If temperature is anomalous AND vibration is anomalous at the same timestamp → escalate to CRITICAL
   - Single-variable anomalies = WARNING
   - Multi-variable anomalies at same time/machine = CRITICAL

4. **Report format**:

```
ANOMALY DETECTION REPORT
=========================
Dataset: [filename]  |  Rows: [N]  |  Columns analyzed: [list]

Summary:
  Critical anomalies (multi-variable):  [N] events
  Warnings (single-variable):           [N] events
  Clean readings:                       [N] rows ([X]%)

Critical anomalies:
  Timestamp          Machine  Variables                 Severity
  ---------          -------  ---------                 --------
  2024-03-14 06:32   M-04     temp=94°C (z=3.8) +      CRITICAL
                              vibration=12.4mm (z=3.2)
  ...

Warning anomalies (top 5 by z-score):
  ...

Interpretation:
  → M-04 at 06:32: simultaneous high temp + vibration suggests bearing failure risk.
    Recommend immediate inspection before next shift.
  → [Next finding with specific recommendation]

Columns with no anomalies: [list]
```

## Notes
- Always report which method was used per column and why.
- If a machine shows anomalies in >20% of its readings, it may be a systemic calibration issue rather than discrete events — flag this distinction.
- Do not call a value anomalous just because it is the max in the dataset — it must exceed the statistical threshold.
