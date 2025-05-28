# Lessons from Multi-Agent Prompt Engineering: Why Architecture Beats Prompts

## Executive Summary

This document provides a comprehensive analysis of the Type-H model evolution, our multi-agent news processing system designed to deliver personalized, actionable content. Through five iterations (H.1 through H.2.7.3), we have refined our prompting strategies to enhance agent performance, improve coordination, and increase output reliability. While we achieved significant advances in personalization quality and agent specialization, persistent challenges with JSON formatting and pipeline integrity demonstrate important limitations of prompt engineering in complex multi-agent systems. This analysis offers critical insights for future development.

⚠️ **CRITICAL FINDINGS:**
- NO version achieved core targets (30s latency, 7k tokens, $0.05 cost)
- JSON compliance got WORSE over time (H.1: Low errors → H2.7.3: Very High errors)
- H2.7 can generate convincing fiction from empty inputs (major safety risk)
- Despite 5 iterations, coverage never exceeded 70%

## Introduction

The Type-H system represents our exploration of multi-agent architectures for news processing and summarization. Each version introduced targeted improvements to prompting strategies, agent roles, and system architecture. This document analyzes the effectiveness of these techniques through empirical evaluation of system performance across multiple dimensions.

## Quick Navigation
- [Performance Dashboard](#performance-metrics) - See all metrics at a glance
- [What Worked](#successes) - Personalization, resilience
- [What Failed](#persistent-challenges) - JSON, pipeline, efficiency
- [Key Lessons](#conclusion) - Why prompt engineering alone isn't enough

## Evolution Timeline

### H.1: Multi-Agent Prototype (Baseline)

Our initial prototype established the foundation for a multi-agent approach with four specialized agents working in sequence:

- **Categorizer Agent**: Classified news by topic and assigned importance/relevance scores
- **Fact-Checker Agent**: Verified factual accuracy and credibility
- **Recommender Agent**: Suggested related topics based on recipient interests
- **Summarizer Agent**: Created personalized news briefings

**Key Limitations:**
- Basic role-specific prompts with minimal guidance
- Duplicated recipient context in each prompt (token inefficiency)
- Limited structure and formatting instructions
- No chain-of-thought or few-shot examples
- Minimal error handling for malformed outputs
- Critical functional bug in Summarizer: processed only 50% of news items

### H.2: Time-Sensitive & Synthesis

Version H.2 introduced several key improvements:

- Added shared system-level prompt to reduce token redundancy
- Improved structure and clarity in agent instructions
- Added explicit output format instructions
- Introduced basic chain-of-thought prompting
- Added Cross-Headline Synthesis Agent with specialized prompts

**Key Issues:**
- Personalization failure with placeholder "{recipient_short}" not replaced
- Introduced datetime calculation errors
- Pipeline integrity issues affecting downstream agents

### H.2.5: Market Data Integration

Version H.2.5 focused on enhancing financial analysis capabilities:

- Added market data context to prompts
- Enhanced chain-of-thought prompting for financial analysis
- Introduced few-shot examples to guide model responses
- Implemented more efficient model selection (gpt-4o-mini for Summarizer)

**Critical Failure:**
- Severe upstream data processing issues (categorization, fact-checking)
- JSON formatting and validation failures
- Failure to pass structured data between agents
- "Zombie Pipeline Problem": Catastrophic failures produced seemingly successful outputs, with the Summarizer generating coherent briefings from entirely synthetic fallback data
- Dangerous precedent of invisible degradation where end users received professional-looking briefings generated from fabricated analysis

### H.2.7: Full Agent Swarm

Version H.2.7 expanded the system's capabilities with:

- Introduced H3H4Summarizer for action-trigger-driven briefings
- Added function-calling prompts for external data retrieval
- Enhanced few-shot examples with more diverse cases
- Improved chain-of-thought prompting with explicit reasoning steps
- Added explicit constraints and guidance for outputs

**Critical Issues:**
- Pervasive JSON formatting errors:
  - Array syntax errors: Using curly braces `{}` instead of square brackets `[]` for array structures
  - Malformed object keys: Containing escaped quotes and nested JSON, creating invalid structures
  - Object formatting: Incorrectly formatted as key-value pairs with empty objects as values
- System parsing failures and occasional hallucinations
- Agent coordination challenges and inconsistent function-calling
- Critical discovery of "autonomous fiction generation" risk: When given empty inputs, H3H4Summarizer generated specific GitHub repos, market recommendations, and research suggestions that didn't exist

🚨 **SAFETY RISK DISCOVERED IN H2.7**
When given empty inputs, the system generated:
- Fictional GitHub repositories
- Non-existent research papers
- Fabricated market recommendations
This represents active misinformation generation, not just failure.

### H.2.7.3: Modular Refactoring

Our latest version represents a significant refinement:

- Added Linkage Agent to identify connections between news items
- Added ArXivSearcher Agent for academic paper integration
- Implemented templating system for consistent prompt generation
- Enhanced JSON enforcement with explicit formatting instructions ("CRITICAL OUTPUT FORMAT REQUIREMENTS")
- Added robust error handling and recovery mechanisms
- Redesigned final summarizer (SummarizerLogic) with improved resilience

**Persistent Challenges:**
- Despite explicit "CRITICAL OUTPUT FORMAT REQUIREMENTS," multiple agents still failed to adhere to the required JSON format:
  - Categorizer produced a single JSON object with stringified JSON objects as keys
  - FactChecker, Recommender, and ArXivSearcher produced single objects instead of arrays
  - Only Synthesizer and Linkage agents correctly produced JSON arrays
- Pipeline integrity issues remained unresolved, with SummarizerLogic receiving "Not available or error in previous step" for ALL upstream analytical inputs
- Token usage (14,946) and latency (55.11s) significantly exceeded targets
- Despite these failures, SummarizerLogic demonstrated remarkable resilience in producing high-quality, personalized briefings with no structured input from upstream agents

## Performance Metrics

The evolution of our prompt engineering techniques produced varying impacts on system performance across multiple dimensions:

### Efficiency Metrics

| Metric | H.1 | H.2 | H.2.5* | H.2.7<sup>1</sup> | H.2.7.3 |
|--------|-----|-----|-------|-------------------|---------|
| **Tokens / brief** | 8,308-8,407 | 7,441-7,619 | 4,421 (P1), 4,401 (P3) | N/A (Pipeline Errors) | 14,946-17,699 |
| **p95 Latency (s)** | 49.45-50.29 | 31.34-36.20 | 24.50 (P1), 18.20 (P3) | N/A (Pipeline Errors) | 55.11-76.42 |
| **Cost $/brief** | $0.044-0.046 | $0.00186-0.00193 | $0.00117 | N/A (Pipeline Errors) | $0.098-0.137 |

*Note: H.2.5 metrics represent only the Summarizer agent, not the full pipeline, due to upstream failures.  
<sup>1</sup> H.2.7 full pipeline efficiency metrics are Not Available (N/A) due to critical runtime errors (e.g., `SYSTEM_PROMPT not defined` in orchestrator logs) preventing complete, successful pipeline execution in analyzed sessions. The "% Items Processed" for H.2.7 (see below) is estimated from available component-level logs.

Our efficiency metrics reveal important trends:
- H.2 achieved a significant reduction in tokens and cost compared to H.1
- H.2.7.3 showed a substantial increase in token usage and latency, indicating severe inefficiencies in its complex pipeline
- Latency improved from H.1 to H.2 but significantly regressed in H.2.7.3
- All versions for which full pipeline data was available, except H.2.5 (Summarizer-only), failed to meet our 30-second latency target

### Format Compliance & Pipeline Integrity

| Metric | H.1 | H.2 | H.2.5 | H.2.7 | H.2.7.3 |
|--------|-----|-----|-------|-------|---------|
| **Internal Schema Errors**<sup>2</sup> | Low (P) | Moderate (P) | High (F) | Very High (Upstream Failures, F) | Very High (Upstream Failures, F) |
| **Pipeline Integrity** | Partial (F) | Compromised (F) | Critical Failure (F) | Critical Failure (F) | Critical Failure (F) |
| **% Items Processed (Summarizer)** | 40-50% | 70% | 50% | 40-50% | 40-50% |

<sup>2</sup> "Internal Schema Errors" refers to the failure of upstream analytical agents to produce valid, structured JSON for downstream consumption, or downstream agents failing to parse malformed inputs. 'P' denotes partial success/issues, 'F' denotes failure. H.1 had a reported ~13% internal JSON error rate. H.2 had data integrity issues like datetime errors.

Format compliance and pipeline integrity show a concerning trend, especially from H.2.5 onwards:
- While H.1's final output was compliant, it had internal JSON processing issues and a critical Summarizer bug limiting item processing to 40-50%.
- H.2 introduced new data integrity problems (e.g., datetime errors) that compromised downstream processing.
- H.2.5, H.2.7, and H.2.7.3 all suffered from critical pipeline failures where upstream agents failed to provide valid structured data to downstream components.
- Despite increasingly strict formatting instructions in prompts for later versions (H.2.7, H.2.7.3), severe internal JSON generation and parsing issues persisted, often leading to error placeholders being passed to the final Summarizer.
- The percentage of news items ultimately processed by the final Summarizer remained consistently below the 100% target across most versions, primarily due to either explicit bugs (H.1) or as a consequence of coping with upstream data failures in later versions.

### Content Quality & Personalization

| Aspect | H.1 | H.2 | H.2.5 | H.2.7 (Summarizer)<sup>3</sup> | H.2.7.3 (Summarizer)<sup>3</sup> |
|--------|-----|-----|-------|--------------------------|------------------------------|
| **Personalization Quality** | Good | Poor | Moderate (GIGO) | Strong | Excellent |
| **Fact-Checking Quality** | Nuanced | Less Nuanced | Default (GIGO) | Variable (GIGO) | Variable (GIGO) |
| **Actionable Content** | Moderate | Moderate | Moderate | Strong | Strong |
| **Market Data Integration** | None | None | Partial (Summarizer) | Potential (Generated) | Partial (Summarizer) |

<sup>3</sup> Quality aspects for H.2.7 and H.2.7.3 are primarily based on the final Summarizer agent's output, which often operated with minimal or errored upstream analytical input (GIGO - Garbage In, Garbage Out).

Content quality and personalization show more positive evolution, particularly in the capabilities of the final summarization stages:
- Personalization quality, after a dip in H.2 due to placeholder errors, improved significantly by H.2.7 and H.2.7.3 at the Summarizer level, even with flawed inputs
- Fact-checking quality peaked in H.1; later versions were hampered by upstream failures or less nuanced component performance
- H.2.7 and H.2.7.3 demonstrated stronger actionable content through explicit "Action Triggers" in their summarizer designs
- Market data integration was introduced in H.2.5 and showed potential, though its full integration with news items remained a challenge

### Agent Specialization & Coordination

| Version | Agents | Key Features | Coordination Quality |
|---------|--------|-------------|----------------------|
| **H.1** | 4 | Basic role-specific prompts | Sequential           |
| **H.2** | 5 | Added Synthesis Agent | parallel             |
| **H.2.5** | 4 | Market data integration | Failed coordination  |
| **H.2.7** | 5 | Function-calling, H3H4Summarizer | Failed coordination  |
| **H.2.7.3** | 7 | Added Linkage, ArXivSearcher | Failed coordination  |

Agent specialization increased consistently:
- From 4 basic agents in H.1 to 7 specialized agents in H.2.7.3
- Each version introduced more sophisticated agent roles and capabilities
- However, coordination between agents remained a persistent challenge
- H.2.5, H.2.7, and H.2.7.3 all suffered from critical pipeline failures

## The Role and Limits of Prompt Engineering in Type-H

Throughout the Type-H iterations, a significant focus was placed on advancing prompt engineering methodologies. Efforts included increasingly sophisticated agent role definitions, structured reasoning directives, few-shot examples, output format enforcement, and persona-based prompting. While these techniques yielded noticeable improvements in certain areas, such as the personalization quality achieved by the resilient Summarizer components even with compromised inputs (as detailed in individual version analyses), they proved insufficient to overcome systemic architectural challenges.

Notably, despite increasingly stringent prompt-based instructions for JSON formatting (see example below showing H.2.7 vs H.2.7.3 prompt changes), widespread compliance issues persisted and, in some iterations like H.2.7.3, affected a majority of analytical agents. This underscored a core learning: prompt engineering, while crucial and promising, cannot single-handedly ensure reliability or solve deep-seated architectural problems in complex multi-agent systems.Indeed, a recurring observation, was that while prompt engineering could significantly strengthen  individual agent performance, such component-level gains did not necessarily lead to overall system improvement if foundational architectural issues remained unaddressed

### JSON Formatting Evolution Example

#### H.2.7 (Before)
**Prompt:**
```
Output your analysis in JSON format with the following structure:
Example format:
[
  {
    "pattern": "AI Regulation Acceleration",
    "explanation": "Multiple headlines show accelerating AI regulations",
    "headlines": [1, 3, 5],
    "strategic_implication": "Compliance-first companies gain advantage Q3–Q4"
  },
  {
    "pattern": "Supply Chain Disruption",
    "explanation": "Ongoing logistics challenges in Asian markets",
    "headlines": [2, 7],
    "strategic_implication": "Inventory buffers needed through Q3 2025"
  }
]
```

#### H.2.7.3 (After)
**Prompt:**
```
Output a JSON array containing objects—no explanations, no table formats, no column headers.
CRITICAL: Your response MUST start with '[' and end with ']' to be valid JSON.
CRITICAL: Every object must be wrapped with '{' and '}' and properly nested in the array.

CRITICAL OUTPUT FORMAT REQUIREMENTS:
1. Your entire response MUST be a single, valid JSON array.
2. The array should start with '[' and end with ']'.
3. Each element in the array must be a valid JSON object.
4. Use only double quotes (") for all keys and string values.
5. Do not include any explanations or text outside the JSON.
6. Ensure no trailing commas in objects or arrays.
```
**Response:**
```
[
  {
    "id": 1,
    "category": "Defence",
    "importance": 4.0,
    "relevance": 3.5,
    "why": "Impacts US foreign policy and international defense relations"
  },
  ...
]
```
Despite these explicit instructions, 4 out of 7 agents in H.2.7.3 still produced malformed JSON, demonstrating that even the most carefully crafted prompts cannot guarantee consistent system behavior in complex architectures.

For a comprehensive review of the specific prompt engineering techniques explored and their detailed evolution, please refer to the [Prompt Engineering Case Study](../../../03_Prompt_Engineering/03.3_Case_Studies/prompt_engineering_case_study.md).

## Key Findings and Lessons Learned

### Successes

1. **Personalization Quality**
   - Improved dramatically from H.1 to H.2.7.3
   - H.2.7.3 achieved excellent personalization despite pipeline failures
   - Demonstrates the value of strong persona-based prompting

2. **Agent Specialization**
   - Successfully expanded from 4 basic agents to 7 specialized agents
   - Enhanced capabilities for specific analytical tasks
   - The H3H4Summarizer showed particular value for action-oriented content

### Persistent Challenges

1. **JSON Format Compliance**
   - Despite increasingly strict instructions, JSON formatting issues persisted and worsened
   - Explicit formatting requirements proved insufficient without system-level validation
   - Highlights a fundamental limitation of prompt-only enforcement

2. **Pipeline Integrity**
   - Agent coordination remained a critical challenge throughout all versions
   - Data flow between agents frequently broke down
   - Demonstrates the fragility of complex multi-agent chains
   - Later versions demonstrated remarkable ability to produce coherent outputs despite compromised inputs

3. **Efficiency Metrics**
   - Token usage, latency, and cost fluctuated significantly
   - No version met all efficiency targets
   - Reveals tension between quality/complexity and performance

### Key Insights for Future Development

1. **Prompt Engineering Tradeoffs**
   - Improvements in one area (e.g., personalization) often came at the expense of others (e.g., efficiency, reliability)
   - need for careful balancing of competing objectives

2. **Resilient Design Principles**
   - High-quality prompts can produce valuable outputs even with severely compromised inputs
   - Focus on graceful degradation rather than perfect coordination

3. **Format Enforcement Limitations**
   - Relying solely on prompts for formatting is insufficient
   - System-level validation and error handling mechanisms are essential
   - May require programmatic enforcement rather than prompt-based

4. **Value of Specialization**
   - Specialized agents showed clear advantages for specific tasks
   - The H3H4Summarizer demonstrated particular value for action-oriented content
   - focused, single-purpose agents may be more reliable than complex chains


## Conclusion

**CORE LESSON: Prompt engineering cannot solve system design problems.**
- JSON formatting got worse despite stricter prompts
- Pipeline failures increased with complexity
- Efficiency degraded as we added features
Success requires fixing architecture, not prompts.

The Type-H evolution illustrates that building effective multi-agent systems requires a holistic approach that balances prompt engineering with robust system design, error handling, and performance optimization. Future work should focus on addressing the persistent challenges identified in this analysis while building on the successes achieved in personalization and agent specialization.

For detailed analysis of each version, please refer to the individual analysis files:
- [H.1 Analysis](H.1/analysis.md): Multi-Agent Prototype
- [H.2 Analysis](H.2/analysis.md): Time-Sensitive & Synthesis
- [H.2.5 Analysis](H.2.5/analysis.md): Market Data Integration Prototype
- [H.2.7 Analysis](H.2.7/analysis.md): Multi-Agent System Evolution & Format Robustness
- [H.2.7.3 Analysis](H.2.7.3/analysis.md): Modular Refactoring
