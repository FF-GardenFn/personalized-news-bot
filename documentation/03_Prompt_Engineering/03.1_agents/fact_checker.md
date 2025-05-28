# Fact Checker Agent In Action

## Overview

The Fact Checker Agent is a critical component of the News Bot system, responsible for verifying the accuracy and credibility of news items before they are presented to recipients. This document provides a detailed analysis of the agent's evolution across different system versions, with real-world examples demonstrating how prompt engineering has improved its performance.

## Agent Purpose and Function

The Fact Checker Agent serves several essential functions within the News Bot ecosystem:

1. **Source Reliability Assessment**: Evaluating the credibility of news sources
2. **Content Verification**: Identifying potential inaccuracies or misinformation
3. **Bias Detection**: Recognizing and flagging potential biases in reporting
4. **Credibility Scoring**: Providing standardized ratings of information reliability
5. **Context Addition**: Supplying missing context that may affect interpretation

## Evolution of Prompting Strategies

### H.01 Version (Initial Implementation)

In the earliest version of the News Bot system, the Fact Checker Agent used basic prompting with minimal guidance:

---
## FactChecker Interaction 

### Prompt

```text

## RECIPIENT
- **Name:** omitted 
- **Profession:** Mechanical Engineer
- **Interests:** Mechanical Engineering, Computer Science, AI, Defence and Military Technology, Politics, Geopolitics, Finance, Market News
- **Location:** Montreal,CA

## PERSONA GUIDANCE
As Mr.B's dedicated News Verifier, adopt a persona that demonstrates:
- Deep expertise in Mechanical Engineer and related fields
- Familiarity with the specific challenges and opportunities in Mechanical Engineer
- Understanding of how Mechanical Engineering, Computer Science, AI, Defence and Military Technology, Politics, Geopolitics, Finance, Market News intersect with their professional goals
- A communication style that would resonate with a Mechanical Engineer
- Appropriate use of terminology, frameworks, and references relevant to their field

## NEWS ITEMS
1. Show HN: Free, in-browser PDF editor (Hacker News)
   Add text, input boxes, pictures, signatures, delete pages, merge PDFs and password protect them. All happening in the browser, 100% free and no sign-u...
   URL: https://breezepdf.com

2. DuckDB is probably the most important geospatial software of the last decade (Hacker News)

   URL: https://www.dbreunig.com/2025/05/03/duckdb-is-the-most-impactful-geospatial-software-in-a-decade.html

# ... (and 8 other news items with similar simple structure omitted for clarity) ...


## INSTRUCTIONS

Your task is to verify the accuracy of news items for high net-worth individuals with limited time. You need to **analyze each news item** for potential inaccuracies, biases, or misinformation.

### FACT-CHECKING APPROACH
As the recipient's dedicated News Verifier, you must:
1. **Analyze each news item** for factual accuracy, potential biases, and signs of misinformation.
2. **Assess the reliability** of the source based on its reputation and track record.
3. **Identify any claims** that seem exaggerated, misleading, or potentially false.
4. **Rate the credibility** of each news item on a scale of 1-5, where 5 is highly credible.
5. **Provide a brief explanation** of your assessment, highlighting any concerns or confirming the reliability.

### OUTPUT FORMAT
For each news item, provide the following information in JSON format:
```
{
  "headline": "Original headline",
  "source_reliability": 1-5,
  "content_credibility": 1-5,
  "concerns": ["List of specific concerns or potential inaccuracies, if any"],
  "explanation": "Brief explanation of your assessment"
}
```

Ensure your fact-checking is tailored to the recipient's profession and interests. Focus on providing accurate and helpful assessments that will allow the recipient to quickly identify reliable information from potential misinformation.


```

### Response

