# Automated SEO Analytics Pipeline

An end-to-end data pipeline that automatically extracts search performance data from the Google Search Console API, transforms it with Pandas, and stores it in a SQLite database for long-term historical analysis.

> Unlike the native Google Search Console dashboard, this pipeline retains data indefinitely and enables custom SQL-based analysis — no manual exports required.

---

## Why This Project Exists

Google Search Console only displays data for the last 16 months and requires manual exports for any custom analysis. This pipeline solves both problems by automating extraction and storing results in a local database that grows over time.

---

## Architecture

```
Google Search Console API
        │
        ▼
  extract.py          ← Authenticates via OAuth 2.0 and pulls raw data
        │
        ▼
  transform.py        ← Cleans data, enforces types, calculates custom metrics
        │
        ▼
  load.py             ← Loads DataFrame into SQLite, supports SQL querying
        │
        ▼
  gsc_data.db         ← Local database for long-term historical storage
```

---

## Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| Language | Python 3.13 | Core scripting |
| Extraction | Google Search Console API | Raw search performance data |
| Auth | OAuth 2.0 (google-auth-oauthlib) | Secure API authentication |
| Transformation | Pandas | Data cleaning and custom metrics |
| Storage | SQLite | Lightweight local database |
| Environment | python-dotenv | Secrets management |
| Containerization | Docker | Portable deployment |

---

## Project Structure

```
seo-pipeline/
├── auth.py            # OAuth 2.0 authentication and token management
├── extract.py         # GSC API data extraction
├── transform.py       # Pandas transformation and custom metrics
├── load.py            # SQLite loading and SQL querying
├── credentials.json   # Google OAuth credentials (not committed to Git)
├── token.json         # Cached auth token (not committed to Git)
├── gsc_data.db        # SQLite database (auto-generated on run)
├── .env               # Environment variables (not committed to Git)
├── .gitignore         # Excludes secrets and generated files
├── Dockerfile         # Container definition
└── README.md          # This file
```

---

## Custom Metrics

Beyond raw GSC data, the pipeline calculates:

| Metric | Formula | Purpose |
|---|---|---|
| `ctr_percent` | `ctr × 100` | Human-readable click-through rate |

More custom metrics can be added in `transform.py`.

---

## Getting Started

### Prerequisites
- Python 3.10+
- A Google account with Search Console access
- A verified property in Google Search Console

### 1. Clone the repository
```bash
git clone https://github.com/HPSgh/seo-pipeline.git
cd seo-pipeline
```

### 2. Create and activate a virtual environment
```bash
python -m venv venv

# Windows
venv\Scripts\activate

# Mac/Linux
source venv/bin/activate
```

### 3. Install dependencies
```bash
pip install pandas google-auth-oauthlib google-auth-httplib2 google-api-python-client
```

### 4. Add your Google credentials
- Create a project in [Google Cloud Console](https://console.cloud.google.com)
- Enable the **Search Console API**
- Create an **OAuth 2.0 Desktop App** credential
- Download and rename it to `credentials.json`
- Place it in the project root

### 5. Configure your target site
In `extract.py`, update the `site_url` variable to match your verified GSC property:
```python
# URL prefix property
site_url = 'https://yoursite.com/'

# Domain property
site_url = 'sc-domain:yoursite.com'
```

### 6. Run the pipeline
```bash
# Step 1 — Authenticate (opens browser on first run)
python auth.py

# Step 2 — Extract, transform, and load
python load.py
```

On first run, a browser window will open for Google OAuth authentication. After approving, a `token.json` file is saved so subsequent runs are fully automated.

---

## Switching to a Different Site

To point the pipeline at a different website:
1. Ensure your Google account has access to that site's Search Console property
2. Update `site_url` in `extract.py` to the exact property URL
3. Re-run `python load.py`

---

## Running with Docker

```bash
docker build -t seo-pipeline .
docker run seo-pipeline
```

---

## Author

**Htoo Pyae Shan**
Software Engineering Student | Aspiring Data Analyst
[GitHub](https://github.com/HPSgh)
