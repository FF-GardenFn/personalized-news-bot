# News Bot Architecture: Type-S

## Overview

Type-S (Sequential) represents the initial architecture of the News Bot system. It follows a sequential processing model where components execute in order, with data flowing linearly through the pipeline. This architecture served as the foundation for the more advanced Type-H (Hierarchical) architecture.

For a visual representation of the Type-S architecture, please refer to the [architecture diagram](architecture_diagram.md).

For details on the migration from Type-S to Type-H, please see the [migration document](migration.md).
## System Components

### 1. Core Components
The core functionality of the application is divided into several modules:

#### 1.1 Data Models (`models.py`)
Defines the data structures used throughout the application:
- `NewsItem`: Represents a news article with properties like headline, source, content, URL, etc.
- `WeatherData`: Represents weather information with properties like description, temperature, etc.
- `MarketData`: Represents financial market data with properties like symbol, price, change percent, etc.
- `Recipient`: Represents a recipient of the news updates with properties like email, name, profession, interests, etc.
- `EmailContent`: Represents the content of an email to be sent, including personalized news, weather, and a quote.
- `APIResponse`: A generic class for API responses with success status, data, and error information.

#### 1.2 News Fetching (`fetch_news.py`)
Responsible for retrieving news from various sources:
- General news from NewsAPI
- Tech news from Hacker News
- Political news from Politico RSS feed
- Research papers from arXiv
- News from various RSS feeds (Reuters, BBC, CNN, TechCrunch, Wired)

#### 1.3 Weather Fetching (`fetch_weather.py`)
Retrieves weather information from OpenWeatherMap API:
- Current weather for a specific city
- Weather forecast for a specific city

#### 1.4 Financial Data Fetching (`fetch_finance.py`)
Retrieves financial data from Alpha Vantage API:
- Market data for specific stock symbols
- Financial news articles
- Cryptocurrency prices

#### 1.5 News Summarization (`summarise.py`)
Uses OpenAI's GPT to create personalized news summaries:
- Generates a detailed prompt with recipient information and news items
- Calls the OpenAI API to generate a personalized news briefing
- Logs the interaction for monitoring
- Also provides a function to fetch inspirational quotes

### 2. Input/Output Components

#### 2.1 Configuration (`config.py`)
Contains configuration settings for the application:
- API keys and credentials
- API URLs
- GPT configuration
- Email configuration
- Default market symbols
- Limits on the number of articles

#### 2.2 Caching (`cache.py`)
Implements a simple file-based caching system:
- Stores API responses to reduce API calls
- Supports expiration times for cached data
- Provides methods for getting, setting, and clearing cache

#### 2.3 Email Sending (`email_sender.py`)
Handles sending emails to recipients:
- Creates an authenticated SMTP connection
- Generates email content with personalized news, weather, and a quote
- Sends emails to recipients
- Supports both SSL and STARTTLS for secure email transmission

### 3. User Interface

#### 3.1 Command-Line Interface (`main.py`)
Provides a command-line interface for running the application:
- Parses command-line arguments (--no-cache, --recipient, --test)
- Loads recipient data from YAML files
- Processes each recipient by fetching news, personalizing it, and sending an email
- Logs the process for monitoring

### 4. Data Storage

#### 4.1 Recipient Configuration (`data/*.yml`)
Stores recipient information in YAML files:
- Email address
- Name
- Profession
- Interests
- Market symbols
- Location

## Information Flow

1. **User Input**: The user runs the application through the command-line interface, optionally specifying arguments like --no-cache, --recipient, or --test.

2. **Recipient Loading**: The application loads recipient information from YAML files in the data directory.

3. **Data Fetching**:
   - Weather data is fetched for each recipient's location
   - News items are fetched from various sources based on the recipient's interests
   - Market news is fetched for the recipient's market symbols
   - Financial news is fetched if the recipient is interested in finance

4. **Data Processing**:
   - News items are limited to a maximum number
   - News is personalized using GPT based on the recipient's profession and interests
   - An inspirational quote is fetched

5. **Email Creation and Sending**:
   - Email content is created with the personalized news, weather, and quote
   - The email is sent to the recipient

6. **Logging and Caching**:
   - API responses are cached to reduce API calls
   - GPT interactions are logged for monitoring
   - The process is logged for debugging and monitoring

## Data Processing Pipeline

1. **Data Collection**:
   - Fetch news from various sources
   - Fetch weather data
   - Fetch financial data
   - All data is cached to improve performance

2. **Data Filtering**:
   - Filter news based on recipient interests
   - Limit the number of news items

3. **Data Personalization**:
   - Create a detailed prompt with recipient information and news items
   - Use GPT to generate a personalized news briefing
   - Add weather information and an inspirational quote

4. **Data Delivery**:
   - Create email content with the personalized data
   - Send the email to the recipient

## External Integrations

1. **News Sources**:
   - NewsAPI for general news
   - Hacker News API for tech news
   - Politico RSS feed for political news
   - arXiv API for research papers
   - Various RSS feeds (Reuters, BBC, CNN, TechCrunch, Wired)

2. **Weather API**:
   - OpenWeatherMap API for weather information

3. **Financial APIs**:
   - Alpha Vantage API for market data and financial news

4. **AI Integration**:
   - OpenAI API for GPT-powered news summarization

5. **Email Service**:
   - SMTP server (e.g., Yahoo Mail) for sending emails


## Error Handling

The application includes error handling at various levels:
- API errors are caught and logged
- Fallback mechanisms are provided for when APIs fail
- Email sending errors are caught and logged

## Extensibility

The application was designed with improvement in mind hence easily extendable:
1. **Adding New News Sources**:
   - Add the API URL to `config.py`
   - Create a new function in `fetch_news.py` to fetch news from the source
   - Add the function to `get_all_news()` in `fetch_news.py`

2. **Customizing GPT Prompts**:
   - Modify the `personalize_news()` function in `summarise.py`

3. **Adding New Recipients**:
   - Create a new YAML file in the `data` directory with the recipient's information

See [architecture](architecture_diagram.md) for a diagram representation. 
