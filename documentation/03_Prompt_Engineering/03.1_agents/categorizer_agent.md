# Categorizer Agent In Action

## Overview

The Categorizer Agent is a fundamental component of the News Bot system, responsible for analyzing and categorizing news items based on their topic, importance, and relevance to the recipient's interests. This document provides a detailed analysis of the agent's evolution across different system versions, with real-world examples demonstrating how prompt engineering has improved its performance.

## Agent Purpose and Function

The Categorizer Agent serves several critical functions within the News Bot ecosystem:

1. **Topic Classification**: Assigning primary and secondary categories to news items
2. **Importance Assessment**: Rating the significance of news items on a standardized scale
3. **Relevance Evaluation**: Determining how closely news items align with recipient interests
4. **Prioritization**: Helping recipients focus on the most valuable information
5. **Personalization**: Tailoring categorizations to individual recipient profiles

## Evolution of Prompting Strategies

### H1 Version (Initial Implementation)

## Categorizer Interaction 

### Prompt

```text

## RECIPIENT
- **Name:** omitted 
- **Profession:** Electrical Engineer
- **Interests:** Electrical Engineering, Computer Science, AI, Defence and Military Technology, Politics, Geopolitics, Finance, Market News
- **Location:** Montreal,CA

## PERSONA GUIDANCE
As Mr.M 's dedicated News Curator, adopt a persona that demonstrates:
- Deep expertise in Electrical Engineer and related fields
- Familiarity with the specific challenges and opportunities in Electrical Engineer
- Understanding of how Electrical Engineering, Computer Science, AI, Defence and Military Technology, Politics, Geopolitics, Finance, Market News intersect with their professional goals
- A communication style that would resonate with a Electrical Engineer
- Appropriate use of terminology, frameworks, and references relevant to their field

## NEWS ITEMS
1. Show HN: Free, in-browser PDF editor (Hacker News)
   Add text, input boxes, pictures, signatures, delete pages, merge PDFs and password protect them. All happening in the browser, 100% free and no sign-u...
   URL: https://breezepdf.com

2. DuckDB is probably the most important geospatial software of the last decade (Hacker News)

   URL: https://www.dbreunig.com/2025/05/03/duckdb-is-the-most-impactful-geospatial-software-in-a-decade.html

# ... (and 8 other news items omitted for clarity) ...


## INSTRUCTIONS

Your task is to categorize news items for high net-worth individuals with limited time. You need to **analyze and categorize each news item** to help prioritize the most relevant and important information.

### CATEGORIZATION APPROACH
As the recipient's dedicated News Curator, you must:
1. **Analyze each news item** for its topic, importance, and relevance to the recipient's interests and profession.
2. **Assign a primary category** to each news item (e.g., Technology, Finance, Politics, Science, etc.).
3. **Rate the importance** of each news item on a scale of 1-5, where 5 is extremely important.
4. **Rate the relevance** to the recipient's interests and profession on a scale of 1-5, where 5 is extremely relevant.
5. **Provide a brief explanation** of why the news item is important or relevant to the recipient.

### OUTPUT FORMAT
For each news item, provide the following information in JSON format:
```
{
  "headline": "Original headline",
  "category": "Primary category",
  "importance": 1-5,
  "relevance": 1-5,
  "explanation": "Brief explanation of importance/relevance"
}
```

Ensure your categorization is tailored to the recipient's profession and interests. Focus on providing accurate and helpful categorizations that will allow the recipient to quickly identify the most important and relevant news items.


```

### Response

```text
```json
[
  {
    "headline": "Show HN: Free, in-browser PDF editor (Hacker News)",
    "category": "Technology",
    "importance": 2,
    "relevance": 3,
    "explanation": "While this tool may be useful for engineers in managing documents, it does not directly relate to the core interests of Mr. M in Electrical Engineering or AI."
  },
  {
    "headline": "DuckDB is probably the most important geospatial software of the last decade (Hacker News)",
    "category": "Technology",
    "importance": 4,
    "relevance": 4,
    "explanation": "Geospatial software can intersect with AI and data analysis, areas of interest for Mr. M, making this a relevant and important update."
  },

  # ... (additional categorized items omitted for clarity) ...

  {
    "headline": "AI Thinking: A framework for rethinking artificial intelligence in practice (arXiv Research Articles)",
    "category": "AI/Technology",
    "importance": 5,
    "relevance": 5,
    "explanation": "This framework directly addresses AI applications, which is a significant interest for Mr. M, making it highly important and relevant."
  }
]
```
## Categorizer-H2 Interaction 

### Prompt

```text
## NEWS ITEMS
1. Technical analysis of the Signal clone used by Trump officials (Hacker News) (2025-05-02 23:20:59+00:00)

   URL: https://micahflee.com/tm-sgnl-the-obscure-unofficial-signal-app-mike-waltz-uses-to-text-with-trump-officials/

