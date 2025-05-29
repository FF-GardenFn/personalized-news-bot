# Type-S Architecture Diagram

## Overview

The Type-S (Sequential) architecture represents the initial implementation of the News Bot system. It follows a sequential processing model where each component executes in order, with data flowing linearly through the pipeline.

## Architecture Diagram

```
┌───────────────────────────────────────────────────────────────────────────┐
│                               News Sources                                │
├───────────────────────────────────────────────────────────────────────────┤
│ 1. General News      → NewsAPI, RSS feeds (Reuters, BBC, CNN)             │
│ 2. Tech News         → Hacker News API                                    │
│ 3. Political News    → Politico RSS feed                                  │
│ 4. Research Papers   → arXiv API                                          │
│ 5. Financial News    → Alpha Vantage API                                  │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                            Data Processing                                │
├───────────────────────────────────────────────────────────────────────────┤
│ 1. Data Collection   → Fetch news, weather, and financial data            │
│ 2. Data Filtering    → Filter based on recipient interests                │
│ 3. Data Caching      → Store API responses to reduce calls                │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                         News Personalization                              │
├───────────────────────────────────────────────────────────────────────────┤
│ 1. Create Prompt     → Generate prompt with recipient info and news items │
│ 2. Call GPT API      → Generate personalized news briefing                │
│ 3. Add Weather       → Include weather information for recipient location │
│ 4. Add Quote         → Include inspirational quote                        │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                            Email Delivery                                 │
├───────────────────────────────────────────────────────────────────────────┤
│ 1. Create Email      → Generate email content with personalized news      │
│ 2. Send Email        → Send email to recipient                            │
│ 3. Log Process       → Log the process for monitoring                     │
└───────────────────────────────────────────────────────────────────────────┘
```

