# News Bot — Development Roadmap

This document outlines significant planned enhancements to the News Bot system, with a focus on evaluation and quality assurance improvements. For each item, we provide context on its rationale, planned approach, and dependencies.

## Evaluation Framework Enhancements

### 1. Chaos Monkey Integration

**Description:** Implement systematic adversarial testing by deliberately introducing controlled perturbations to data items during replay sessions.

**Rationale:** Adversarial testing is critical for ensuring system robustness and detecting subtle failure modes that might not be apparent in standard testing.

**Planned Approach:**
- Develop a `chaos_monkey.py` module that can inject entity substitutions and numerical value alterations into 5% of test data
- Implement detection mechanisms to measure the system's ability to identify these intentional anomalies
- Establish the Chaos Catch Rate metric (target: ≥ 0.90) as a critical quality gate
- Schedule quarterly red-team sessions to complement automated testing

**Dependencies:**
- Core system stability must be achieved first
- Requires consistent JSON schema adherence across all agents

**Target Timeline:** H.03.0 release
> **Note:** This feature will be planned as soon as the core architecture is more stable. My estimation is that this will be in H.03.0.

### 2. Enhanced Statistical Methods for AIR

**Description:** Implement statistical shrinkage for small-n metrics, particularly for Actionable Insight Rate (AIR).

**Rationale:** The current AIR calculation can be volatile with small sample sizes, leading to potentially misleading conclusions.

**Planned Approach:**
- Implement β-binomial model for AIR to account for small sample sizes
- Add confidence intervals to AIR reporting
- Develop visualization tools to show AIR trends over time with appropriate uncertainty bands

**Dependencies:**
- Requires consistent feedback collection via Google Forms
- Statistical libraries integration

**Target Timeline:** H.02.8 release

## Automation & Process Improvements

### 3. Automated Evaluation Cadence

**Description:** Implement automated nightly and weekly evaluation processes.

**Rationale:** Regular, automated evaluation is essential for catching regressions early and maintaining system quality.

**Planned Approach:**
- Set up CI/CD pipeline for nightly replay on latest crawl
- Implement automated metrics collection and reporting
- Develop Slack integration for daily and weekly summaries
- Implement the following evaluation cadence:
  - **Nightly**: Full replay on latest snapshot with metrics in Slack channel
  - **Weekly (Friday)**: Expert review + friend feedback analysis with metrics report + action items
  - **Monthly**: Comprehensive trend analysis with roadmap adjustments

**Dependencies:**
- Additional contributor support
- Stable replay harness

**Target Timeline:** With contributor onboarding

### 4. Structured Expert Micro-Review Protocol

**Description:** Formalize and automate the expert micro-review process.

**Rationale:** Consistent, structured expert review is crucial for detecting subtle quality issues that automated metrics might miss. Weekly one-hour evaluation sessions would provide high-fidelity detection of factual inaccuracies and content staleness that may not be captured by automated evaluation metrics.

**Planned Approach:**
- Develop templates and guidelines for expert reviewers
- Implement weekly structured sessions with domain experts
- Create a database for storing and analyzing review results

**Dependencies:**
- Additional contributor support
- Standardized review criteria

**Target Timeline:** With contributor onboarding

## Infrastructure & Tooling

### 5. Metrics Dashboard

**Description:** Develop a comprehensive metrics dashboard for visualizing system performance.

**Rationale:** A unified dashboard will make it easier to track trends, identify issues, and communicate system status. Automated nightly appends and weekly trend review would enable more proactive monitoring and analysis.

**Planned Approach:**
- Implement Streamlit dashboard for visualizing metrics
- Add trend analysis and anomaly detection
- Integrate with existing metrics collection

**Dependencies:**
- Consistent metrics collection
- Streamlit infrastructure

**Target Timeline:** H.02.9 release

### 6. Feedback Collection Standardization

**Description:** Standardize all structured feedback through Google Forms.

**Rationale:** Consistent feedback collection is essential for reliable AIR and Relevance@3 calculation.

**Planned Approach:**
- Enhance Google Form integration in all briefings
- Develop incentives for users to provide feedback via the form
- Implement automated reminders for feedback collection

**Dependencies:**
- User cooperation
- Google Forms API integration

**Target Timeline:** H.02.8 release

---

### 7. Self-Play Regression Analysis

