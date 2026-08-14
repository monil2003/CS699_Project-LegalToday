# CS699_Project
# LegalToday: AI-Powered Legal Research Platform

LegalToday is an AI-driven legal research platform combining semantic
search, AI-generated case summaries, lawyer recommendations, legal news,
search history, and in-app judgment viewing.

## Features

-   **Semantic Legal Search** using vector embeddings and cosine
    similarity.
-   **AI Case Summarization** using the Google Gemini API.
-   **Parallel Summary Generation** using Python `ThreadPoolExecutor`,
    reducing reported processing time for 10 results from **15+ seconds
    to under 2 seconds**.
-   **Session-Based Search History** for authenticated users.
-   **Pagination** for search results.
-   **Lawyer Recommendations** based on query-derived practice areas,
    specialization, and ratings.
-   **Legal News Feed** using NewsAPI and configured Indian news
    sources.
-   **PDF Judgment Viewer** for reading judgments within the
    application.
-   **User Authentication** with registration, login, logout, sessions,
    and bcrypt password hashing.

## Technology Stack

  Component         Technology
  ----------------- -----------------------------
  Backend           Python, Flask
  Frontend          HTML, CSS, JavaScript
  Database          SQLite
  Embeddings        Google `text-embedding-004`
  Generative AI     Google Gemini API
  Authentication    Flask-Session, bcrypt
  Data Processing   Pandas, NumPy
  News              NewsAPI
  Concurrency       Python `ThreadPoolExecutor`

## Architecture

``` text
User
  |
  v
HTML / CSS / JavaScript
  |
  v
Flask API
  +--> Authentication / Sessions
  +--> Search & Pagination
  +--> Lawyer Recommendations
  +--> News Feed
  |
  +--> Embedding Logic --> SQLite
  |                         |
  |                         +--> Case metadata
  |                         +--> Case embeddings
  |
  +--> Gemini API
          +--> Query embeddings
          +--> Case summaries
```

The project presentation describes Flask as the backend API, SQLite as
the relational database and vector store, Gemini for embeddings and
summarization, and HTML/JavaScript for the frontend.

## Semantic Search Pipeline

1.  Accept a legal query from the user.
2.  Generate a query embedding using `text-embedding-004`.
3.  Retrieve stored case embeddings from SQLite.
4.  Compute cosine similarity using NumPy.
5.  Rank judgments by similarity.
6.  Paginate the ranked results.
7.  Generate concise, query-focused summaries with Gemini.
8.  Return summaries and case metadata to the frontend.

## AI Summarization

Gemini receives the retrieved case snippet and user query and generates
a concise two-sentence summary focused on the query.

Summary generation is parallelized with up to 10 workers:

``` python
with concurrent.futures.ThreadPoolExecutor(max_workers=10) as executor:
    ...
```

The project presentation reports reducing summary generation time for 10
results from **15+ seconds to under 2 seconds**.

## Data Initialization

On the first run, case data can be loaded from:

``` text
merged_scraped_data.csv
```

The application combines each case title and snippet, generates an
embedding, and stores the case metadata and serialized vector in SQLite.

Lawyer information is loaded from:

``` text
lawyers.csv
```

A Pandas-based cleaning pipeline was used to sanitize and normalize
scraped legal data and address duplicate/spurious records.

## Project Structure

``` text
LegalToday/
├── app.py
├── .env
├── merged_scraped_data.csv
├── lawyers.csv
├── users.db
├── templates/
└── static/
    └── pdfs/
```

## Environment Variables

Create a `.env` file:

``` env
GEMINI_API_KEY1=your_gemini_api_key
GEMINI_API_KEY2=your_gemini_api_key
GEMINI_API_KEY3=your_gemini_api_key
NEWS_API_KEY=your_newsapi_key
```

Never commit API keys or `.env` files to version control.

## Installation

``` bash
git clone <repository-url>
cd LegalToday

python3 -m venv venv
source venv/bin/activate

pip install flask flask-session python-dotenv pandas bcrypt google-generativeai numpy requests
```

Add the required CSV datasets and configure `.env`, then run:

``` bash
python3 app.py
```

## Authentication

The application supports registration, login, logout, session-based
authentication, bcrypt password hashing, and per-user search history.
User and history data are stored in SQLite.

## Legal News

The `/news` route uses NewsAPI to retrieve English-language legal news
from configured Indian news domains, sorts articles by publication time,
and displays the results through the web interface.

## Challenges and Solutions

### AI Latency

Sequential summary generation for 10 results required **15+ seconds**.
`ThreadPoolExecutor` was used to issue requests concurrently, reducing
the reported time to **under 2 seconds**.

### Frontend State

A history dropdown could close before clicks registered because of a
`blur` event. A `setTimeout` delay was added to allow event propagation.

### Data Consistency

Scraping disparate legal sources produced duplicate and spurious
records. A Pandas cleaning script was used to sanitize and normalize the
dataset.

## Limitations

-   Dependent on external API quotas.
-   English-language support only.

## Future Scope

-   Chat-with-PDF functionality.
-   Multilingual support for regional languages.
-   OCR for scanned PDF documents.

## Project Highlights

-   Semantic vector search for legal judgments.
-   Gemini-powered query-focused case summarization.
-   Reported **15+ seconds → \<2 seconds** summary-generation
    improvement for 10 results.
-   Integrated legal search, judgment viewing, lawyer recommendations,
    search history, and legal news.
-   Flask + SQLite backend with session-based authentication.
