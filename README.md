# Movie Database Project

## The Story Behind This Project

This project demonstrates something powerful: **how proper assistant instructions transform Databricks Genie from a basic code generator into a production-quality development partner**.

Every line of code in this project was generated through natural language prompts to Genie — **I didn't write any code manually**. But the key wasn't just prompting. It was building comprehensive assistant instructions that taught Genie about:

* Code quality standards (docstrings, type hints, error handling)
* SQL formatting conventions (clause structure, commenting, Unity Catalog documentation)
* Data engineering patterns (bronze/silver/gold architecture, split pipelines)
* Best practices for notebooks, imports, and documentation

The result? Production-quality code that follows consistent standards, includes proper documentation, and implements real-world data engineering patterns — all generated through conversation.

## What You'll Learn

This project is educational on two levels:

1. **Building Better Assistant Instructions**: See how a well-structured `.assistant_instructions.md` file and custom skills guide Genie to produce higher-quality code
2. **Real-World Data Engineering**: Explore a modular data pipeline with bronze/silver/gold layers, API integration, and analytical queries

## Project Overview

End-to-end data pipeline ingesting movie data from The Movie Database (TMDB) API and transforming it through bronze, silver, and gold layers for analytics.

### Data Pipeline Architecture

**Bronze Layer** (Raw Data):
* `workspace.default.movies_bronze` - Raw movie data from multiple TMDB endpoints (~15-20K movies after deduplication)
* `workspace.default.movies_bronze_genres` - Genre ID to name mapping

**Silver Layer** (Cleaned Data):
* `workspace.default.movies_silver` - Cleaned and enriched movie data with derived fields

**Gold Layer** (Analytics-Ready):
* `workspace.default.movies_gold_by_genre` - Aggregated metrics by genre (with human-readable names)
* `workspace.default.movies_gold_by_decade` - Aggregated metrics by decade

### Modular Pipeline Design

The pipeline follows production best practices with **separate notebooks for each layer**:

1. **`01-bronze-ingestion`** (Python) - API calls and bronze table creation
2. **`02-silver-transformations`** (SQL) - Data cleaning and normalization
3. **`03-gold-aggregations`** (SQL) - Business-ready aggregations
4. **`04-analysis-queries`** (SQL) - Ad-hoc analytical queries

**Benefits of this architecture:**
* Clear separation of concerns
* Easier debugging (isolate failures to specific stages)
* Independent testing and development
* Better version control (smaller diffs, clearer changes)
* Reusability (downstream notebooks can work with different sources)
* Parallel development (team members can work on different stages)

### Data Sources

The pipeline fetches from multiple TMDB API endpoints using different sort orders for diversity:
* `/discover/movie` - Movies released since 2000 (400 pages each with 3 sort orders: popularity, release_date, vote_count)
* `/movie/popular` - Currently popular movies (5 pages)
* `/movie/top_rated` - Highest-rated movies (5 pages)
* `/movie/now_playing` - Currently in theaters (3 pages)
* `/movie/upcoming` - Coming soon (3 pages)

Total dataset: ~15-20K unique movies after deduplication

### Analysis Queries

Five compelling analytical queries demonstrate real insights:

1. **Top Genres by Rating**: Which genres consistently produce the highest-rated movies
2. **Decade Trends**: How movie production volume and quality have evolved over time
3. **Top Rated Movies**: The highest-rated films with credible vote counts
4. **Rating vs Popularity**: Correlation analysis across popularity tiers
5. **Language Diversity**: International cinema representation since 2010

## The Assistant Instructions Approach

### What Makes This Different?

Out-of-the-box, Genie generates functional code. But with comprehensive assistant instructions, it generates:

* **Consistently formatted code** following team standards
* **Comprehensive documentation** (docstrings, comments, table comments)
* **Proper error handling** with informative messages
* **Production patterns** (DRY principles, modular design, type safety, split architectures)
* **Best practices** (Unity Catalog documentation, SQL formatting, security)

### The Assistant Instructions and Skills

See `.assistant_instructions.md` and the `.assistant/skills/` directory in the repository root. These files guide Genie's behavior and provide domain-specific expertise.

**Assistant Instructions** (`.assistant_instructions.md`) include:

