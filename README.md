# User Data Extractor

User Data Extractor is a small, flexible tool to extract, normalize, and export user information from a variety of inputs (CSV, JSON, HTML pages, APIs, etc.). It is intended to help developers and data analysts quickly turn raw user lists or pages into clean, structured datasets for analysis, migrations, and reporting.

Key goals:
- Fast extraction from common formats (CSV/TSV/JSON/HTML).
- Normalization of common user fields (name, email, username, location, company).
- Pluggable "source" extractors so you can add support for new input types or custom APIs.
- Output to JSON/CSV/NDJSON for easy downstream consumption.

Features
- CLI for quick one-off runs.
- Library API for embedding into scripts or other tools.
- Configurable field mapping and normalization rules.
- Logging and simple error handling.
- Example extractors for CSV and simple HTML pages.

Status
- Template/initial implementation (customize extractors for specific sites/APIs).
- See USAGE.md for full usage examples and configuration.

Table of contents
- Requirements
- Installation
- Quick start
- CLI usage
- Library usage
- Configuration
- Examples
- Contributing
- License

Requirements
- Python 3.8+
- pip
- (Optional) Docker for containerized runs

Installation

1. Clone the repository
   git clone https://github.com/SazzzNiziyan/User-data-Extractor.git
   cd User-data-Extractor

2. Create a virtual environment (recommended)
   python3 -m venv .venv
   source .venv/bin/activate

3. Install dependencies
   pip install -r requirements.txt
   # If this project is packaged:
   pip install -e .

Quick start (CLI)
- Example: extract from a CSV and write JSON output
  python -m user_data_extractor.cli --input data/users.csv --input-format csv --output results/out.json --fields name,email,username,company

- Example using Docker
  docker build -t user-data-extractor .
  docker run --rm -v "$(pwd)/data:/data" user-data-extractor --input /data/users.csv --input-format csv --output /data/out.json

If your project exposes a single script (e.g. extract.py), swap the module command above with:
  python extract.py --help

CLI usage
Run:
  python -m user_data_extractor.cli --help

Common options (example)
  --input / -i            Path to input file or directory (required)
  --input-format -f       Input format: csv, json, html, api (auto-detect if omitted)
  --source                Source identifier or extractor name (e.g. github_users, generic_csv)
  --output / -o           Path to output file (json/csv). Defaults to stdout.
  --output-format         json, csv, ndjson (defaults to json)
  --fields                Comma-separated list of fields to include (name,email,username,...)
  --config                Path to YAML/JSON configuration file with field mappings
  --threads               Number of worker threads for parallel extraction
  --verbose / -v          Increase logging verbosity
  --dry-run               Parse & validate but don't write output

Library usage (Python)
Example:
  from user_data_extractor import Extractor, formats
  extractor = Extractor(source="csv", config={"field_map": {"fullname": "name"}})
  records = extractor.extract("data/users.csv")
  # records is a list of normalized dicts
  extractor.write(records, "results/out.json", format="json")

Configuration
- field mapping: map input column names to normalized field names
- normalization: control how names, emails, and usernames are cleaned
- rate limits: when using API extractors, set rate limits and retries
- output schema: whitelist/ordering of output fields

Examples
- CSV -> JSON:
  python -m user_data_extractor.cli -i data/users.csv -f csv -o results/users.json --fields name,email,location

- Scrape HTML directory -> NDJSON:
  python -m user_data_extractor.cli -i data/html_pages/ -f html -o results