**Description:** Implement a system where a new build explains why it changed any headline rank >3 positions compared to the previous build.

**Rationale:** Understanding why the system's ranking decisions change between versions is crucial for maintaining consistency and improving the quality of recommendations.

**Planned Approach:**
- Develop a comparison mechanism between consecutive builds
- Implement an explanation generator for significant ranking changes
- Create a dashboard for visualizing and analyzing these changes

**Dependencies:**
- Stable replay harness
- Consistent metrics collection

**Target Timeline:** H.03.0 release

### 8. Token-Light FactScore Model

**Description:** Develop a custom model to cut verifier cost by 70% while maintaining accuracy.

**Rationale:** The current fact-checking process is token-intensive and expensive. A more efficient model would significantly reduce operational costs.

**Planned Approach:**
- Fine-tune a smaller model specifically for fact verification
- Optimize prompts for token efficiency
- Implement a two-stage verification process with the lightweight model as the first pass

**Dependencies:**
- Model fine-tuning infrastructure
- Benchmark dataset for fact verification

**Target Timeline:** H.03.0 release

### 9. Formalized Prompt Testing and Validation Protocol

**Description:** Implement a structured approach to A/B testing and regression testing for prompts.

**Rationale:** Systematic prompt testing ensures that changes to prompts result in measurable improvements and don't introduce regressions.

**Planned Approach:**
- Develop a standardized A/B testing methodology:
  1. Create variant prompts in `prompts/experimental/`
  2. Run both prompts on identical input sets
  3. Compare metrics side-by-side
  4. Promote to production if improvement >5% on any Tier-1 metric
- Implement prompt regression testing:
  - Ensure every prompt change passes all Tier-1 metric thresholds
  - Develop a regression test suite with "golden" test cases that verify specific capabilities

**Dependencies:**
- Stable replay harness
- Consistent metrics collection

**Target Timeline:** H.03.0 release

### 10. Automated Claim Extraction

**Description:** Develop a system for automated extraction of claims from news articles for more efficient fact-checking.

**Rationale:** Manual claim extraction is time-consuming and can be inconsistent. Automated extraction would improve the efficiency and consistency of the fact-checking process.

**Planned Approach:**
- Implement NLP techniques to identify and extract claims from news articles
- Develop a classification system to prioritize claims for verification
- Create a database of extracted claims for tracking and analysis

**Dependencies:**
- NLP libraries and models
- Training data for claim extraction

**Target Timeline:** H.03.1 release

### 11. Sentiment Analysis for Feedback

**Description:** Implement sentiment analysis of friend feedback comments to extract additional insights.

**Rationale:** Qualitative feedback contains valuable information that can be systematically analyzed to identify patterns and trends.

**Planned Approach:**
- Develop sentiment analysis models tailored to feedback comments
- Implement a classification system for feedback themes
- Create visualizations to track sentiment trends over time

**Dependencies:**
- NLP libraries and models
- Sufficient feedback data

**Target Timeline:** H.03.1 release

### 12. Correlation Analysis for Metrics

**Description:** Implement correlation analysis between metrics to identify leading indicators.

**Rationale:** Understanding the relationships between different metrics can help predict future performance and identify areas for improvement.

**Planned Approach:**
- Develop statistical models to analyze correlations between metrics
- Identify leading indicators that predict changes in key metrics
- Create dashboards to visualize metric relationships

**Dependencies:**
- Statistical analysis libraries
- Sufficient historical metric data

**Target Timeline:** H.03.2 release

### 13. Expanded Test Case Library

**Description:** Develop an expanded test case library with more edge cases.

**Rationale:** A comprehensive test case library ensures that the system can handle a wide range of inputs and scenarios.

**Planned Approach:**
- Identify and categorize edge cases from production data
- Create synthetic test cases for rare scenarios
- Develop a framework for continuously expanding the test case library

**Dependencies:**
- Production data analysis tools
- Test case generation framework

**Target Timeline:** H.03.2 release

This roadmap is subject to change based on evolving project priorities and resource availability. Items will be moved to the main development backlog as they become actionable.

## Quality Assurance Enhancements

### 17. Quarterly Test-Case Audit Process

**Description:** Implement a systematic quarterly audit process for test cases.