* **Python Guidelines**: Docstring templates, type hints, import organization, error handling
* **SQL Guidelines**: Clause formatting, commenting, Unity Catalog best practices
* **Data Pipeline Architecture**: Modular design, naming conventions, medallion architecture patterns
* **Code Quality**: Configuration management, reusability, performance considerations
* **Documentation Standards**: README maintenance, in-code comments, notebook structure

**Skills Directory** (`.assistant/skills/`) contains domain-specific knowledge modules that Genie can reference for specialized tasks.

These instructions accumulate over time as you work with Genie, creating a living style guide for your workspace.

### The Methodology

1. **Start with instructions**: Define your standards before coding
2. **Use natural language prompts**: Describe what you want, not how to build it
3. **Review and refine**: When Genie's output needs adjustment, update instructions
4. **Build incrementally**: Start simple, add complexity as patterns emerge
5. **Share the file**: Team members benefit from accumulated knowledge

## Prerequisites

* Python 3.8+
* Databricks workspace (free tier supported)
* TMDB API account (free)
* Databricks CLI

## Setup Instructions

### 1. Set Up Assistant Instructions (Important)

To use this project with your own Databricks workspace, you need to copy the assistant configuration files to your home directory:

**Required files:**
* `.assistant_instructions.md` - Core assistant behavior and coding standards
* `.assistant/skills/` - Domain-specific knowledge modules

**Setup steps:**

1. Copy the assistant instructions file to your Databricks home directory:
   ```bash
   # From your local machine (if using Databricks CLI)
   databricks workspace import ../.assistant_instructions.md /Users/your-email@domain.com/.assistant_instructions.md
   ```

2. Copy the skills directory to your Databricks home directory:
   ```bash
   # From your local machine (if using Databricks CLI)
   databricks workspace import-dir ../.assistant/skills /Users/your-email@domain.com/.assistant/skills
   ```

   Or manually in Databricks UI:
   * Navigate to your home directory (`/Users/your-email@domain.com/`)
   * Upload `.assistant_instructions.md` to your home directory
   * Create a `.assistant` folder in your home directory
   * Upload the `skills/` folder into the `.assistant` folder

**Why this matters:** The assistant instructions file tells Genie how to write code according to your standards. Without copying it to your home directory, Genie will use default behavior and won't follow the patterns demonstrated in this project.

---

### 2. Get TMDB API Key

