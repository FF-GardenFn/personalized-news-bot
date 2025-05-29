# News Bot — Target Metrics & Benchmark Structure
*Aligned with Evaluation Framework v1.5 (solo-lab edition)*

> **Important Note:** This document outlines the target structure for ongoing metric tracking. For actual historical data, please refer to the version-specific Analysis Reports in the [05_Analysis/](../05_Analysis/) directory.

---

## 0 Tier Map

| Tier | Metric | Target | Hard-fail? |
|------|--------|--------|------------|
| **0** | Actionable Insight Rate **(AIR)** | ≥ 0.25 | ✅ |
|      | p95 Latency | ≤ 30 s | ✅ |
|      | Cost / brief | ≤ $0.05 | ✅ |
|      | FactScore | ≥ 0.85 | ✅ |
| **1** | Relevance@3 | ≥ 4.0 | ⚠️ |
|      | Tok/brief | ≤ 7,000 | ⚠️ |
| **2** | Embedding Novelty Δ | ≥ 0.05 | • |
|      | JSON-schema Errors / 100 | 0 | • |
|      | Unit-test Coverage | ≥ 85% | • |

*Hard-fail ⇒ block release; warning ⇒ ticket created and fixed within one sprint.*

---

## 1 Efficiency Metrics

| Metric | Definition | Target Value | Collection Method | Tier |
|--------|------------|--------------|------------------|------|
| **Tokens / brief** | Σ input + output tokens | ≤ 7,000 | LLM billing logs | 1 |
| **p95 Latency (s)** | 95th percentile wall clock | ≤ 30 s | Orchestrator async timer | 0 |
| **Cost $/brief** | Tokens × current GPT price | ≤ $0.05 | LLM billing logs | 0 |



---

## 2 Utility & Prioritisation

| Metric | Definition | Target Value | Collection Method | Tier |
|--------|------------|--------------|------------------|------|
| **AIR** | Actions taken ∕ Items delivered | ≥ 0.25 | Post-brief Google Form | 0 |
| **Relevance@3 (1-5)** | Avg. recipient rating of top-3 items | ≥ 4.0 | Google Form | 2 |

---

## 3 Factual Integrity

| Metric | Definition | Target Value | Collection Method | Tier |
|--------|------------|--------------|------------------|------|
| **FactScore** | Mean of claim-level truth scores | ≥ 0.85 | `fact_checker.log` + manual spot-check | 0 |
| **Hallucination %** | % of claims with no factual basis | ≤ 5.0% | Manual micro-review | 1 |
| **Source Attribution %** | % of claims with proper attribution | ≥ 90.0% | Manual micro-review | 2 |

---

## 4 Insight Quality

| Metric | Definition | Target Value | Collection Method | Tier |
|--------|------------|--------------|------------------|------|
| **Novelty (1-10)** | Expert rating of insight uniqueness | ≥ 7.0 | Manual micro-review | 2 |
| **Embedding Δ (1 – cosine sim)** | Mean cosine distance to previous-30-day centroid | ≥ 0.05 | `vector_store_service` | 2 |

---

## 6 Format Compliance & Reliability

| Metric | Definition | Target Value | Collection Method | Tier |
|--------|------------|--------------|------------------|------|
| **JSON-schema Errors** | JSON-schema violations per 100 briefs | 0 | Output validator | 3 |
| **Orchestrator Success %** | % of pipeline runs completing without errors | ≥ 95.0% | Orchestrator logs | 2 |

---

## 7 Change Log

| Date | Change | Reason |
|------|--------|--------|
| 2025-05-17 | Restructured document as Target Metrics & Benchmark Structure | Align with actual implementation status; refer to Analysis Reports for historical data |
| 2025-05-09 | Switched FactScore tool to GPT-4o-mini | Speed/cost optimisation |
| 2025-03-16 | Added AIR, cost cap; fixed hallucination typo; removed NDCG | Align with solo-lab evaluation plan |

---

## 8 Performance Analysis Summary

> **Conclusion:** This document outlines target metrics and benchmarks. For current performance status, please refer to the latest Analysis Reports in the [05_Analysis/](../05_Analysis/) directory.
