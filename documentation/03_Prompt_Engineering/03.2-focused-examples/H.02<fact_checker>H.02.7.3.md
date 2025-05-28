 # Fact Checker Agent: H.02 Vs H.02.7.3

## Overview

This document showcases the evolution of the Fact Checker Agent, by showcasing the difference between version H.02 and H.02.7.3, demonstrating how prompt engineering significantly improved the agent's ability to verify information accuracy and credibility. The examples below highlight the transformation from basic credibility scoring to sophisticated multi-dimensional analysis.

## Version H.02: Basic Fact Checking

### FactChecker-H.02 Interaction 

The initial version provided simple credibility scores without detailed analysis or specific concerns.

### Input

```text
## NEWS ITEMS
1. Technical analysis of the Signal clone used by Trump officials (Hacker News) (2025-05-02 23:20:59+00:00)

URL: https://micahflee.com/tm-sgnl-the-obscure-unofficial-signal-app-mike-waltz-uses-to-text-with-trump-officials/

2. The Death of Daydreaming (Hacker News) (2025-05-05 12:22:10+00:00)

URL: https://www.afterbabel.com/p/on-the-death-of-daydreaming

  # ... (and 8 other news items with similar simple structure omitted for clarity) ...

```

## Prompt

```text
Task: For each headline id, output → {"id","credibility","concerns"}.
Method:
1. credibility = average of
a) Source reputation (indexed list supplied separately),
b) Claim verifiability (open-fact retrieval).
Scale: 0-1 float.
List concrete concerns; empty list if none.

```

### Response

```json
[
{
"id": 1,
"credibility": 0.6,
"concerns": []
},
{
"id": 2,
"credibility": 0.4,
"concerns": []
},
{
"id": 3,
"credibility": 0.8,
"concerns": []
},
{
"id": 4,
"credibility": 0.7,
"concerns": []
},
{
"id": 5,
"credibility": 0.5,
"concerns": []
},
{
"id": 6,
"credibility": 0.5,
"concerns": []
},
{
"id": 7,
"credibility": 0.5,
"concerns": []
},
{
"id": 8,
"credibility": 0.5,
"concerns": []
},
{
"id": 9,
"credibility": 0.6,
"concerns": []
},
{
"id": 10,
"credibility": 0.6,
"concerns": []
}
]
```


## Version H.02.7.3: Advanced Multi-Dimensional Analysis

### FactChecker-H.02.7.3 Interaction 

The enhanced version introduces sophisticated multi-dimensional analysis with strict output formatting requirements.

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
1. US and Qatar sign defense and aviation deal as Trump says of emir: ‘we just like each other’ – live (The Guardian) CONTENT_SNIPPET: Protesters interrupted Robert F Kennedy Jr’s opening remarks before the Senate health committee this afternoon, shouting: “RFK kills people with Aids!” The health secretary was visibly startled and jumped from his chair when protesters began shouting, before being removed by Capitol police. “That was a made for C-Span moment,” said senator Bill Cassidy, the Republican chair of the committee. Dozens of federal health workers and offices dedicated to HIV/Aids research have been shuttered under Ken... URL: https://www.theguardian.com/us-news/live/2025/may/14/donald-trump-syria-middle-east-gulf-latest-us-politics-news-updates-live Age: 0.0 days
2. Joseph Nye obituary (The Guardian) CONTENT_SNIPPET: Joseph Nye, who has died aged 88, owed his fame in the diplomatic world to just two words: “soft power”. The international affairs scholar came up with the phrase while sitting at his kitchen table trying to find a way to condense and encapsulate his approach to US foreign policy. He acknowledged military force and economic power were essential components in pursuit of the national interest but argued there was a third element, one not requiring force, that was under-appreciated. He called it so... URL: https://www.theguardian.com/us-news/2025/may/14/joseph-nye-obituary Age: 0.0 days
3. Trump ally to brief European leaders on sanctions to weaken Russia’s economy (The Guardian) CONTENT_SNIPPET: The US senator, Lindsey Graham, a close ally of Donald Trump, is expected to brief European leaders on Thursday in Antalya on his plans to push through Congress sanctions designed to devastate the Russian economy if Vladimir Putin does not show a willingness to negotiate the future of Ukraine in good faith. Graham is due to meet European foreign ministers, including the UK foreign secretary David Lammy, in Turkey where they had gathered on Wednesday for a long scheduled informal two-day Nato mee... URL: https://www.theguardian.com/world/2025/may/14/trump-ally-to-brief-european-leaders-on-sanctions-to-weaken-russias-economy Age: 0.0 days
4. Sperm donor who claims he fathered more than 180 children loses custody battle (The Guardian) CONTENT_SNIPPET: An unregistered sperm donor who says he has fathered more than 180 children has failed to gain custody of a three-year-old child he had with a Durham woman, who said she was left “broken” and “suicidal” by their encounter. Robert Albon, who goes by the pseudonym “Joe Donor” and has appeared on This Morning and in a Channel 4 documentary, applied to have the girl live with him after a court deemed her mother was unable to look after her. It is one of four times the 54-year-old American has attemp... URL: https://www.theguardian.com/science/2025/may/14/sperm-donor-who-boasts-of-180-children-loses-custody-battle Age: 0.0 days
5. Taxi driver in France charged with stealing from David Lammy and his wife (The Guardian) CONTENT_SNIPPET: A taxi driver has been charged by French police with stealing luggage and cash from the UK’s foreign secretary, David Lammy, and his wife, Nicola Green. The driver took the couple more than 600km (370 miles) from the town of Forli in Italy to the French ski resort of Flaine, Haute-Savoie, last month. The Foreign, Commonwealth and Development Office (FCDO) said Lammy and his spouse were victims in the case and that the driver has been charged with theft after driving off with their luggage. It al... URL: https://www.theguardian.com/politics/2025/may/14/taxi-driver-in-france-charged-with-stealing-from-david-lammy-and-his-wife Age: 0.0 days
6. Menendez brothers ‘huge step’ closer to freedom as judge reduces sentences (The Guardian) CONTENT_SNIPPET: After months of delays and decades behind bars, Erik and Lyle Menendez now have a long-awaited chance at freedom after a judge reduced their sentences for the 1989 killings of their parents. Their family and massive network of supporters celebrated Tuesday when Judge Michael Jesic resentenced the brothers from life in prison without the possibility of parole to 50 years to life. The judge’s decision means they are immediately eligible for parole under California’s youthful offender law because o... URL: https://www.theguardian.com/us-news/2025/may/14/menendez-brothers-judge-sentence-parole Age: 0.0 days
7. White House scraps plan to block data brokers from selling Americans’ sensitive data (TechCrunch) CONTENT_SNIPPET: The decision to reverse course comes after an industry lobby group called for the rule change to be withdrawn. URL: https://techcrunch.com/2025/05/14/white-house-scraps-plan-to-block-data-brokers-from-selling-americans-sensitive-data/ Age: 0.0 days
8. How this VC spots the global game changers (TechCrunch) CONTENT_SNIPPET: Accel’s Sonali De Rycker chatted with TechCrunch’s Editor-in-Chief Connie Loizos about what it takes to spot and scale category-defining companies in today’s fast-evolving tech landscape. She shared perspectives on identifying early-stage bets and supporting global growth. URL: https://techcrunch.com/video/how-this-vc-spots-the-global-game-changers/ Age: 0.0 days
9. 1.5m foreign workers already in UK could face longer wait for permanent settlement (The Guardian) CONTENT_SNIPPET: About 1.5 million foreign workers who have moved to Britain since 2020 may have to wait a further five years to apply for permanent settlement. Under changes set out in the immigration white paper, automatic settlement and citizenship rights will be granted after 10 years instead of five. But the paper did not specify whether this would apply to recent arrivals already in the UK and in the process of their application. Yvette Cooper, the home secretary, will consult stakeholders on whether the c... URL: https://www.theguardian.com/uk-news/2025/may/14/foreign-workers-uk-wait-permanent-settlement Age: 0.0 days
10. Ministers block Lords bid to make AI firms declare use of copyrighted content (The Guardian) CONTENT_SNIPPET: Ministers have used an arcane parliamentary procedure to block an amendment to the data bill that would require artificial intelligence companies to disclose their use of copyright-protected content. The government stripped the transparency amendment, which was backed by peers in the bill’s reading in the House of Lords last week, out of the draft text by invoking financial privilege, meaning there is no budget available for new regulations, during a Commons debate on Wednesday afternoon. The am... URL: https://www.theguardian.com/technology/2025/may/14/uk-ministers-to-block-amendment-requiring-ai-firms-to-declare-use-of-copyrighted-content Age: 0.0 days

