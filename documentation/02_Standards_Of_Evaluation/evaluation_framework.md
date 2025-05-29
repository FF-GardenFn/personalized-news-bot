# News Bot — Evaluation Framework  
*Version 1.5 (Last updated 2025-05-16)*

---

## 1 Purpose & Scope
This document defines **how every News Bot release is measured, compared, and accepted**.  
It establishes the evaluation framework exclusively; test-data curation and prompt engineering methodologies are documented separately.

---

## 2 Design Principles  

| # | Principle | Practical Consequence |
|---|-----------|-----------------------|
| **P1** | **Quantifiable over anecdotal** | Every headline score, token count, and fact-check result is persisted to disk; manual ratings are numeric (1–5). |
| **P2** | **Re-playable harness** | All versions are run on *identical frozen input snapshots* so metrics are paired and directly comparable. |
| **P3** | **Resource-aware** | Latency and token cost carry equal weight with quality—reflecting computational and financial efficiency considerations. |
| **P4** | **Lean but professional** | Optimized for efficiency without sacrificing rigor; evaluations are designed to complete on a single development environment within a reasonable timeframe rather than requiring extensive A/B testing or resource-intensive dashboards. |
| **P5** | **Single North-Star** | *Actionable Insight Rate (AIR)* anchors decision-making; other metrics diagnose why AIR moved. |

---

## 3 Evaluation Dimensions & Metrics  

| Dimension | Metric (symbol) | Definition | Data Source |
|-----------|-----------------|------------|-------------|
| **Utility** | **AIR** |  (Actions taken¹ ∕ Items delivered) | Post-brief Google Form |
| **Factual Integrity** | *FactScore* | Mean of claim-level truth scores from automated check ⇢ manual spot-check weighting | `fact_checker.log` + manual JSON |
| **Prioritisation** | *Relevance@3* | Avg. recipient rating (1–5) of top-3 items | Google Form |
| **Insight Novelty** | *EmbΔ* | Mean cosine distance to previous-30-day centroid | `vector_store_service` |
| **Efficiency** | *Tok/brief* | Total tokens ≈ input + output | LLM billing logs |
|  | *p95 Latency* | 95th-percentile wall-clock per brief (s) | Orchestrator async timer |
| **Robustness** | *Chaos Catch Rate*² | (Mutated articles flagged ∕ total injected) | Chaos Monkey harness |
| **Format Compliance** | *Schema Errors* | JSON‐schema violations per 100 briefs | Output validator |
| **Maintainability** | *Cov%* | Unit-test coverage (pytest –-cov) | CI |

¹ *Action* = clicked link, opened repo, placed paper in reading-list, etc.—collected via a two-question form.
² Planned for future implementation when core system stability permits robust adversarial testing (see Section 5.2).

---

## 4 Evaluation Data Sets  

### 4.1 Frozen Input Snapshots  
| ID | Span | Size | Stored at |
|----|------|------|-----------|
| **FS-2025-W19** | 2025-05-05 → 2025-05-11 | 1,147 news/papers | `07_Appendix/FS-2025-W19/` |
| **FS-2025-W20** | 2025-05-12 → 2025-05-16 |  793 items | `07_Appendix/FS-2025-W20/` |

Snapshots include raw JSON + patent/paper PDFs.  
> **Rule**: A new version is always replayed on *all* frozen sets; no changes to snapshots after publication.

### 4.2 Live Evaluation Cohort  
A select group of 5-6 technically proficient beta participants i.e. Friends. After each briefing feedback is collected either through email or other messaging platform. A screenshot of the output isn provided to  build identification and timestamp data for structured feedback collection.

> **Note:** As we go forward and stablize core we aim to have briefing automatically generates a Google Form as the intended collection method.

---

## 5 Methodology  

### 5.1 Offline Replay Harness  
```bash
python run_replay.py \
  --snapshot FS-2025-W19 \
  --build_id H.02.7.3 \
  --out metrics/H.02.7.3_FS-2025-W19.json
```

The harness:
1. Normalises seeds & random order to ensure determinism.
2. Captures per-agent logs (tokens, latency, cost).
3. Writes a consolidated metrics JSON line per brief.

### 5.2 Adversarial Data Integrity Testing
(This functionality is planned for future implementation. See ROADMAP.md for details on Chaos Monkey Integration.)

---

## 6 Scoring & Acceptance Gate

| Tier | Metric | Target | Hard Fail? |
|------|--------|--------|------------|
| 0 | AIR | ≥ 0.25 | Yes |
| 1 | FactScore | ≥ 0.85 | Yes |
|  | p95 Latency | ≤ 30 s | Yes |
|  | Tok/brief | ≤ 7,000 | Yes |
| 2 | Relevance@3 | ≥ 4.0 | Soft |
|  | EmbΔ | ≥ 0.05 | Soft |
|  | Chaos Catch Rate² | ≥ 0.90 | Soft |
| 3 | Schema Errors | = 0 | Soft |
|  | Cov% | ≥ 85% | Informational |

Hard Fail ⇒ block merge or roll back; Soft ⇒ create ticket, fix within next sprint.

---

## 7 Evaluation Cadence

> **Note:** Current evaluation activities are primarily event-driven (e.g., post-major feature release or bug-fix validation). The 'On commit' cadence described below is in practice. For planned expansions to nightly and weekly automated evaluations, please see the project ROADMAP.md.

| When | What |
|------|------|
| On commit | Unit tests, schema lint, micro-metrics on 5-item mini-set (<60 s). |

---

## 8 Metric Storage 

All metric JSONL files append into 07_Appendix/metrics_log.jsonl.Nevertheless for security purposes they are not publicly available. 
.

---

> *"Measure what costs money or changes behaviour—everything else is commentary."*
> 
> **— The Evaluator's Oath**
