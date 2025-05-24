# News Bot: Personalized Multi-Agent News Curation

![Status](https://img.shields.io/badge/status-WIP-yellow)
![License](https://img.shields.io/badge/license-MIT-blue)
![Python](https://img.shields.io/badge/python-3.10%2B-blue)
![Last Commit](https://img.shields.io/github/last-commit/FF-GardenFn/personalized-news-bot)
![Issues](https://img.shields.io/github/issues/FF-GardenFn/personalized-news-bot)
![PRs](https://img.shields.io/github/issues-pr/FF-GardenFn/personalized-news-bot)

The News Bot transforms raw news data into personalized, actionable intelligence through a sophisticated multi-agent AI pipeline, helping users navigate information overload with precisely curated content relevant to their professional and personal interests.

**Note**: This is an ongoing personal project. The documentation/ directory has been open-sourced solely for the purpose of evaluating my work in current job applications. All example outputs are genuine (redacted where appropriate); some sections are still in active development.

## Table of Contents
* [Project Vision & Goals](#project-vision--goals)
* [Key Features & Capabilities](#key-features--capabilities)
* [Philosophical Motivation: AI as a True Partner](#philosophical-motivation-ai-as-a-true-partner)
* [Quick Start & Reviewer's Guide](#quick-start--reviewers-guide)
* [Prompt Engineering Highlights](#prompt-engineering-highlights)
* [Technical Foundation & Architecture Overview](#technical-foundation--architecture-overview)
* [Development Status & Roadmap](#development-status--roadmap)
* [Model Evolution & Versioning](#model-evolution--versioning)

## Project Vision & Goals

The News Bot project aims to revolutionize how individuals consume news by creating a sophisticated AI system that transforms raw news data into personalized, actionable intelligence. Our vision is to help users navigate the overwhelming volume of daily news by providing them with precisely curated content that is directly relevant to their professional and personal interests.

### Core Goals

1. **Information Filtering**: Reduce information overload by filtering news based on relevance and importance
2. **Personalization**: Tailor news content to each recipient's specific context and interests
3. **Insight Generation**: Extract non-obvious connections and strategic implications from news items
4. **Fact Verification**: Assess the credibility of news sources and claims
5. **Actionable Intelligence**: Transform news into concrete, actionable insights

## Key Features & Capabilities

> *A lightweight multi-agent system—each agent solves one part of the puzzle.*

The News Bot leverages a multi-agent architecture where specialized agents work together to process news content through several stages:

| Agent | Primary Role                                                 |
|-------|--------------------------------------------------------------|
| **Categorizer Agent**| Assigns topics, importance scores, and relevance ratings |
| **Fact-Checker Agent**| Evaluates source reliability and content credibility   |
| **Recommender Agent**| Identifies trending topics and suggests additional sources |
| **Synthesis Agent**| Discovers cross-headline patterns and strategic implications |
| **Summarizer Agent**|Generates personalized briefings with actionable takeaways |
| **Linkage Detector**| Discovers connections between seemingly unrelated news items |
| **ArXiv Searcher**| Retrieves and analyzes relevant academic papers |

All agents are judged against a unified evaluation harness, with *Actionable Insight Rate* (AIR) as the North-Star metric, as detailed in our [Evaluation Framework](02_Standards_Of_Evaluation/evaluation_framework.md).

## Philosophical Motivation: AI as a True Partner

The aspiration articulated throughout this project is for an AI that transcends its role as a mere information processor or task automator.

The ultimate vision is of AI as a true partner in the human endeavor of flourishing—an AI that empowers individuals to:
- Navigate the complexities of the modern world with greater clarity
- Make more informed and value-aligned decisions
- Ultimately lead more meaningful and fulfilling lives

Achieving this requires more than just technological breakthroughs; it demands:
- A sustained commitment to human-centric design
- Unwavering ethical principles
- Rigorous safety protocols
- A continuous, reflective dialogue about the future we wish to build

Achieving human flourishing through AI requires shifting from an instrumental to a relational paradigm—treating AI not just as a tool, but as a cognitive partner in autonomy, trust, and shared intelligence.

## Quick Start & Reviewer's Guide

To facilitate efficient review, I recommend the following path through the documentation:

1. **Start with Prompt Engineering Examples**:
   - [Focused Prompt Engineering Examples](03_Prompt_Engineering/03.2-focused-examples/): Examine the Linkage Detection and Recommender agent examples
   - [Prompt Engineering Case Study](03_Prompt_Engineering/03.2-focused-examples/prompt_engineering_case_study.md): Review the comprehensive case study

2. **Explore Key System Components**:
   - [Evaluation Framework](02_Standards_Of_Evaluation/evaluation_framework.md): Understand how we measure success
   - [System Architecture](04_Architecture/system_architecture.md): Learn about the multi-agent design
   - [Detailed Project Overview](01_Overview/project_overview.md): Get a comprehensive understanding of the project

3. **Review Performance Analysis**:
   - [Analysis Reports (by version)](05_Analysis/): Examine quantitative metrics across system versions

## Prompt Engineering Highlights

This project showcases the evolution of prompt engineering techniques across multiple system versions (H1 → H2.7.3):

- **H1**: Basic role-specific prompts with limited structure
- **H2**: Improved structure with shared system-level prompts
- **H2.5**: Enhanced with market data context and few-shot examples
- **H2.7**: Advanced function-calling and explicit constraints
- **H2.7.3**: Sophisticated templating system with standardized formats

The [Focused Examples](03_Prompt_Engineering/03.2-focused-examples/) section provides detailed before/after comparisons, showing exact prompts, model outputs, and explanations of the iterative refinement process, along with quantitative outcomes where possible.

## Technical Foundation & Architecture Overview

The News Bot is built on a foundation of:

- **Large Language Models**: Utilizing state-of-the-art LLMs for natural language understanding and generation
- **Prompt Engineering**: Sophisticated prompting strategies to guide model behavior
- **Agent Architecture**: Specialized agents with distinct roles and capabilities
- **Orchestration Layer**: Coordinating agent interactions and workflow

The system has evolved through two main architecture types:
1. **Type-S (Sequential)**: Initial implementation with sequential processing
2. **Type-H (Hierarchical)**: Advanced implementation with parallel processing and specialized agents

For full details, see the [System Architecture](04_Architecture/system_architecture.md) document.

## Development Status & Roadmap

The News Bot is currently in active development (Version H2.7.3), with several key milestones achieved:

### Completed Milestones
- ✅ Multi-agent architecture implementation
- ✅ Specialized prompt engineering for each agent type
- ✅ Basic personalization capabilities
- ✅ Cross-item synthesis functionality
- ✅ JSON-structured agent outputs

### In Progress
- 🔄 Enhanced personalization algorithms
- 🔄 Improved fact-checking capabilities
- 🔄 Permanent JSON solution for agent outputs
- 🔄 More sophisticated synthesis techniques
- 🔄 Optimization of prompt efficiency

### Future Roadmap
- 📅 Integration with real-time news sources
- 📅 User feedback incorporation
- 📅 Multi-modal content processing
- 📅 Advanced personalization using historical user data
- 📅 Batch processing


## Model Evolution & Versioning

Our iterative development journey (from initial Type-S models to the current Type-H2.7.3) is detailed through version-specific design documents and performance analyses, primarily found within the `[04_Architecture/](04_Architecture/)` and `[05_Analysis/]` directories. These sections explain the evolution of strategies, design choices, and outcomes, often referencing earlier stages.

For those interested in the most granular historical data, a `legacy/` directory archives raw materials from these earlier iterations, including detailed logs, original development notes, and deprecated code segments. This provides a comprehensive trail of the incremental improvements made in performance, cost-efficiency, latency, factual accuracy, and safety metrics over the project's nine-month development.

---

If you review this documentation in depth, I will be honoured and grateful. Thank you for your time and consideration.