1. Sign up for a free account at [https://www.themoviedb.org/signup](https://www.themoviedb.org/signup)
2. Navigate to your account settings → API
3. Request an API key (select "Developer" option)
4. Copy your API key for use in the next steps

### 3. Install Databricks CLI

Install the Databricks CLI using pip:

```bash
pip install databricks-cli
```

Verify installation:

```bash
databricks --version
```

### 4. Configure Databricks CLI

#### Generate a Personal Access Token

1. In your Databricks workspace, go to **Settings → Developer → Access tokens**
2. Click **Generate new token**
3. Give it a name (e.g., "CLI Access")
4. Set expiration (or leave blank for no expiration)
5. Click **Generate** and copy the token immediately

#### Create `.databrickscfg` File

Create or edit `~/.databrickscfg` in your home directory:

```ini
[DEFAULT]
host = https://your-workspace-url.cloud.databricks.com
token = your-personal-access-token-here
```

Replace:
* `your-workspace-url` with your actual Databricks workspace URL
* `your-personal-access-token-here` with the token you generated

**Security Note**: Never commit `.databrickscfg` to version control. Add it to your `.gitignore`.

### 5. Create Databricks Secret Scope

Create a secret scope to store your TMDB API key securely:

```bash
databricks secrets create-scope tmdb
```

### 6. Store TMDB API Key in Secrets

Add your TMDB API key to the secret scope:

```bash
databricks secrets put-secret tmdb api_key
```

This will open your default text editor. Paste your TMDB API key, save, and close the editor.

**Alternative**: Use the `--string-value` flag to provide the key directly:

```bash
databricks secrets put-secret tmdb api_key --string-value "your-tmdb-api-key-here"
```

### 7. Verify Secret Configuration

Verify your secret was created successfully:

```bash
# List all secret scopes
databricks secrets list-scopes

# List secrets in the tmdb scope (shows key names, not values)
databricks secrets list-secrets --scope tmdb
```

You should see `api_key` listed in the tmdb scope.

## Running the Pipeline

The pipeline is split into 4 notebooks that should be run **in order**:

### 1. Bronze Layer - Raw Data Ingestion

**Notebook**: `01-bronze-ingestion`

**Purpose**: Fetch raw movie data from TMDB API and create bronze tables

**What it does**:
* Initializes the MovieAPIClient with your API key
* Fetches movies from multiple endpoints with different sort orders (popularity, release_date, vote_count)
* Fetches genre mapping data
* Deduplicates movies by ID
* Creates `movies_bronze` and `movies_bronze_genres` tables

**Runtime**: 10-15 minutes on first run (fetches 1,200+ API pages)

**Output**:
* `workspace.default.movies_bronze` (~15-20K movies)
* `workspace.default.movies_bronze_genres` (19 genres)

---

### 2. Silver Layer - Clean and Normalize

**Notebook**: `02-silver-transformations`

**Purpose**: Transform bronze data into cleaned, analytics-ready silver tables

**What it does**:
* Filters out records with missing critical fields
* Handles NULL values with COALESCE
* Extracts `release_year` from `release_date`
* Calculates `release_decade` for trend analysis
* Preserves `genre_ids` as JSON for downstream processing

**Runtime**: <1 minute

**Output**:
* `workspace.default.movies_silver`

---

### 3. Gold Layer - Business Aggregations

**Notebook**: `03-gold-aggregations`

**Purpose**: Create business-ready aggregate tables optimized for analytics

**What it does**:
* Explodes genre arrays and joins with genre names
* Aggregates movies by genre (count, avg rating, popularity, votes)
* Aggregates movies by decade for trend analysis
* Creates denormalized tables for fast querying

**Runtime**: <1 minute

**Output**:
* `workspace.default.movies_gold_by_genre`
* `workspace.default.movies_gold_by_decade`

---

### 4. Analysis Queries

**Notebook**: `04-analysis-queries`

**Purpose**: Demonstrate analytical insights from gold tables

**What it does**:
* Top Genres by Rating (which genres produce best movies)
* Decade Trends (how metrics evolved over time)
* Top Rated Movies (highest-rated films with credible votes)
* Rating vs Popularity (correlation analysis)
* Language Diversity (international cinema representation)

**Runtime**: <1 minute total

**Output**: Query results for analysis and visualization

---

### Quick Start

1. Open `01-bronze-ingestion` in Databricks
2. Attach to serverless compute or a cluster
3. Run all cells (this will take 10-15 minutes)
4. Open `02-silver-transformations` and run all cells
5. Open `03-gold-aggregations` and run all cells
6. Open `04-analysis-queries` and run all cells to see insights

**Note**: The notebooks are designed to be idempotent — you can rerun them safely. Tables are created with `CREATE OR REPLACE`.

## Project Structure

```
movie-database-project/
├── README.md                      # This file
├── 01-bronze-ingestion            # Bronze layer: API ingestion (Python)
├── 02-silver-transformations      # Silver layer: Cleaning/normalization (SQL)
├── 03-gold-aggregations           # Gold layer: Business aggregations (SQL)
├── 04-analysis-queries            # Analysis: Ad-hoc queries (SQL)
├── movie-data-pipeline            # Original monolithic notebook (deprecated)
├── ../.assistant_instructions.md  # Assistant instructions (one level up)
└── ../.assistant/skills/          # Skills directory (one level up)
```

**Note**: The original `movie-data-pipeline` notebook is kept for reference but the split architecture (01-04) is now the recommended approach.

## Code Quality Standards

This project follows strict coding standards defined in `.assistant_instructions.md`:

* **Python**: Google-style docstrings, type hints, PEP 8 imports, alphabetically sorted multi-line imports
* **SQL**: Clause-per-line formatting, 2-space column indentation, separate-line comments
* **Documentation**: All Unity Catalog tables include COMMENT with purpose, refresh frequency, and source lineage
* **Error Handling**: Specific exceptions, early validation, informative error messages
* **Security**: No hardcoded credentials, all sensitive data in Databricks secrets
* **Architecture**: Split notebooks per layer with numeric prefixes indicating execution order

## Sample Insights

Run the analysis queries to discover:

1. **Top Genres by Rating**: Documentary (7.3), Animation (7.07), History (6.99) lead in quality
2. **Decade Trends**: Massive growth from 25 movies in 1990s to 11,307 in 2020s
3. **Top Rated Movies**: Classics like The Shawshank Redemption (8.72), The Godfather (8.69) dominate
4. **Popularity vs Quality**: High popularity doesn't always mean high ratings
5. **Global Cinema**: English dominates but significant Korean, Japanese, and Spanish representation

## Troubleshooting

### "Secret does not exist" Error

**Cause**: The secret scope or key wasn't created properly.

**Solution**:
```bash
databricks secrets list-scopes
databricks secrets list-secrets --scope tmdb
```

If missing, re-run the secret creation commands in Step 5-6.

### "API key cannot be empty" Error

**Cause**: The API key wasn't retrieved from secrets correctly.

**Solution**: Verify the secret exists and contains your API key:
```bash
databricks secrets list-secrets --scope tmdb
```

If the secret is missing, re-run step 6 to store your API key in the secret scope.

### TMDB API Rate Limiting

**Cause**: Too many requests to TMDB API.

**Solution**: The free tier allows 1,000+ requests per day. If you hit limits, reduce the `pages` parameter in `01-bronze-ingestion`, cell 6:
```python
# Instead of 400 pages, try 100
modern_popular = client.get_movies_by_year_range(
    start_year=2000, end_year=2026, pages=100, sort_by="popularity.desc"  # Reduced from 400
)
```

### Bronze Ingestion Takes Too Long

**Cause**: Fetching 15-20K movies takes time on the first run.

**Solution**: This is expected. The initial ingestion takes 10-15 minutes due to 1,200+ API pages. Subsequent reruns are faster. You can reduce volume by decreasing page counts in `01-bronze-ingestion`.

### Table Not Found in Downstream Notebooks

**Cause**: Upstream notebook wasn't run or failed.

**Solution**: Ensure you've run the notebooks in order:
1. `01-bronze-ingestion` (creates bronze tables)
2. `02-silver-transformations` (requires bronze tables)
3. `03-gold-aggregations` (requires silver tables)
4. `04-analysis-queries` (requires silver/gold tables)

## Security Best Practices

* ✅ API keys stored in Databricks Secrets (not in code)
* ✅ `.databrickscfg` excluded from version control
* ✅ No hardcoded credentials in notebooks
* ⚠️ Always use Databricks secrets for sensitive credentials - never hardcode API keys or tokens in your code

## Next Steps

### Enhance the Pipeline
* Add scheduling via Databricks Workflows to refresh data automatically
* Implement incremental loading (only fetch new movies since last run)
* Add data quality tests (schema validation, null checks, anomaly detection)
* Include movie cast/crew data for richer analysis
* Add ML model to predict movie success based on features
* Create Delta Live Tables version for continuous processing

### Enhance the Assistant Instructions
* Add conventions for your team's specific patterns
* Document common data sources and join keys in Agent Memories section
* Define business metrics and their calculations
* Add examples of good vs bad code patterns
* Create custom skill files for specialized domain knowledge

### Share Your Experience
* Write about your assistant instructions journey
* Share patterns that improved code quality
* Document surprising insights from Genie
* Contribute examples to the community

## What Makes This Approach Effective?

### Consistency
Every piece of Genie-generated code follows the same standards. No more "this function has docstrings, that one doesn't."

### Knowledge Transfer
New team members read the instructions once and understand your standards. Genie enforces them automatically.

### Continuous Improvement
As you discover better patterns, update instructions. Future code benefits immediately.

### Reduced Review Friction
Code reviews focus on logic and design, not formatting and documentation style.

### Scalable Quality
Quality scales with the team. More people prompting Genie = more consistent output, not more variation.

### Modular Development
Split notebooks enable parallel development and easier debugging. Team members can work on different layers simultaneously.

## References

* [TMDB API Documentation](https://developers.themoviedb.org/3)
* [Databricks CLI Documentation](https://docs.databricks.com/dev-tools/cli/index.html)
* [Unity Catalog Best Practices](https://docs.databricks.com/data-governance/unity-catalog/index.html)
* [Databricks Genie Documentation](https://docs.databricks.com/genie/index.html)
* [Delta Live Tables Documentation](https://docs.databricks.com/workflows/delta-live-tables/index.html)

## License

This project is for educational purposes. TMDB data is subject to [TMDB Terms of Use](https://www.themoviedb.org/terms-of-use).

---

## About This README

This README was also generated through conversation with Genie, following the documentation standards in `.assistant_instructions.md`. Notice the structure, the progression from "why" to "how," and the balance of educational content with practical instructions. That's what good assistant instructions enable — quality output across all artifacts, not just code.