**Rationale:** Regular auditing of test cases ensures that the test suite remains relevant and comprehensive, covering both common scenarios and edge cases observed in production.

**Planned Approach:**
- Develop a standardized process for quarterly test case reviews
- Implement procedures to delete stale cases that are no longer relevant
- Create mechanisms to add new edge cases seen in production
- Establish documentation standards for test case management

**Dependencies:**
- Stable test case framework
- Production monitoring for edge case detection

**Target Timeline:** H.03.0 release

### 18. Advanced Human Evaluation QA Measures

**Description:** Implement advanced quality assurance measures for human evaluation processes.

**Rationale:** Ensuring the quality and consistency of human evaluations is critical for maintaining the reliability of metrics that depend on human judgment.

**Planned Approach:**
- Implement GPT-4o verification for claims marked as "True" by human evaluators
- Establish a system to flag disagreements between human and AI evaluations
- Develop inter-rater reliability metrics (Cohen's kappa) when multiple evaluators are available
- Set a target of Cohen-κ >0.7 for acceptable inter-rater reliability

**Dependencies:**
- GPT-4o API integration
- Multiple evaluators for inter-rater metrics

**Target Timeline:** H.03.1 release

### 19. Continuous Improvement Cycle for QA Processes

**Description:** Formalize a continuous improvement cycle for quality assurance processes.

**Rationale:** A structured approach to improving QA processes ensures that evaluation methodologies evolve and improve over time.

**Planned Approach:**
- Implement weekly structured reviews of QA metrics
- Identify process bottlenecks and areas for improvement
- Implement automation or guideline tweaks based on reviews
- Measure the same QA metrics after changes to confirm improvements
- Update documentation and increment minor version numbers as appropriate

**Dependencies:**
- Additional contributor support
- Established baseline QA metrics

**Target Timeline:** With contributor onboarding

### 20. Quarterly Bias & Safety Governance Protocol

**Description:** Implement a comprehensive quarterly bias and safety audit process.

**Rationale:** Regular audits of bias and safety aspects ensure that the system maintains ethical standards and avoids harmful outputs.

**Planned Approach:**
- Conduct comprehensive Bias & Safety audits on a quarterly basis
- Develop detailed documentation for the audit process
- Publish formal summary reports for transparency purposes
- Align the evaluation cadence with established industry methodologies

**Dependencies:**
- Bias detection tools
- Safety evaluation framework

**Target Timeline:** H.03.2 release

## New Metric Development

### 14. Triangulation Divergence Metric

**Description:** Implement a metric to measure variance between multiple source perspectives.

**Rationale:** Understanding the divergence in perspectives across different sources is crucial for ensuring balanced and comprehensive news coverage.

**Planned Approach:**
- Develop a Triangulator agent that can compare perspectives across multiple sources
- Implement algorithms to quantify the variance between these perspectives
- Establish a target value (≤ 0.20) for acceptable divergence
- Integrate this metric into the regular evaluation framework

**Dependencies:**
- Triangulator agent development
- Multiple source integration

**Target Timeline:** H.03.2 release

### 15. Sentiment Neutrality Metric

**Description:** Implement a metric to measure the neutrality of tone in system outputs.

**Rationale:** Maintaining a neutral tone is essential for unbiased news reporting and ensuring that the system doesn't inadvertently introduce bias.

**Planned Approach:**
- Develop or integrate a sentiment analysis tool specifically for news content
- Implement a scale (0-1) to measure closeness to neutral tone
- Establish a target value (≥ 0.80) for acceptable neutrality
- Integrate this metric into the regular evaluation framework

**Dependencies:**
- Sentiment analysis tool development or integration
- Benchmark dataset for calibration

**Target Timeline:** H.03.1 release

### 16. Temporal Recall Metric

**Description:** Implement a metric to measure the percentage of recent items (< 24h) included in briefings.

**Rationale:** Ensuring that recent, time-sensitive news items are appropriately prioritized is crucial for the system's utility as a news briefing service.

**Planned Approach:**
- Develop mechanisms to track the timestamp of news items
- Implement algorithms to calculate the percentage of recent items included in briefings
- Establish a target value (≥ 0.80) for acceptable recall
- Integrate this metric into the regular evaluation framework as an informational metric

**Dependencies:**
- Timestamp tracking for news items
- Snapshot diff functionality

**Target Timeline:** H.03.0 release

