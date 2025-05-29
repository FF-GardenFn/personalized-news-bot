# News Bot: Agent Architecture

## Table of Contents
1. [Overview](#overview)
2. [Agent Types](#agent-types)
3. [Agent Interactions](#agent-interactions)
4. [Agent Implementation](#agent-implementation)
5. [Orchestration](#orchestration)
6. [Evolution of Agent Architecture](#evolution-of-agent-architecture)

## Overview

In its current state the system employs a sophisticated multi-agent architecture where specialized agents work together to process news data. This approach allows for:

1. **Specialization**: Each agent focuses on a specific task, allowing for deeper analysis
2. **Parallelization**: Agents can work in parallel, improving efficiency
3. **Modularity**: New agents can be added or existing ones modified without affecting the entire system
4. **Robustness**: If one agent fails, others can continue working

The agent architecture follows a wave-based execution model, where agents are organized into groups that execute in sequence, with each wave building on the results of previous waves.

### High-Level Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────────┐
│                             NEWS SOURCES                                │
│  (NewsAPI, Finance APIs, Academic APIs, Patents, GitHub, X/Twitter)     │
└───────────────────────────────────────┬─────────────────────────────────┘
                                        │
                                        ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                          VECTOR SEARCH LAYER                            │
│                 (FAISS + Embeddings + Semantic Search)                  │
└───────────────────────────────────────┬─────────────────────────────────┘
                                        │
                                        ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                            AGENT SWARM                                  │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌───────────┐ │
│  │   Wave 0    │───▶│   Wave 1    │───▶│   Wave 2    │───▶│  Wave 3   │ │
│  │(WebSearcher)│    │(Categorizer,│    │(FactChecker)│    │(Triangul.)│ │
│  └─────────────┘    │ Recommender)│    └─────────────┘    └─────┬─────┘ │
│                     └─────────────┘                             │       │
│                                                                 ▼       │
│                                                         ┌───────────────┐│
│                                                         │    Wave 4     ││
│                                                         │ (Synthesizer, ││
│                                                         │ LinkageDetect,││
│                                                         │ ArXivSearcher,││
│                                                         │ GitHubSearch.,││
│                                                         │  PatentAgent) ││
│                                                         └───────┬───────┘│
│                                                                 │       │
│                                                                 ▼       │
│                                                         ┌───────────────┐│
│                                                         │    Wave 5     ││
│                                                         │  (Summarizer) ││
│                                                         └───────────────┘│
│                                                                         │
└─────────────────────────────────────────┬─────────────────────────────  ┘
                                          │
                                          ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                           DELIVERY CHANNELS                             │
│                               (**Email**)                               │
└─────────────────────────────────────────────────────────────────────────┘
```

## Agent Types

### Wave 0 Agents

These agents perform initial data gathering and web searches:

#### Web Searcher

**Purpose**: Perform web searches to gather additional information about news items.

**Inputs**:
- News items
- Recipient information

**Outputs**:
- Additional context
- Supporting evidence
- Related news items
- External references

**Implementation**: Uses semantic search to find relevant web content and integrates it with existing news items.

**Dependencies**: None

### Wave 1 Agents

These agents perform initial analysis of news items:

#### Categorizer

**Purpose**: Categorize news items by topic and assign importance scores.

**Inputs**:
- News items
- Recipient information

**Outputs**:
- Primary category for each news item
- Secondary category (if applicable)
- Importance score (1-10)
- Relevance to recipient's interests (1-10)
- Recency factor

**Implementation**: Uses GPT-4o-mini or Grok to analyze news items and categorize them.

**Dependencies**: None

#### Recommender

**Purpose**: Recommend topics and sources based on news items and recipient interests.

**Inputs**:
- News items
- Recipient information

**Outputs**:
- Recommended topics
- Recommended sources
- Relevance scores
- Explanation of recommendations

**Implementation**: Uses GPT-4o-mini or Grok to generate recommendations.

**Dependencies**: None

### Wave 2 Agents

These agents perform factual verification:

#### Fact-Checker

**Purpose**: Verify the factual accuracy of news items.

**Inputs**:
- News items
- Recipient information
- Web Searcher results

**Outputs**:
- Source reliability score (1-10)
- Content credibility score (1-10)
- Potential bias assessment (left, right, center, none)
- Factual inconsistencies
- Missing context

**Implementation**: Uses GPT-4o or Grok to assess the factual accuracy of news items.

**Dependencies**: Web Searcher

### Wave 3 Agents

These agents perform refined verification:

#### Triangulator

**Purpose**: Compare information from multiple sources to verify facts and identify bias.

**Inputs**:
- News items
- Fact-checker results

**Outputs**:
- Triangulation results
- Divergence metrics
- Bias assessment
- Cross-source verification

**Implementation**: Compares facts across sources and calculates divergence metrics to identify inconsistencies and potential bias.

**Dependencies**: Fact-Checker

### Wave 4 Agents

These agents perform deeper analysis and integration:

#### Synthesizer

**Purpose**: Identify patterns and insights across news items.

**Inputs**:
- News items
- Categorizer results
- Fact-checker results
- Recommender results
- Triangulator results

**Outputs**:
- Common themes
- Contradictions
- Cause-effect relationships
- Emerging trends
- Implications for the recipient

**Implementation**: Uses GPT-4o to analyze relationships between news items and identify meaningful patterns.

**Dependencies**: Categorizer, Recommender, Fact-Checker, Triangulator

#### Linkage Detector

**Purpose**: Identify connections between different news items, academic papers, and other sources.

**Inputs**:
- News items
- Academic papers
- Patent data
- GitHub repositories
- Categorizer results
- Fact-checker results
- Triangulator results

**Outputs**:
- Cross-source connections
- Relationship types (causal, correlational, etc.)
- Connection strength scores
- Visualization data

**Implementation**: Uses vector embeddings and graph analysis to identify and quantify connections between different sources.

**Dependencies**: Categorizer, Fact-Checker, Triangulator

#### ArXiv Searcher

**Purpose**: Find academic papers related to news items.

**Inputs**:
- News items
- Categorizer results
- Recipient interests
- Fact-checker results
- Triangulator results

**Outputs**:
- Relevant academic papers
- Key findings
- Connections to news items
- Research trend analysis

**Implementation**: Queries arXiv API and uses vector matching to find relevant papers and extract key insights.

**Dependencies**: Categorizer, Fact-Checker, Triangulator

#### GitHub Searcher

**Purpose**: Search GitHub for repositories related to news items and academic papers.

**Inputs**:
- News items
- Academic papers
- Categorizer results
- Fact-checker results
- Triangulator results

**Outputs**:
- Relevant GitHub repositories
- Repository metadata
- Code examples
- Developer activity metrics

**Implementation**: Queries GitHub API to find trending and relevant repositories based on news and academic content.

**Dependencies**: Categorizer, Fact-Checker, Triangulator

#### Patent Agent

**Purpose**: Extract key claims from patents and connect them to news items.

**Inputs**:
- News items
- Patent data
- Categorizer results
- Fact-checker results
- Triangulator results

**Outputs**:
- Key patent claims
- Connections to news items
- Implications for innovation
- Patent trend analysis

**Implementation**: Uses vector search to find relevant patents and LLM to extract claims and analyze implications.

**Dependencies**: Categorizer, Fact-Checker, Triangulator

### Wave 5 Agents

These agents produce the final output:

#### Summarizer

**Purpose**: Combine all results into a coherent briefing.

**Inputs**:
- News items
- Results from all Wave 4 agents
- Recommender results
- Market data
- Recipient preferences

**Outputs**:
- Personalized news briefing
- Action triggers
- Cross-references
- Strategic insights
- Visualization data

**Implementation**: Uses GPT-4o to generate a comprehensive briefing that integrates all agent outputs into a cohesive narrative.

**Dependencies**: Synthesizer, Linkage Detector, ArXiv Searcher, GitHub Searcher, Patent Agent, Recommender

## Agent Interactions

The agents interact through a structured flow organized into waves. Each wave builds on the results of previous waves, with the orchestrator managing the execution and data flow between agents.

### Complete Agent Interaction Diagram

```
┌───────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                           WAVE 0                                                  │
├───────────────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                                   │
│                                  ┌─────────────────┐                                              │
│                                  │  Web Searcher   │                                              │
│                                  └────────┬────────┘                                              │
│                                           │                                                       │
└───────────────────────────────────────────┼───────────────────────────────────────────────────────┘
                                            │
                                            ▼
┌───────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                       ORCHESTRATOR                                                │
│                                                                                                   │
│   ┌─────────────────────────────────────────────────────────────────────────────────────────┐     │
│   │                                 Data Aggregation Layer                                  │     │
│   └─────────────────────────────────────────────────────────────────────────────────────────┘     │
│                                             │                                                     │
│   ┌─────────────────────────────────────────────────────────────────────────────────────────┐     │
│   │                                 Execution Planning Layer                                │     │
│   └─────────────────────────────────────────────────────────────────────────────────────────┘     │
│                                             │                                                     │
│   ┌─────────────────────────────────────────────────────────────────────────────────────────┐     │
│   │                                 Result Distribution Layer                               │     │
│   └─────────────────────────────────────────────────────────────────────────────────────────┘     │
│                                                                                                   │
└───────────────────────────────────────────────────────────────────────────────────────────────────┘
                                            │
                                            ▼
┌───────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                           WAVE 1                                                  │
├───────────────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                                   │
│   ┌─────────────────┐                                      ┌─────────────────┐                    │
│   │   Categorizer   │                                      │   Recommender   │                    │
│   └────────┬────────┘                                      └────────┬────────┘                    │
│            │                                                        │                             │
└────────────┼────────────────────────────────────────────────────────┼─────────────────────────────┘
             │                                                        │
             ▼                                                        ▼
┌───────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                       ORCHESTRATOR                                                │
└───────────────────────────────────────────────────────────────────────────────────────────────────┘
                                            │
                                            ▼
┌───────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                           WAVE 2                                                  │
├───────────────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                                   │
│                                  ┌─────────────────┐                                              │
│                                  │  Fact-Checker   │                                              │
│                                  └────────┬────────┘                                              │
│                                           │                                                       │
└───────────────────────────────────────────┼───────────────────────────────────────────────────────┘
                                            │
                                            ▼
┌───────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                       ORCHESTRATOR                                                │
└───────────────────────────────────────────────────────────────────────────────────────────────────┘
                                            │
                                            ▼
┌───────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                           WAVE 3                                                  │
├───────────────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                                   │
│                                  ┌─────────────────┐                                              │
│                                  │  Triangulator   │                                              │
│                                  └────────┬────────┘                                              │
│                                           │                                                       │
└───────────────────────────────────────────┼───────────────────────────────────────────────────────┘
                                            │
                                            ▼
┌───────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                       ORCHESTRATOR                                                │
└───────────────────────────────────────────────────────────────────────────────────────────────────┘
                                            │
                                            ▼
┌───────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                           WAVE 4                                                  │
├───────────────────────────────────────────────────────────────────────────────────────────────────┤   
│                                                                                                   │
│   ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐     │
│   │   Synthesizer   │     │ Linkage Detector│     │  Patent Agent   │     │  ArXiv Searcher │     │
│   └────────┬────────┘     └────────┬────────┘     └────────┬────────┘     └────────┬────────┘     │
│            │                       │                       │                       │              │
│            │                       │                       │                       │              │
│   ┌─────────────────┐                                                                             │
│   │ GitHub Searcher │                                                                             │
│   └────────┬────────┘                                                                             │
│            │                                                                                      │
└────────────┼───────────────────────┼───────────────────────┼───────────────────────┼──────────────┘
             │                       │                       │                       │
             ▼                       ▼                       ▼                       ▼
┌───────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                       ORCHESTRATOR                                                │
└───────────────────────────────────────────────────────────────────────────────────────────────────┘
                                            │
                                            ▼
┌───────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                           WAVE 5                                                  │
├───────────────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                                   │
│                                  ┌─────────────────┐                                              │
│                                  │   Summarizer    │                                              │
│                                  └─────────────────┘                                              │
│                                                                                                   │
└───────────────────────────────────────────────────────────────────────────────────────────────────┘
```

### Orchestrator Layers

The orchestrator consists of three main layers:

1. **Data Aggregation Layer**: Collects and normalizes results from agents in the previous wave
   - Validates data formats
   - Handles missing or error results
   - Prepares data for the next wave

2. **Execution Planning Layer**: Determines which agents to run in the next wave
   - Makes conditional decisions (e.g., run Triangulator only if Fact-Checker succeeded)
   - Allocates resources based on task priority
   - Manages parallel execution

3. **Result Distribution Layer**: Distributes relevant data to agents in the next wave
   - Filters data based on agent requirements
   - Formats data for each agent's expected input
   - Tracks data lineage for debugging and analysis

### Detailed Agent Interactions

#### Wave 0 to Wave 2

The Wave 0 agent (Web Searcher) performs initial data gathering:

1. **Web Searcher → Fact-Checker**: Provides additional context and supporting evidence for fact verification

#### Wave 1 to Wave 4

The Wave 1 agents (Categorizer, Recommender) perform initial analysis:

1. **Categorizer → Synthesizer**: Provides category information to help identify patterns across news items
2. **Categorizer → Linkage Detector**: Helps identify connections between categorized news items
3. **Categorizer → ArXiv Searcher**: Guides academic paper search based on news categories
4. **Categorizer → GitHub Searcher**: Helps identify relevant repositories based on news categories
5. **Categorizer → Patent Agent**: Provides category context for patent analysis
6. **Recommender → Synthesizer**: Provides topic recommendations to help identify patterns

#### Wave 2 to Wave 3

The Wave 2 agent (Fact-Checker) performs factual verification:

1. **Fact-Checker → Triangulator**: Provides initial fact-checking results for the Triangulator to compare with other sources

#### Wave 3 to Wave 4

The Wave 3 agent (Triangulator) performs refined verification:

1. **Triangulator → Synthesizer**: Provides verified facts and identified biases for pattern recognition
2. **Triangulator → Linkage Detector**: Helps identify connections between verified information
3. **Triangulator → Patent Agent**: Provides verified context for patent analysis
4. **Triangulator → ArXiv Searcher**: Guides academic paper search based on verified information
5. **Triangulator → GitHub Searcher**: Helps identify relevant repositories based on verified information

#### Wave 4 to Wave 5

The Wave 4 agents (Synthesizer, Linkage Detector, Patent Agent, ArXiv Searcher, GitHub Searcher) perform deep analysis and integration:

1. **Synthesizer → Summarizer**: Provides identified patterns and insights for the final briefing
2. **Linkage Detector → Summarizer**: Provides identified connections for the final briefing
3. **Patent Agent → Summarizer**: Provides patent analysis for the final briefing
4. **ArXiv Searcher → Summarizer**: Provides academic research findings for the final briefing
5. **GitHub Searcher → Summarizer**: Provides repository information for the final briefing

#### Wave 1 to Wave 5

The Wave 1 agent (Recommender) also directly contributes to the final output:

1. **Recommender → Summarizer**: Provides topic and source recommendations for the final briefing

### Key Interaction Patterns

1. **Data Flow**: Results from earlier agents flow to later agents, with each wave building on the insights of previous waves
2. **Orchestration**: The orchestrator manages the execution of agents, making decisions about which agents to run and how to distribute data
3. **Parallel Execution**: Agents within the same wave execute in parallel, improving efficiency
4. **Conditional Execution**: Some agents only execute if certain conditions are met (e.g., Triangulator only runs if Fact-Checker succeeds)
5. **Cross-Wave Dependencies**: Later wave agents may depend on results from multiple earlier waves
6. **Result Aggregation**: The Summarizer aggregates results from all previous agents to create a cohesive final briefing
7. **Feedback Loops**: In some cases, agents may request additional information from earlier agents through the orchestrator

## Agent Implementation

All agents inherit from a common base class:

```python
class BaseAgent:
    def __init__(self, name: str, model: str):
        self.name = name
        self.model = model
        self.logger = logging.getLogger(f"agent.{name}")

    async def process(self, recipient: Recipient, data: Dict[str, Any]) -> APIResponse:
        """Process data and return a response."""
        start_time = time.time()

        try:
            # Prepare prompt
            prompt = self._prepare_prompt(recipient, data)

            # Call LLM
            response = await self._call_llm(prompt)

            # Parse response
            result = self._parse_response(response)

            # Log interaction
            self._log_interaction(recipient, prompt, response, result)

            return APIResponse(
                success=True,
                data=result,
                metadata={
                    "processing_time": time.time() - start_time,
                    "model": self.model,
                    "agent": self.name
                }
            )
        except Exception as e:
            self.logger.error(f"Error in {self.name}: {str(e)}")
            return APIResponse(
                success=False,
                error=str(e),
                metadata={
                    "processing_time": time.time() - start_time,
                    "model": self.model,
                    "agent": self.name
                }
            )

    def _prepare_prompt(self, recipient: Recipient, data: Dict[str, Any]) -> str:
        """Prepare the prompt for the LLM."""
        raise NotImplementedError("Subclasses must implement this method")

    async def _call_llm(self, prompt: str) -> str:
        """Call the LLM with the prompt."""
        # Implementation depends on the LLM being used
        raise NotImplementedError("Subclasses must implement this method")

    def _parse_response(self, response: str) -> Dict[str, Any]:
        """Parse the LLM response."""
        raise NotImplementedError("Subclasses must implement this method")

    def _log_interaction(self, recipient: Recipient, prompt: str, response: str, result: Dict[str, Any]):
        """Log the interaction for analysis."""
        log_entry = {
            "timestamp": datetime.now().isoformat(),
            "agent": self.name,
            "recipient_email": recipient.email,
            "prompt": prompt,
            "response": response,
            "result": result,
            "model": self.model
        }
        self.logger.info(json.dumps(log_entry))
```

Each agent implements the abstract methods:

1. `_prepare_prompt`: Constructs the prompt for the LLM
2. `_call_llm`: Calls the LLM with the prompt
3. `_parse_response`: Parses the LLM response into a structured format

## Orchestration

The orchestrator manages the execution of the agent swarm:

```python
class Orchestrator:
    def __init__(self):
        self.agents = {
            "categorizer": CategorizerAgent(),
            "fact_checker": FactCheckerAgent(),
            "recommender": RecommenderAgent(),
            "synthesizer": SynthesizerAgent(),
            "patent_agent": PatentAgent(),
            "arxiv_searcher": ArxivSearcherAgent(),
            "repo_matcher": RepoMatcherAgent(),
            "summarizer": SummarizerAgent()
        }

    async def run(self, recipient: Recipient, news_items: List[NewsItem]) -> APIResponse:
        """Run the agent swarm on the news items."""
        results = {}

        # First wave
        first_wave = ["categorizer", "fact_checker", "recommender"]
        first_wave_tasks = [self.agents[agent].process(recipient, {"news_items": news_items}) for agent in first_wave]
        first_wave_results = await asyncio.gather(*first_wave_tasks)

        for i, agent in enumerate(first_wave):
            results[agent] = first_wave_results[i]

        # Second wave (if fact-checking results are available)
        if results["fact_checker"].success:
            triangulator = TriangulatorAgent()
            results["triangulator"] = await triangulator.process(recipient, {
                "news_items": news_items,
                "fact_check_results": results["fact_checker"].data
            })

        # Third wave
        third_wave = ["synthesizer", "patent_agent", "arxiv_searcher", "repo_matcher"]
        third_wave_tasks = [self.agents[agent].process(recipient, {
            "news_items": news_items,
            "categorizer_results": results["categorizer"].data,
            "fact_checker_results": results["fact_checker"].data,
            "recommender_results": results["recommender"].data,
            "triangulator_results": results.get("triangulator", {}).data
        }) for agent in third_wave]
        third_wave_results = await asyncio.gather(*third_wave_tasks)

        for i, agent in enumerate(third_wave):
            results[agent] = third_wave_results[i]

        # Final wave
        summarizer = self.agents["summarizer"]
        results["summarizer"] = await summarizer.process(recipient, {
            "news_items": news_items,
            **{k: v.data for k, v in results.items() if v.success}
        })

        return results["summarizer"]
```

Key orchestration features:

1. **Wave-Based Execution**: Agents are organized into waves that execute in sequence
2. **Parallel Execution**: Agents within the same wave execute in parallel using `asyncio.gather`
3. **Conditional Execution**: Some agents (like the Triangulator) only execute if certain conditions are met
4. **Result Aggregation**: Results from all agents are aggregated and passed to the Summarizer


