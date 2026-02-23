# Scrapy Web Scraper

A [Scrapy](https://scrapy.org/) project that scrapes book listings from [Books to Scrape](https://books.toscrape.com/) and stores them in MongoDB.

## Features

- **Book spider** – Crawls the Books to Scrape catalog and extracts URL, title, and price for each book
- **Pagination** – Follows “next page” links to scrape the full catalog
- **MongoDB pipeline** – Saves items to MongoDB with upsert (deduplication by URL hash)
- **Politeness** – Obeys `robots.txt`, uses download delay and per-domain concurrency limits
- **Retries** – Retries on HTTP 500 and 429
- **Tests** – Unit tests for the book spider (contract and parsing)

## Project structure

```
ScrapyWebScraper/
├── books/                    # Scrapy project
│   ├── books/
│   │   ├── spiders/
│   │   │   └── book.py       # Book spider
│   │   ├── items.py          # BooksItem definition
│   │   ├── pipelines.py      # MongoDB pipeline
│   │   └── settings.py       # Scrapy settings
│   ├── scrapy.cfg
│   └── tests/
│       └── test_book.py      # Spider tests
├── requirements.txt
└── README.md
```

## Requirements

- Python 3.x
- MongoDB (local or remote)

## Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/Abderrahmane102/LibraryScraper.git
   cd LibraryScraper
   ```

2. **Create and activate a virtual environment** (recommended)

   ```bash
   python -m venv venv
   # Windows
   venv\Scripts\activate
   # Linux/macOS
   source venv/bin/activate
   ```

3. **Install dependencies**

   ```bash
   pip install -r requirements.txt
   ```

   If you use the random user agent middleware (see `books/settings.py`), also install:

   ```bash
   pip install scrapy-user-agents
   ```

4. **Start MongoDB** (if running locally)

   Default settings expect MongoDB at `mongodb://localhost:27017/`. Adjust `MONGO_URI` and `MONGO_DATABASE` in `books/books/settings.py` if needed.

## Usage

From the project root, run the spider from the `books` directory:

```bash
cd books
scrapy crawl book
```

Data is written to the MongoDB database and collection configured in `books/books/settings.py` (`books_db` and `books` by default). Logs go to `books/book_scraper.log` (level `WARNING`).

### Export to file (optional)

To export to JSON or CSV instead of (or in addition to) MongoDB, use Scrapy’s feed exports:

```bash
cd books
scrapy crawl book -o books.json
# or
scrapy crawl book -o books.csv
```

## Configuration

Key settings in `books/books/settings.py`:

| Setting | Default | Description |
|--------|---------|-------------|
| `MONGO_URI` | `mongodb://localhost:27017/` | MongoDB connection URI |
| `MONGO_DATABASE` | `books_db` | Database name |
| `DOWNLOAD_DELAY` | `2` | Delay (seconds) between requests |
| `CONCURRENT_REQUESTS_PER_DOMAIN` | `1` | Concurrent requests per domain |
| `ROBOTSTXT_OBEY` | `True` | Whether to obey robots.txt |
| `LOG_LEVEL` | `WARNING` | Logging level |
| `LOG_FILE` | `book_scraper.log` | Log file path |

## Running tests

From the project root:

```bash
cd books
python -m pytest tests/ -v
```

Or with unittest:

```bash
cd books
python -m unittest discover -s tests -v
```

