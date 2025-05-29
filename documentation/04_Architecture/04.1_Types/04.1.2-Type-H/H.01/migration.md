# Migration Plan: H.01 → H.02

This document outlines the migration plan from Version H.01 to Version H.02. This plan is informed by the findings in the **[Version H.01 Analysis Report](../../../05_Analysis/05.2-Type-H/H.01/analysis.md)** and the summary in the **[Version H.01 Migration Retrospective](./README.md)**.

## 1. Executive Summary of Plan for H.02

H.02 aims to address the critical performance and functional issues identified in H.01 while preserving its strong personalization capabilities. The plan focuses on implementing shared system-level prompts, adding cross-headline synthesis, improving time-sensitivity through age-decay scoring, and fixing the Summarizer bug to ensure all news items are processed.

## 2. H.01 Current State Assessment ( Analysis Summary )

The H.01 architecture faced several significant challenges:

- **Efficiency**: High latency (~50s) and token usage (~8350 tokens) exceeding targets
- **Output Quality**: Room for improvement in actionable insights and factual accuracy
- **Agent Coordination**: Limited information sharing between agents
- **Summarizer Completeness**: Critical bug causing failure to process all news items as required

## 3. Migration Rationale & Objectives for Version H.02

The migration to H.02 is needed to address the fundamental issues identified in H.01:

- **Objective 1:** Improve output quality by enhancing the depth and value of insights
- **Objective 2:** Reduce token redundancy by eliminating duplicated context across agents
- **Objective 3:** Add cross-item analysis to enable identification of patterns across news items
- **Objective 4:** Improve time-sensitivity to ensure recent news receives appropriate emphasis
- **Objective 5:** Fix the Summarizer bug to ensure all news items are processed

## 4. Proposed Architectural & Key Implementation Changes for Version H.02

### 1. Shared Prompts Module
- Centralize system-level and agent-specific prompt templates in shared_prompts.py
- Reduce token redundancy and improve maintainability
- Standardize formatting instructions across agents

### 2. Age-Decay Scoring
- Implement adjusted_score = raw_score * exp(-lambda * age_days) with domain-specific decay rates
- Move calculation from LLM to Python code for reliability
- Ensure news importance reflects publication age appropriately

### 3. Cross-Headline Synthesis Agent
- Add a dedicated synthesis module to identify patterns across multiple headlines
- Enable discovery of non-obvious connections between news items
- Create foundation for strategic insights based on cross-item analysis

### 4. Orchestrator Enhancements
- Refactor orchestrator_h2.py to use shared prompts and the synthesis module
- Implement multi-layered JSON validation and recovery mechanisms
- Add explicit response format instructions to improve output consistency

### 5. Bug Fixes and Improvements
- Fix undefined SYSTEM_PROMPT reference in summarise.py
- Correct agent type naming from "synthesizer" to "synthesis"
- Match news items by unique ID instead of headline for robust enhancement
- Improve error handling and logging for more reliable benchmark execution

### 6. Optimization Strategies
- **Conditional Processing**: Implement relevance-based filtering
  - Process only items with relevance ≥ 3
  - Skip fact-checking for items with relevance < 2
- **Dynamic Depth Analysis**: Vary processing depth based on relevance
  - For relevance 5 items: Full analysis
  - For relevance 3-4 items: Brief summary
  - For relevance 1-2 items: Minimal processing
- **Shared Context Management**: Extract common elements to system messages
  - Move recipient profile to shared system prompt
  - Standardize formatting instructions across agents
  - Implement token budget per agent

## 5. Target Performance & Success Criteria for Version H.02

| Metric            | H.01 Baseline | Target for H.02 | Improvement Goal | Tier/Notes              |
|-------------------|---------------|-----------------|------------------|-------------------------|
| Tok/brief         | ~8,350 tokens | ≤ 7,000 tokens  | ~25% reduction   | Tier 1 (Hard Fail)      |
| p95 Latency       | ~50s          | ≤ 30s           | ~40% reduction   | Tier 0 (Hard Fail)      |
| Cost $/brief      | ~$0.045       | ≤ $0.05         | Maintain         | Tier 0                  |
| Items Processed   | 40-50%        | 100%            | Fix critical bug | Functional Requirement  |
| Relevance@3       | 4.33-4.67     | ≥ 4.0           | Maintain         | Tier 1                  |
| AIR               | 0.6-1.0       | ≥ 0.25          | Maintain         | Tier 0                  |
| Format Compliance | 0 errors      | 0 errors        | Maintain         | Tier 3                  |

## 6. Migration Strategy & Implementation Roadmap for H.02

1. **Foundation Repair Phase**
   - Fix the Summarizer bug to ensure all news items are processed
   - Implement shared prompts module to reduce token redundancy
   - Add age-decay scoring for time-sensitive news prioritization

2. **Feature Implementation Phase**
   - Develop and integrate the Cross-Headline Synthesis Agent
   - Enhance the orchestrator to use shared prompts and the synthesis module
   - Implement conditional processing and dynamic depth analysis

3. **Optimization Phase**
   - Fine-tune prompts for token efficiency
   - Implement token budget per agent
   - Optimize JSON validation and recovery mechanisms

## 7. Expected Benefits of Version H.02

- **Reduced Token Usage:** 8,300 → ~6,200 tokens (-25%) through shared prompts
- **Improved Latency:** 50s → ~35s (-30%) through optimized processing
- **Complete Coverage:** Fix Summarizer bug to process all news items
- **Enhanced Insights:** Cross-headline synthesis identifies meaningful patterns
- **Maintained Personalization:** Preserve quality while improving efficiency

## 8. Potential Risks & Mitigation for H.02 Development

- **Risk 1:** Shared prompts may reduce personalization quality
  - **Mitigation:** Carefully balance shared and agent-specific prompts, with extensive testing
- **Risk 2:** Age-decay scoring may inappropriately downgrade important older news
  - **Mitigation:** Calibrate decay rates by domain and test with diverse news ages
- **Risk 3:** Conditional processing may miss important low-relevance items
  - **Mitigation:** Implement override mechanisms for items with specific keywords or sources