Ensure your entire response is ONLY the JSON array as described in the initial instructions.

```

### Response

```json
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

## Analysis of Key Differences

The evolution from H.02 to H.02.7.3 demonstrates significant improvements in the Fact Checker Agent's capabilities through strategic prompt engineering:

### Key Prompt Engineering Strategies

1. **Enhanced Output Structure**
- H.02: Simple credibility score with no detailed breakdown
- H.02.7.3: Multi-dimensional analysis including source reliability, content credibility, overall credibility, specific concerns, market impact, and political slant

2. **Explicit Formatting Requirements**
- H.02: Minimal guidance on output format
- H.02.7.3: Detailed JSON structure requirements with explicit formatting instructions and error prevention guidance

3. **Few-Shot Examples**
- H.02: No examples provided
- H.02.7.3: Clear examples of desired output structure embedded within the prompt

4. **Critical Instructions**
- H.02: Basic task description
- H.02.7.3: Multiple critical instructions to enforce valid JSON array output and prevent common formatting errors

### Impact on Model Performance

The refined prompt engineering transformed the Fact Checker Agent from providing simplistic credibility scores to delivering sophisticated, multi-faceted analysis. This evolution significantly enhanced:

- **Analytical Depth**: From single-dimension to multi-dimensional assessment
- **Output Consistency**: Structured JSON format with clear field definitions
- **Actionable Intelligence**: Addition of market impact assessment and political slant analysis
- **Verification Transparency**: Explicit identification of specific concerns by category

---

> **Note on H.02.7.3 Fact Checker Agent Performance and Output Format:**
>
> The H.02.7.3 example presented in this document demonstrates a significant step forward in the analytical depth requested from the Fact Checker Agent. However, it also highlights a notable challenge in early iterations of this advanced version: while the prompt explicitly instructed the agent to process all news items and return a JSON array, the observed output in this specific interaction consisted of a single JSON object for only the first news item.
>
> Addressing this deviation—ensuring consistent iterative processing across all items and strict adherence to the desired array output structure—has been a key focus of ongoing refinements for the H.02.7.3 Fact Checker. Efforts also include token optimization for the expanded data fields generated by this more sophisticated agent.
>
> For a comprehensive discussion of these format compliance challenges, detailed performance metrics, and the full H.02.7.3 system evaluation across all agents, please refer to the **[detailed H.02.7.3 system analysis](../../05_Analysis/05.2-Type-H/H.02.7.3/analysis.md)**.