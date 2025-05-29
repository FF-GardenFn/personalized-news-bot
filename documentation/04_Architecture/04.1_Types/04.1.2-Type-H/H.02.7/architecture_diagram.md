# Type-H.02.7 Architecture

## Overview

The Type-H.02.7 architecture represents a significant evolution of the News Bot system, featuring a full agent swarm with specialized agents working in parallel. This version builds on the attempted improvements of H.02.5 while going for a more sophisticated orchestration and function-calling capabilities.

## Architecture Diagram

```
┌───────────────────────────────────────────────────────────────────────────┐
│                               Fetch Stage                                 │
├───────────────────────────────────────────────────────────────────────────┤
│ 1. Global News APIs    → NewsAPI, Guardian, NewsData.io, RSS feeds        │
│ 2. Finance APIs        → AlphaVantage, Yahoo Finance, Coinbase on-chain   │
│ 3. Academic APIs       → arXiv, IEEE Xplore                               │
│ 4. Patent APIs         → PatentsView (US PTO), Google Patents BigQuery    │
│ 5. GitHub Trending     → ghapi.huchen.dev/repositories?since=daily        │
└───────────────────────────────────────────────────────────────────────────┘

        │                                           │ 
        ▼                                           ▼

┌─────────────────────────────┐      ┌────────────────────────────┐
│  Normalize into NewsItem    │      │  Persist raw content +     │
│  {headline, source, content,│      │  metadata in Vector Store  │
│   url, author, date}        │      │  (FAISS + text-embedding)  │
└─────────────────────────────┘      └────────────────────────────┘

        │                                          │
        ▼                                          ▼

┌───────────────────────────────────────────────────────────────────────────┐
│                            Semantic Layer                                 │
│  • LLM "function-call" to generate refined search query                   │
│  • Vector-search top-k semantically similar items across all sources      │
│    (news ↔ academic ↔ patents ↔ repos)                                    │
└───────────────────────────────────────────────────────────────────────────┘

        │                                                  │
        ▼                                                  ▼

┌───────────────────────────────────────────────────────────────────────────  ┐
│                            Agent Swarm H.02.7                               │
│  Run in parallel:                                                           │
│    – Categorizer     (gpt-4o-mini or Grok)                                  │
│    – Fact-Checker    (gpt-4o or Grok)                                       │
│    – Recommender     (gpt-4o-mini or Grok)                                  │
│    – Synthesizer     (gpt-4o or Grok)                                       │
│    – Patent Agent    (vector ↔ LLM extract claims)                          │
│    – IEEE & arXiv     (query Xplore/ArXiv, embed & vector match)            │
│    – Repo-Matcher     (match GitHub Trending repos or repos ↔ news & papers)│
└─────────────────────────────────────────────────────────────────────────────┘

        │                                        │
        ▼                                        ▼

┌───────────────────────────────────────────────────────────────────────────┐
│                         Summarization (H.02.7)                            │
│  • Combine: News + Academic + Patents + Repos cross-links                 │
│  • Render markdown briefing                                               │
│  • Append "Action Triggers"                                               │
│      – Flight price watcher ↔ travel news                                 │
│      – Agent-shopper ↔ product-launch news                                │
│      – Sell/Buy stocks ↔ market movers (AlphaVantage, Yahoo, Coinbase)    │
│      – IEEE "deep dive" link for research-intensive items                 │
│      – "Repo Spotlight" for matched trending projects                     │
└───────────────────────────────────────────────────────────────────────────┘

        │                                           │   
        ▼                                           ▼

┌───────────────────────────────────────────────────────────────────────────┐
│                       Execution & Delivery                                │
│  • Email                                                                  │
│  • Trigger downstream agents (booking, trading, shopping)                 │
│  • Log all token + latency metrics                                        │
└───────────────────────────────────────────────────────────────────────────┘
```
