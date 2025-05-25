# Recommender Agent: H.1 Vs H.2

## Overview

This document highlights the evolution of the Recommender Agent between model H.1 and H.2. Here we demonstrate how prompt engineering alongside clever code organization (utilizing a system-generated prompt) significantly reduced latency and token count while maintaining the agent's output quality.

The Recommender Agent serves a critical function in the News Bot ecosystem by identifying gaps in news coverage and suggesting additional topics and sources that align with the recipient's interests and professional context. This personalized recommendation capability directly contributes to improving the Actionable Insight Rate (AIR), our primary evaluation metric.

## Version H.1: Initial Implementation

### Recommender Interaction 

In the H.1 version, the Recommender Agent used a comprehensive prompt with detailed recipient information and explicit persona guidance:

### Prompt

```text

## RECIPIENT
- **Name:** Mr. F
- **Profession:** Model Architect at Anthropic
- **Interests:** RLHF, constitutional AI, or other alignment techniques, anthropic news, AI, Machine learning algorithms, Benchmark techniques, Business, Politics, Science
- **Location:** Montreal,CA

## PERSONA GUIDANCE
As Mr. F's dedicated News Recommender, adopt a persona that demonstrates:
- Deep expertise in Model Architect at Anthropic and related fields
- Familiarity with the specific challenges and opportunities in Model Architect at Anthropic
- Understanding of how RLHF, constitutional AI, or other alignment techniques, anthropic news, AI, Machine learning algorithms, Benchmark techniques, Business, Politics, Science intersect with their professional goals
- A communication style that would resonate with a Model Architect at Anthropic
- Appropriate use of terminology, frameworks, and references relevant to their field

## CURRENT NEWS ITEMS
1. Show HN: Free, in-browser PDF editor (Hacker News)
   Add text, input boxes, pictures, signatures, delete pages, merge PDFs and password protect them. All happening in the browser, 100% free and no sign-u...

2. DuckDB is probably the most important geospatial software of the last decade (Hacker News)


 # ... (and 8 other news items with similar simple structure omitted for clarity) ...


## INSTRUCTIONS

Your task is to generate personalized news recommendations for high net-worth individuals with limited time. You need to **analyze the recipient's interests and current news items** to suggest additional topics and sources that would be valuable to them.

### RECOMMENDATION APPROACH
As the recipient's dedicated News Recommender, you must:
1. **Analyze the recipient's interests** and profession to understand their information needs.
2. **Review the current news items** to identify themes and topics that are already being covered.
3. **Identify gaps** in the current news coverage that might be relevant to the recipient.
4. **Suggest specific topics** that the recipient might be interested in but are not currently covered.
5. **Recommend reliable sources** that would provide valuable information on these topics.

### OUTPUT FORMAT
Provide your recommendations in the following JSON format:

{
  "recommended_topics": [
    {
      "topic": "Specific topic name",
      "relevance_explanation": "Brief explanation of why this topic is relevant to the recipient",
      "recommended_sources": ["Source 1", "Source 2"]
    }
  ],
  "broader_interests": ["Interest area 1", "Interest area 2"],
  "explanation": "Brief explanation of your overall recommendation strategy"
}

Ensure your recommendations are tailored to the recipient's profession and interests. Focus on providing valuable suggestions that will help the recipient stay informed about topics that matter to them.

```


### Response Output

```text

{
  "recommended_topics": [
    {
      "topic": "Advances in Constitutional AI Techniques",
      "relevance_explanation": "As a Model Architect at Anthropic, understanding the latest advancements in constitutional AI techniques will enhance your ability to align AI systems with human values effectively.",
      "recommended_sources": ["arXiv", "AI Alignment Forum"]
    },
    {
      "topic": "Latest Trends in Reinforcement Learning for AI Alignment",
      "relevance_explanation": "Given your interest in RLHF, exploring the latest trends and methodologies in reinforcement learning could inspire new approaches in your work at Anthropic.",
      "recommended_sources": ["DeepMind Research", "ICML Proceedings"]
    },
    {
      "topic": "Ethical Implications of AI in Business and Governance",
      "relevance_explanation": "As AI increasingly intersects with business and politics, understanding the ethical implications will be crucial for navigating these domains responsibly.",
      "recommended_sources": ["Harvard Business Review", "MIT Technology Review"]
    },
    {
      "topic": "Comparative Analysis of Benchmark Techniques in AI",
      "relevance_explanation": "With your focus on benchmark techniques, a comparative analysis of existing methods could provide insights into improving AI model evaluations.",
      "recommended_sources": ["NeurIPS Conference Papers", "Journal of Machine Learning Research"]
    }
  ],
  "broader_interests": ["Artificial Intelligence", "Machine Learning Algorithms", "Political Implications of AI"],
  "explanation": "The recommendations focus on emerging topics and trends in AI and ML that are directly aligned with Mr. F's professional role and interests. By suggesting reliable sources for each topic, the aim is to provide valuable, timely information that can enhance his expertise and inform his work at Anthropic."
}
```
### Metrics

