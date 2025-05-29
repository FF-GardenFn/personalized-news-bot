# News Bot — Quality Assurance for Evaluation  
*Version 1.5 — 2025-05-16*
>Note: This document is a work-in-progress and is subject to change.Made a standalone in anticipation of a contributor joining the project.

---

## 0 Strategic Importance  
The validity and reliability of evaluation results are fundamentally dependent on the robustness of the processes that generate them. This comprehensive quality assurance framework ensures that all metrics documented in `metrics_log.jsonl` maintain the essential characteristics of being **reproducible, unbiased, and actionable**—even within the constraints of limited engineering resources and a restricted evaluation participant pool (<10 beta participants).

---

## 1 Guiding Principles  

| Code | Principle | Solo-lab Concretisation |
|------|-----------|-------------------------|
| **R** | **Reliability** | Deterministic seeds, frozen input snapshots, replay harness. |
| **V** | **Validity** | Metrics map 1-to-1 with framework Tier-0/Tier-1 definitions. |
| **O** | **Objectivity** | Blind sample ordering, GPT-assisted second-opinion checks. |
| **C** | **Coverage** | Manual fact spot-checks. |
| **A** | **Actionability** | Every QA finding feeds a GitHub issue with "fix-by" date. |

---

## 2 Process Checklist  

| Stage | Gate | Tool / Action | Owner | Status |
|-------|------|---------------|-------|--------|
| **Pre-commit** | Unit tests ≥85% pass | `pytest --cov` | Dev | Current |
| | JSON schema lint 0 errors | `schema_validator.py` | Dev | Current |
| | p95 latency ≤30 s, cost ≤$0.05 | Dashboard alert | Dev | Manual check |
| | QA report archived | `reports/QA_YYYY-WW.md` | Dev | Ad-hoc |


---

## 3 Test-Case Governance  

1. **Snapshot Freeze Rule** — Once a frozen set (FS-ID) is published, it never changes.  
2. **Version Control** — All YAML test specs live under `tests/` and require PR review.

---

## 4 Human Evaluation Quality Assurance Protocol  

*Specialized procedures designed for implementation within a constrained evaluator resource environment consisting of the primary evaluator and occasional domain expert collaboration.*

| Step | Lean Implementation |
|------|---------------------|
| **Calibration** | Review 5 historical briefs, discuss discrepancies for 10 min. |
| **Blindness** | Sample order randomised; file names hashed. |

---

## 5 Data QA  

1. **Collection** – Automated logs write JSON lines with SHA, timestamp, build-id.  
2. **Validation script** (`metrics_validator.py`) asserts:  
   * all Tier-0 metrics present,  
   * no nulls,  
   * numeric ranges sane (e.g., 0 ≤ AIR ≤ 1).  
3. **Storage** – Append-only `07_Appendix/metrics_log.jsonl`; daily off-device backup.

---

## 6 Reporting QA  

| Rule | Enforcement |
|------|-------------|
| Exec-summary first | Markdown template auto-generates headings. |
| Strengths *and* weaknesses | Lint fails if "Weakness" section empty. |
| Link to raw artifacts | Each number hyperlinks to log slice in repo. |
| Action items | Minimum one GitHub issue per weakness. |

---

## 7 QA Health Metrics  

| Metric | Target | Note | Status |
|--------|--------|------|--------|
| **Schema errors / 100 briefs** | 0 | Hard-fail CI | Current |
| **Manual Fact Pass %** | ≥ 0.85 | Claims checked as needed | Current (ad-hoc) |
| **Evaluation Completion %** | ≥ 95% | Planned vs. done items | Current |
| **Actionability of QA reports** | ≥ 8 / 10 | Dev self-rating | Current |

---
## 8 Continuous Improvement Loop  

> **Target Process:** The following represents the ideal continuous improvement cycle, to be fully implemented with increased team capacity.

1. **Periodic Review³** Review QA metrics; identify process bottlenecks.  
2. **Improvement Implementation³** Implement automation or guideline tweaks.  
3. **Verification³** Measure same QA metrics after changes; confirm improvements.  
4. **Documentation³** Update documentation; increment minor version.

³ Currently implemented on an ad-hoc basis as needed. Ideal Cadence: Weekly structured review and improvement cycle.

---

## 9 Role Allocation Framework  

| Functional Role | Responsibilities and Scope |
|-----------------|----------------------------|
| **Development and Quality Assurance Lead** | Primary ownership of codebase, metrics implementation, and quality assurance documentation. |
| **External Peer Reviewer** | Independent domain expert providing ad-hoc evaluation assistance for micro-review procedures as needed. |
| **Continuous Integration System** | Automated GitHub Actions workflow executing test suites and scheduled evaluation tasks. |
