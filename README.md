# Factory Brain Agent 🏭

> *Your repository becomes your factory engineer.*

**Factory Brain** is a git-native AI agent built on the [GitAgent standard](https://github.com/open-gitagent/gitagent) that transforms raw factory CSV data into actionable engineering intelligence — detecting bottlenecks, analyzing machine downtime, flagging anomalies, and delivering a prioritized optimization roadmap.

---

## The Problem

Every factory generates gigabytes of production data every shift. Most of it sits unread in spreadsheets. Identifying a bottleneck, catching a failing machine before it breaks, or understanding why Shift 2 underperforms Shift 1 requires an experienced engineer — and they're expensive, busy, and can only be in one place at a time.

**Factory Brain puts that expertise in a git repo.**

---

## Demo

```bash
# 1. Install the tools
npm install -g gitclaw
npm install -g gitagent

# 2. Clone the repo
git clone https://github.com/YOUR_USERNAME/factory-brain-agent
cd factory-brain-agent

# 3. Set your API key
export ANTHROPIC_API_KEY="your-key-here"

# 4. Validate the agent structure
npx gitagent validate
npx gitagent info

# 5. Run the agent (gitclaw starts an interactive session)
gitclaw --dir . "Analyze demo/production_data.csv and detect bottlenecks"
gitclaw --dir . "Run downtime analysis on demo/machine_downtime_log.csv"
gitclaw --dir . "Detect anomalies in demo/sensor_data.csv"
gitclaw --dir . "Calculate OEE for demo/production_data.csv"
gitclaw --dir . "Generate full optimization roadmap from all demo CSV files"
```

### Expected output from the demo data

Running `detect-bottlenecks` on the included sample CSV will find:
- **M-07 (Forming)** is the bottleneck across all 3 shifts — avg 48s vs 26s ideal
- M-02 and M-06 are near-bottlenecks
- Fixing M-07 alone would increase line throughput by ~22%

Running `anomaly-detection` on `sensor_data.csv` will flag:
- **M-07 at 06:15–06:20**: simultaneous temperature spike (94°C) + vibration spike (11.2mm) → CRITICAL
- **M-04 at 06:25**: temperature spike (78°C) isolated → WARNING
- **M-06 at 06:25**: overtemperature (97°C) isolated → WARNING

---

## Skills

| Skill | What it does | Key output |
|-------|-------------|-----------|
| `detect-bottlenecks` | Theory of Constraints analysis on cycle times | Ranked station list, throughput impact |
| `downtime-analysis` | Classifies downtime, computes MTBF/MTTR | Per-machine reliability metrics |
| `anomaly-detection` | Z-score + IQR outlier detection | Critical/Warning anomaly table |
| `optimization-suggestions` | Synthesizes all findings into a priority roadmap | Impact/Effort ranked action plan |
| `oee-calculator` | Availability × Performance × Quality | OEE per machine per shift |
| `shift-report` | End-of-shift handover document | Markdown report for incoming team |

---

## Agent Structure

```
factory-brain-agent/
├── agent.yaml          # Manifest
├── SOUL.md             # Identity: senior industrial engineer persona
├── RULES.md            # Hard constraints: cite data, no hallucination
├── skills/
│   ├── detect-bottlenecks/SKILL.md
│   ├── downtime-analysis/SKILL.md
│   ├── anomaly-detection/SKILL.md
│   ├── optimization-suggestions/SKILL.md
│   ├── shift-report/SKILL.md
│   └── oee-calculator/SKILL.md
└── demo/
    ├── production_data.csv
    ├── machine_downtime_log.csv
    └── sensor_data.csv
```

---

## Why This Matters

Manufacturing is a $14 trillion global industry. Most factories still rely on manual inspection and spreadsheet analysis. A git-native agent that any engineer can clone, customize, and deploy — without infrastructure — is the kind of tool that gets used in the real world.

OEE (Overall Equipment Effectiveness) is the gold standard manufacturing KPI. The demo data shows a line running at ~59% OEE. World class is 85%. **Factory Brain identifies exactly why, and exactly what to fix first.**

---

## Built With

- [GitAgent standard](https://github.com/open-gitagent/gitagent)
- [gitclaw SDK](https://github.com/open-gitagent/gitclaw) — local runtime
- [clawless](https://github.com/open-gitagent/clawless) — serverless browser deploy
- Claude Sonnet (claude-sonnet-4-5-20250929)