```text
```json
[
  {
    "headline": "Show HN: Free, in-browser PDF editor (Hacker News)",
    "source_reliability": 4,
    "content_credibility": 4,
    "concerns": ["Potential exaggeration of '100% free' without hidden costs"],
    "explanation": "Hacker News is generally a reliable source for tech news, especially for open-source software. The claim of '100% free' should be verified, as sometimes there are hidden features behind paywalls. Checking user reviews and the website could confirm this."
  },
  {
    "headline": "DuckDB is probably the most important geospatial software of the last decade (Hacker News)",
    "source_reliability": 4,
    "content_credibility": 3,
    "concerns": ["Subjective claim about importance", "Lack of comparative analysis"],
    "explanation": "While DuckDB has been gaining traction for its lightweight and efficient database capabilities, the claim of being the 'most important' geospatial software is subjective. A comparison with other databases like PostGIS or spatial functionalities within other DBMS would provide a more balanced view."
  },

  # ... (additional fact-checked items omitted for clarity) ...

  {
    "headline": "AI Thinking: A framework for rethinking artificial intelligence in practice (arXiv Research Articles)",
    "source_reliability": 4,
    "content_credibility": 4,
    "concerns": ["Frameworks may be speculative"],
    "explanation": "This arXiv article proposes a new framework for AI, a rapidly evolving field. While it is research-backed, such frameworks can be speculative and may not yet be widely adopted or validated in practice."
  }
]
```


The output provided basic fact-checking but lacked depth and nuance. The explanations were brief and didn't provide detailed reasoning for the ratings or concerns.

### H.02 Version (Improved Structure)

The H.02 version introduced more structured prompting with clearer instructions and persona guidance:

#### H.02 Prompt Example:
```
## FactChecker-H.02 Interaction for h@gmail.com – 2025-05-05 19:40:03

### Prompt

```text
## NEWS ITEMS
1. Technical analysis of the Signal clone used by Trump officials (Hacker News) (2025-05-02 23:20:59+00:00)

   URL: https://micahflee.com/tm-sgnl-the-obscure-unofficial-signal-app-mike-waltz-uses-to-text-with-trump-officials/

2. The Death of Daydreaming (Hacker News) (2025-05-05 12:22:10+00:00)

   URL: https://www.afterbabel.com/p/on-the-death-of-daydreaming

# ... (and several other news items omitted for clarity) ...



Task: For each headline id, output → {"id","credibility","concerns"}.
Method:
1. credibility = average of
   a) Source reputation (indexed list supplied separately),
   b) Claim verifiability (open-fact retrieval).
Scale: 0-1 float.
List concrete concerns; empty list if none.

```

### Response

```text
```json
[
  {
    "id": 1,
    "credibility": 0.6,
    "concerns": ["Source is less known", "Technical analysis may be biased"]
  },
  {
    "id": 2,
    "credibility": 0.5,
    "concerns": ["Opinion piece", "Lacks empirical data"]
  },

  # ... (additional fact-checked items omitted for clarity) ...

  {
    "id": 10,
    "credibility": 0.6,
    "concerns": ["Emerging field", "Lacks extensive empirical support"]
  }
]
```
```
```

The output showed improvement in fact-checking depth and explanation quality, with more detailed reasoning for the ratings and concerns.

# FactChecker Interaction 

### Prompt

```text


Output a JSON array containing objects—no explanations, no table formats, no column headers.
CRITICAL: Your response MUST start with '[' and end with ']' to be valid JSON.
CRITICAL: Every object must be wrapped with '{{' and '}}' and properly nested in the array.

Task: For each headline id, output an object in this format:
[
  {
    "id": 1,
    "source_reliability": 0.85,
    "content_credibility": 0.75,
    "overall_credibility": 0.80,
    "concerns": ["Potential bias", "Limited sources"],
    "market_impact": "Moderate impact on defense sector valuations",
    "political_slant": "Center-right"
  },
  {
    "id": 2,
    "source_reliability": 0.90,
    "content_credibility": 0.95,
    "overall_credibility": 0.93,
    "concerns": [],
    "market_impact": "Minimal impact on tech sector",
    "political_slant": "Neutral"
  }
]
Method:
1. source_reliability = reputation of the publisher (0-1 float)
2. content_credibility = accuracy of the claims based on fact verification (0-1 float)
3. overall_credibility = weighted average of source_reliability and content_credibility
4. concerns = specific red flags, empty list if none
5. market_impact = assessment of how this news might impact relevant market sectors
6. political_slant = political leaning of the source (Left, Center-left, Center, Center-right, Right, or Neutral)

Scale: All credibility scores are 0-1 float.
List concrete concerns; empty list if none.