2. The Death of Daydreaming (Hacker News) (2025-05-05 12:22:10+00:00)

   URL: https://www.afterbabel.com/p/on-the-death-of-daydreaming

# ... (and several other news items omitted for clarity) ...





Task: For each headline, output → {"id","category","importance","relevance","why"}.
Scoring schema:
• importance: 1 trivial ↔ 5 systemic; multiply raw impact by e^(-age_days/5).
• relevance: 1 tangential ↔ 5 directly affects Science, Nursing, Medicine, AI, Lifestyle, Intersection of AI with Nursing.
Allowed categories: Technology | Science | Defence | Politics | Markets | Other

```

### Response

```text
```json
[
  {
    "id": 1,
    "category": "Technology",
    "importance": 1.0,
    "relevance": 1,
    "why": "Trivial analysis of a communication tool unrelated to core interests."
  },
  {
    "id": 2,
    "category": "Other",
    "importance": 1.2,
    "relevance": 1,
    "why": "Tangential discussion on daydreaming, minimal relevance to primary interests."
  },

  # ... (additional categorized items omitted for clarity) ...

  {
    "id": 10,
    "category": "Science",
    "importance": 4.5,
    "relevance": 5,
    "why": "Discusses advancements in personalized medicine, highly relevant to nursing."
  }
]
```

### H.2.7.3 - Persona 1 Example

#### Prompt:

```text
Output a JSON array containing objects—no explanations, no table formats, no column headers.
CRITICAL: Your response MUST start with '[' and end with ']' to be valid JSON.
CRITICAL: Every object must be wrapped with '{{' and '}}' and properly nested in the array.

Task: For each headline, include an object with these exact fields:
[
  {
    "id": 1,
    "category": "Technology",
    "importance": 4,
    "relevance": 3,
    "why": "This impacts core AI infrastructure development"
  },
  {
    "id": 2,
    "category": "Science",
    "importance": 3.5,
    "relevance": 4.2,
    "why": "New research relevant to quantum computing"
  }
]
Scoring schema:
• importance: 1 trivial ↔ 5 systemic; multiply raw impact by e^(-age_days/5).
• relevance: 1 tangential ↔ 5 directly affects {interests}.
Allowed categories: Technology | Science | Defence | Politics | Markets | Other


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


## NEWS ITEMS TO CATEGORIZE:
1. US and Qatar sign defense and aviation deal as Trump says of emir: 'we just like each other' – live (The Guardian)
   URL: https://www.theguardian.com/us-news/live/2025/may/14/donald-trump-syria-middle-east-gulf-latest-us-politics-news-updates-live Age: 0.0 days

2. Joseph Nye obituary (The Guardian)
   URL: https://www.theguardian.com/us-news/2025/may/14/joseph-nye-obituary Age: 0.0 days

# ... (and 8 other news items omitted for clarity) ...

Ensure your entire response is ONLY the JSON array as described.
```

#### Response:

```text
{
"{{\"id\": 1, \"category\": \"Defence\", \"importance\": 4.0, \"relevance\": 3.5, \"why\": \"Impacts US foreign policy and international defense relations\"}}": {},
"{{\"id\": 2, \"category\": \"Politics\", \"importance\": 3.0, \"relevance\": 3.0, \"why\": \"Reflects on influential political theories and their originator\"}}": {},

# ... (additional categorized items omitted for clarity) ...

"{{\"id\": 8, \"category\": \"Technology\", \"importance\": 4.5, \"relevance\": 4.5, \"why\": \"Directly impacts AI industry practices and transparency\"}}": {}
}
```