- **prompt_tokens**: 1031
- **completion_tokens**: 487
- **total_tokens**: 1518
- **execution_time**: 6.65 seconds
- **retry_count**: 0
- **model**: gpt-4o-mini

## Version H.2: Streamlined Implementation

### Recommender-H2 Interaction  

In the H.2 version, the Recommender Agent used a more streamlined prompt with system-generated preprocessing and explicit ranking logic:

### Prompt

```text
## NEWS ITEMS
1. Technical analysis of the Signal clone used by Trump officials (Hacker News) (2025-05-02 23:20:59+00:00)

   URL: https://micahflee.com/tm-sgnl-the-obscure-unofficial-signal-app-mike-waltz-uses-to-text-with-trump-officials/

2. The Death of Daydreaming (Hacker News) (2025-05-05 12:22:10+00:00)

   URL: https://www.afterbabel.com/p/on-the-death-of-daydreaming

 # ... (and 8 other news items with similar simple structure omitted for clarity) ...


Input: scored headlines JSON.
Task: Suggest ≤3 *new* topics that fill coverage gaps.
Return a JSON array with objects containing these fields:
- topic: The name of the topic
- why: Brief explanation of why this topic is relevant
- sources: An array of recommended sources, ranked by accessibility

Example format:
```json
[
  {
    "topic": "Quantum Computing Applications in Finance",
    "why": "Combines recipient's interests in technology and finance",
    "sources": ["Quantum Computing Report", "Financial Times Tech Section"]
  }
]

Rank by (avg importance × relevance) of missed sub-domains.
```



### Response Output

```text
```json
[
  {
    "topic": "Regulatory Frameworks for AI",
    "why": "As AI technologies advance, understanding the legal and regulatory landscape is crucial for alignment and governance.",
    "sources": ["Harvard Law Review", "Brookings Institution", "AI Now Institute"]
  },
  {
    "topic": "Ethical Considerations in AI Development",
    "why": "Explores the ethical implications of AI technologies, important for ensuring alignment with societal values.",
    "sources": ["AI Ethics Journal", "Stanford Encyclopedia of Philosophy", "The Atlantic"]
  },
  {
    "topic": "Advancements in Explainable AI (XAI)",
    "why": "Focuses on techniques that enhance transparency and interpretability of AI systems, key for trust and alignment.",
    "sources": ["MIT Technology Review", "Towards Data Science", "Journal of Machine Learning Research"]
  }
]
```

### Metrics

- **prompt_tokens**: 1075
- **completion_tokens**: 190
- **total_tokens**: 1265
- **execution_time**: 4.05 seconds
- **retry_count**: 0
- **model**: gpt-4o-mini


## Analysis of Key Differences

The Recommender Agent's evolution from H.1 to H.2 demonstrates a strategic refinement of prompt engineering aimed at enhancing operational efficiency and output consistency while maintaining high-quality, relevant suggestions.

### Prompt Engineering Improvements

1. **Structural Changes**:
   - **H.1**: Comprehensive prompt with detailed recipient profiles, extensive persona guidance, and complex recommendation approach embedded directly in the task
   - **H.2**: Focused, modular design with streamlined instructions and system-generated preprocessing

2. **Instruction Refinement**:
   - **H.1**: General guidance on recommendation approach
   - **H.2**: Explicit constraints and ranking logic (e.g., "Suggest ≤3 *new* topics, "Rank by (avg importance × relevance) of missed sub-domains")

3. **Output Format**:
   - **H.1**: Complex nested JSON structure with multiple fields
   - **H.2**: Simpler, flatter JSON array output optimized for downstream processing

### Performance Improvements

| Metric | H.1 | H.2 | Improvement |
|--------|-----|-----|-------------|
| Total Tokens | 1,518 | 1,265 | -16.7% |
| Execution Time | 6.65s | 4.05s | -39.1% |
| Output Quality | High | High | Maintained |

### Alignment with Evaluation Framework

These improvements directly address key metrics in our [evaluation framework](../../02_Standards_Of_Evaluation/evaluation_framework.md):

1. **Efficiency (P3: Resource-aware)**:
   - Reduced token usage aligns with the Tok/brief metric (Tier 1 target: ≤ 7,000)
   - Improved execution time contributes to the p95 Latency metric (Tier 1 target: ≤ 30s)

2. **Utility (P5: Single North-Star)**:
   - Maintained high-quality recommendations support the Actionable Insight Rate (AIR) metric (Tier 0 target: ≥ 0.25)
   - Structured ranking logic enhances the Relevance@3 metric (Tier 2 target: ≥ 4.0)

3. **Format Compliance**:
   - Simplified JSON structure reduces Schema Errors (Tier 3 target: = 0)

### Qualitative Assessment

Despite the more concise output and the abstraction of direct persona directives in the task prompt, the H.2 recommendations (e.g., "Regulatory Frameworks for AI," "Ethical Considerations in AI Development") remained highly relevant and insightful for the sophisticated AI professional profile. This demonstrates that the refined prompting and reliance on structured input/ranking logic successfully preserved core recommendation quality while achieving significant efficiency gains.