CRITICAL OUTPUT FORMAT REQUIREMENTS:
1. Your entire response MUST be a single, valid JSON array.
2. The array should start with '[' and end with ']'.
3. Each element in the array must be a valid JSON object, starting with '{{' and ending with '}}'.
4. Use only double quotes (") for all keys and string values within the JSON.
5. Do not include any explanations, comments, or any text outside of this JSON array structure.
6. Ensure no trailing commas in objects or arrays.

Example of a correctly formatted JSON array response (structure varies by agent task):
[
  {"id": 1, "field_a": "value1", "field_b": 123},
  {"id": 2, "field_a": "value2", "field_b": 456}
]


## NEWS ITEMS TO FACT-CHECK:
1. US and Qatar sign defense and aviation deal as Trump says of emir: 'we just like each other' – live (The Guardian)
   URL: https://www.theguardian.com/us-news/live/2025/may/14/donald-trump-syria-middle-east-gulf-latest-us-politics-news-updates-live Age: 0.0 days

2. Joseph Nye obituary (The Guardian)
   URL: https://www.theguardian.com/us-news/2025/may/14/joseph-nye-obituary Age: 0.0 days

# ... (and 8 other news items omitted for clarity) ...

Ensure your entire response is ONLY the JSON array as described in the initial instructions.

```

### Response

```text
{
  "id": 1,
  "source_reliability": 0.85,
  "content_credibility": 0.75,
  "overall_credibility": 0.80,
  "concerns": ["Potential bias", "Limited sources"],
  "market_impact": "Moderate impact on defense sector valuations",
  "political_slant": "Center-right"
}
```

The output provided basic fact-checking but lacked depth and nuance. The explanations were brief and didn't provide detailed reasoning for the ratings or concerns.

## H.02.7.3 Version

By version H.02.7.3, the Fact Checker Agent evolved into a more sophisticated component with the ability to trinagulate ( calls the trinagulator agent ) producing therefore a detailed credibility assessments, political slant analysis, and market impact evaluations.

### Persona 1 Example 

#### Prompt:
```text
Output a JSON array containing objects—no explanations, no table formats, no column headers.
CRITICAL: Your response MUST start with '[' and end with ']' to be valid JSON.
CRITICAL: Every object must be wrapped with '{{' and '}}' and properly nested in the array.

Task: For each headline id, output an object in this format:
[
{
"id": 1,
"source_reliability": 0.85,
"content_credibility": 0.75,
"overall_credibility": 0.80,
"concerns": ["Potential bias", "Limited sources"],
"market_impact": "Moderate impact on defense sector valuations",
"political_slant": "Center-right"
},
{
"id": 2,
"source_reliability": 0.90,
"content_credibility": 0.95,
"overall_credibility": 0.93,
"concerns": [],
"market_impact": "Minimal impact on tech sector",
"political_slant": "Neutral"
}
]
Method:
1. source_reliability = reputation of the publisher (0-1 float)
2. content_credibility = accuracy of the claims based on fact verification (0-1 float)
3. overall_credibility = weighted average of source_reliability and content_credibility
4. concerns = specific red flags, empty list if none
5. market_impact = assessment of how this news might impact relevant market sectors
6. political_slant = political leaning of the source (Left, Center-left, Center, Center-right, Right, or Neutral)

Scale: All credibility scores are 0-1 float.
List concrete concerns; empty list if none.


CRITICAL OUTPUT FORMAT REQUIREMENTS:
1. Your entire response MUST be a single, valid JSON array.
2. The array should start with '[' and end with ']'.
3. Each element in the array must be a valid JSON object, starting with '{{' and ending with '}}'.
4. Use only double quotes (") for all keys and string values within the JSON.
5. Do not include any explanations, comments, or any text outside of this JSON array structure.
6. Ensure no trailing commas in objects or arrays.

Example of a correctly formatted JSON array response (structure varies by agent task):
[
{"id": 1, "field_a": "value1", "field_b": 123},
{"id": 2, "field_a": "value2", "field_b": 456}
]


## NEWS ITEMS TO FACT-CHECK:
1. US and Qatar sign defense and aviation deal as Trump says of emir: 'we just like each other' – live (The Guardian)
   URL: https://www.theguardian.com/us-news/live/2025/may/14/donald-trump-syria-middle-east-gulf-latest-us-politics-news-updates-live Age: 0.0 days

2. Joseph Nye obituary (The Guardian)
   URL: https://www.theguardian.com/us-news/2025/may/14/joseph-nye-obituary Age: 0.0 days

# ... (and 8 other news items omitted for clarity) ...

Ensure your entire response is ONLY the JSON array as described in the initial instructions.
```

#### Response:
```text
{
"id": 1,
"source_reliability": 0.85,
"content_credibility": 0.80,
"overall_credibility": 0.83,
"concerns": ["Potential bias"],
"market_impact": "Moderate impact on defense sector valuations",
"political_slant": "Center-left"
}
```

