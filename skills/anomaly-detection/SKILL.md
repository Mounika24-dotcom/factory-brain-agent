---
name: anomaly-detection
description: "Detects statistically unusual patterns in sensor or production CSV data using Z-score and IQR methods. Flags critical multi-variable anomalies and single-variable warnings."
allowed-tools: cli read write
---

# Anomaly Detection

When this skill is invoked, immediately take action. Use the `read` tool right now to open the file. Do not wait for further instructions.

## Step 1 — Read the file immediately
Use the `read` tool to open the CSV file specified by the user. If none specified, read `demo/sensor_data.csv`.

## Step 2 — Profile each numeric column
For every numeric column (temperature, vibration, pressure, rpm, power, etc.), compute:
- Mean
- Standard deviation
- Min and Max

## Step 3 — Apply Z-score detection
For every data row and every numeric column, compute z = (value - mean) / std_dev.
- If |z| > 3.0 → ANOMALY (strong)
- If |z| > 2.5 → WARNING

## Step 4 — Check for multi-variable anomalies
If the SAME row (same timestamp and machine_id) has anomalies in 2 or more columns simultaneously → escalate to CRITICAL.

## Step 5 — Print the full report immediately

ANOMALY DETECTION REPORT
=========================
File: [filename] | Rows analyzed: [N] | Columns: [list]

Statistical baseline per column:
  Column          Mean      Std Dev   Min      Max
  ------          ----      -------   ---      ---
  temperature_c   [X.X]     [X.X]     [X]      [X]
  vibration_mm    [X.X]     [X.X]     [X]      [X]
  [etc]

CRITICAL ANOMALIES (multiple variables spiking together):
  Timestamp           Machine  Variables flagged              Action needed
  ---------           -------  -----------------              -------------
  [timestamp]         [M-XX]   temp=[X] (z=[X]) +             INSPECT NOW
                               vibration=[X] (z=[X])

WARNING ANOMALIES (single variable):
  Timestamp           Machine  Column          Value    Z-score
  [timestamp]         [M-XX]   [column]        [X]      [X.X]

SUMMARY:
  Critical events : [N]
  Warnings        : [N]
  Clean rows      : [N] ([X]%)

RECOMMENDATIONS:
  1. [Most critical finding with specific machine and time]
  2. [Second finding]
  3. [Pattern across machines if any]