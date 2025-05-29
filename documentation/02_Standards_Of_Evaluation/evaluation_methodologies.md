# News Bot — Evaluation Methodologies  
*Aligned with Evaluation Framework v1.5*

> NOTE: This document outlines the methodologies used to evaluate News Bot releases, focusing on quantifiable metrics and reproducible results. It is designed for use by a single maintainer. While it is update frequently in accordance with the evlauation fraemwork chnages and actual bot performance, it is extremely important to understand that some elements of this document might have been placed in anticipation of an output, a contibutor or a future change, and thus might not be fully implemented yet in the analysis. Furhtermore, some elemnts here were written and adjusted before in anticipation of what an output might look like, and as such **might** be actually used in a slightly different manner in the analysis files. 
---

## 0 Methodological Principles  

> **"Limited sample size does not necessitate limited methodological rigor."**  
> Each evaluation methodology described herein has been deliberately selected for its capacity to generate **quantifiable, reproducible metrics** within the constraints of a single development environment and a limited evaluation cohort, while maintaining scalability as the project scope expands.
> Implementation and use of these methodologies remain within the purview of a single maintainer, thus at his discretion to adapt or modify as necessary and use when appropriate.

---

## 1 Methodology Portfolio  

| ID | Name | Core Question | Primary Metrics |
|----|------|---------------|-----------------|
| **M-1** | Offline **Replay Harness** | "How does Build X perform on an identical frozen input set versus Build Y?" | FactScore, Tok/brief, p95 Latency, Chaos Catch Rate¹ |
| **M-2** | **Expert Micro-Review** | "Are the insights factually solid and genuinely novel?" | Manual Fact Pass %, Novelty (1–5) |
| **M-3** | **Friend Feedback Loop** | "Did this briefing spark a real action?" | Actionable Insight Rate (AIR), Relevance@3 |
| **M-4** | **Longitudinal Diffing** | "What is trending up or down over weeks?" | 7-day moving averages of Tier-1 metrics |

¹ Planned for future integration to enable Chaos Catch Rate metric.

> **Rationale for Methodology Scope:** The selected four methodologies provide comprehensive coverage of behavioral patterns, quality metrics, efficiency parameters, and longitudinal trends while remaining implementable within the resource constraints of an individual maintainer.

---

## 2 M-1: Offline Replay Harness  

### 2.1 Inputs  
* **Frozen Snapshot (FS-ID)** – e.g., `FS-2025-W19`  
* **Build-ID** – Git commit SHA or semantic tag.  

### 2.2 Execution  

```bash
python run_replay.py --snapshot FS-2025-W19 \
                     --build_id H.02.7.3 \
                     --out metrics/H.02.7.3_FS-2025-W19.json
```

1. **Seed Lock-in** – PYTHONHASHSEED=0, deterministic random ordering.
2. **Per-agent instrumentation** – tokens, latency, cost.
3. **Schema Validator** – abort run if JSON invalid.

### 2.3 Outputs
* One JSON line per brief with all Tier-1 / Tier-2 metrics.
* Aggregated summary.csv auto-diffed against last stable build.

### 2.4 Why it matters

Gives paired, noise-free deltas, ideal for determining whether the new changes in prompt have an impact.

---

## 3 M-2: Expert Micro-Review Protocol

| Procedural Step | Implementation Details |
|-----------------|------------------------|
| Sample Selection | Randomized selection of 10 briefings from the most recent evaluation run. |
| Review Panel | Primary evaluator plus one domain expert colleague via video conference (30-minute structured session). |
| Evaluation Criteria | • Verification of 3 factual claims per briefing (Pass/Fail assessment)  • Quantitative assessment of "Insight Novelty" using standardized 1-5 scale |
| Documentation | Structured data capture in manual_checks/2025-W20.csv format. |

Currently, micro-reviews are conducted on an ad-hoc basis as needed.

---

## 4 M-3: Recipient Engagement Assessment Protocol

### 4.1 Implementation Methodology
* The assessment instrument includes the following structured queries:
  1. "Did you take any action based on information presented in this briefing?" (Binary Yes/No response)
  2. "Please rate the relevance of the top three items using the standardized scale (1–5)."
  3. Optional qualitative feedback field for unstructured observations.

> **Note:** Qualitative feedback is currently collected via informal channels (e.g., iMessage/text with beta users), and this input is considered alongside any available structured data. For plans to standardize feedback collection through Google Forms, please see ROADMAP.md. See also note in [Evaluation Framework Section 4.2](evaluation_framework.md#42-live-evaluation-cohort).

### 4.2 Analytical Framework
* **Actionable Insight Rate (AIR)** = Affirmative responses ÷ total response count
* **Relevance@3** = Mean relevance rating of top three items
* **Qualitative Analysis** = Systematic review of unstructured feedback to identify recurring patterns and themes

### 4.3 Strategic Significance
This methodology establishes a critical connection between quantitative technical metrics and real-world utility outcomes, which represents the fundamental purpose of the system.

---

## 5 M-4: Longitudinal Diffing

### 5.1 Process
1. **Metrics collection**⁴ - Metrics are periodically collated in `metrics_log.jsonl`.
2. **Trend analysis**⁴ - Moving averages are calculated as needed.
3. **Trend detection** - Significant changes (>10% week-over-week) are flagged for investigation.

⁴ Currently implemented manually on an as-needed basis.

### 5.2 Why it matters
Catches subtle regressions or improvements that might be missed in single-run comparisons.

---

## 7 Evaluation Frequency

| Cadence | Activity | Output |
|---------|----------|--------|
| **Per-commit** | Unit tests, schema validation | Pass/fail in CI |

---

## 8 Limitations & Mitigations

| Limitation | Mitigation |
|------------|------------|
| Small tester pool (5-6 friends) | Collect more data points per person; weight by engagement level |
| Limited adversarial testing | |
| Solo evaluator bias | Blind evaluation; second opinion from domain friend |
| Cost constraints | Focus on high-leverage metrics; sample-based evaluation |

---

> **Guiding Principle**: The objective of this evaluation framework is not to achieve theoretical measurement perfection, but rather to implement *actionable* measurement methodologies that effectively drive meaningful system improvements within the constraints of available resources.