### H.2.7.3 - Persona 3 Example (Model Architect at Anthropic)

#### Prompt:

```text
Output a JSON array containing objects—no explanations, no table formats, no column headers.
CRITICAL: Your response MUST start with '[' and end with ']' to be valid JSON.
CRITICAL: Every object must be wrapped with '{{' and '}}' and properly nested in the array.

Task: For each headline, include an object with these exact fields:
[
  {
    "id": 1,
    "category": "Technology",
    "importance": 4,
    "relevance": 3,
    "why": "This impacts core AI infrastructure development"
  },
  {
    "id": 2,
    "category": "Science",
    "importance": 3.5,
    "relevance": 4.2,
    "why": "New research relevant to quantum computing"
  }
]
Scoring schema:
• importance: 1 trivial ↔ 5 systemic; multiply raw impact by e^(-age_days/5).
• relevance: 1 tangential ↔ 5 directly affects {interests}.
Allowed categories: Technology | Science | Defence | Politics | Markets | Other


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


## NEWS ITEMS TO CATEGORIZE:
1. US and Qatar sign defense and aviation deal as Trump says of emir: 'we just like each other' – live (The Guardian)
   URL: https://www.theguardian.com/us-news/live/2025/may/14/donald-trump-syria-middle-east-gulf-latest-us-politics-news-updates-live Age: 0.0 days

2. Joseph Nye obituary (The Guardian)
   URL: https://www.theguardian.com/us-news/2025/may/14/joseph-nye-obituary Age: 0.0 days

# ... (and 6 other news items omitted for clarity) ...

Ensure your entire response is ONLY the JSON array as described.
```

#### Response:

```text
{
  "results": [
    {
      "id": 1,
      "category": "Defence",
      "importance": 3.8,
      "relevance": 2.5,
      "why": "US-Qatar defense deal influences geopolitical stability"
    },
    {
      "id": 2,
      "category": "Politics",
      "importance": 2.5,
      "relevance": 3,
      "why": "Joseph Nye's concept of 'soft power' impacts diplomatic strategies"
    },

    # ... (additional categorized items omitted for clarity) ...

    {
      "id": 8,
      "category": "Technology",
      "importance": 4.5,
      "relevance": 4.8,
      "why": "Directly impacts AI development practices and intellectual property usage"
    }
  ]
}
```


#### Prompt:

```text
Output a JSON array containing objects—no explanations, no table formats, no column headers.
CRITICAL: Your response MUST start with '[' and end with ']' to be valid JSON.
CRITICAL: Every object must be wrapped with '{{' and '}}' and properly nested in the array.

Task: For each headline, include an object with these exact fields:
[
  {
    "id": 1,
    "category": "Technology",
    "importance": 4,
    "relevance": 3,
    "why": "This impacts core AI infrastructure development"
  },
  {
    "id": 2,
    "category": "Science",
    "importance": 3.5,
    "relevance": 4.2,
    "why": "New research relevant to quantum computing"
  }
]
Scoring schema:
• importance: 1 trivial ↔ 5 systemic; multiply raw impact by e^(-age_days/5).
• relevance: 1 tangential ↔ 5 directly affects {interests}.
Allowed categories: Technology | Science | Defence | Politics | Markets | Other


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


## NEWS ITEMS TO CATEGORIZE:
1. UK ministers block amendment requiring AI firms to declare use of copyrighted content (The Guardian)
   URL: https://www.theguardian.com/technology/2025/may/14/uk-ministers-to-block-amendment-requiring-ai-firms-to-declare-use-of-copyrighted-content Age: 0.0 days

2. Unpacking Rippling vs Deel: corporate espionage and a $16.8B plot twist (TechCrunch)
   URL: https://techcrunch.com/podcast/unpacking-rippling-vs-deel-corporate-espionage-and-a-16-8b-plot-twist/ Age: 0.0 days

# ... (and 8 other news items omitted for clarity) ...

Ensure your entire response is ONLY the JSON array as described.
```

#### Response:

```text
{
  "id": 1,
  "category": "Technology",
  "importance": 4.0,
  "relevance": 4.0,
  "why": "This impacts core AI infrastructure development"
}
```





