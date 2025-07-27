# Data Engineering Part 2 - Exercise

## What you’ll build
You will create a small, end‑to‑end data pipeline in a Jupyter notebook using the Open Brewery DB (no auth):

1. Extract: Use Python requests to GET brewery data, organize it into a light 3rd Normal Form (3NF), and save CSVs to the `extraction-pipeline` folder.
2. Transform: Load those CSVs, compute simple trend aggregations, and save one tidy CSV to the `transformation-pipeline` folder.
3. Visualize: Read the aggregated CSV and produce three matplotlib charts.

Write one class per step (one class per notebook cell), then a final cell that runs the whole pipeline and shows the charts.

## Web API you must use
Open Brewery DB v1 Base URL: https://api.openbrewerydb.org/v1/breweries

Use HTTP GET with query params (e.g., per_page, page). No API key needed.

Tip: Start with a small page size to explore, then scale up. Handle empty pages gracefully.

## Constraints & guardrails
Notebook structure

- Cell A: Extractor class (API → 3NF CSVs)
- Cell B: Transformer class (3NF CSVs → aggregated CSV)
- Cell C: Visualizer class (aggregated CSV → 3 charts)
- Cell D: Orchestration (instantiate and run everything)

Libraries to use: 
requests, pandas, matplotlib

### `requests` usage: 
`requests` is a Python library that makes it easier to send and manage HTTP requests. 

### Guidance on using the `requests` library
For any HTTP request you make to the Open Brewery DB v1 API, you should
- Use requests.get(url, params=..., timeout=...), where `url` is the Web API route you want to get info from and `params` are any URL params you need to pass throughn in your request to the Web API
- Check response.status_code
- Call response.raise_for_status()
- Then, return response.json().

Here is a quickstart guide to the `request` library: [Quickstart Guide](https://requests.readthedocs.io/en/latest/user/quickstart/)

## Deliverables
1. `extraction-pipeline` contains your 3NF CSVs (names are your choice; structure matters).
2. `transformation-pipeline` has a CSV file in it named `aggregated_metrics.csv`, where its table data is in tidy/long form (at minimum, you should have these following columns in the csv: metric, dimension, dim_value, value).
3. Three matplotlib charts generated only from the aggregated CSV.
4. A final cell that runs extract → transform → visualize without manual edits in between.

## Modeling (you decide the exact columns)
Model a simple 3NF that separates entities from labels:

- Consider a fact table for breweries (unique brewery_id, name, city).
- Consider dimensions for state and brewery_type so repeated text isn’t duplicated in the fact.
- Create stable keys for your dims (e.g., a lowercased/slugged version of state/type). Make a clear choice for handling missing/blank values (e.g., use "unknown").

Acceptance checks (Extraction):
• Each table has a clear primary key (no duplicates).
• Foreign keys match referenced primary keys (and share the same dtype).
• CSVs exist in `extraction-pipeline`.


## Transformations (choose at least three)
Pick three basic aggregations that tell a story. For example:

- breweries_per_state — count per state.
- breweries_per_type — count per brewery type.
- top_cities_by_breweries — top 10 cities by count.

Combine all metrics into one tidy CSV with columns like:

``` 
metric,dimension,dim_value,value
breweries_per_state,state,Texas,423
breweries_per_type,brewery_type,micro,1820
top_cities_by_breweries,city,Portland,64
```

Acceptance checks (Transformation):
• Single tidy CSV in transformation-pipeline/.
• Group‑bys and joins are correct; types align.
• Missing values are handled consistently (document your choice in a short comment).


## Visualizations (three charts)
From the aggregated CSV, create three clear charts, e.g.:

- Bar chart: Top 10 states by brewery count.
- Bar chart: Brewery count by type.
- Horizontal bar chart: Top 10 cities by brewery count.

Chart hygiene:

- Informative titles, axis labels, readable tick labels (rotate if necessary).
- Keep styling simple; clarity over aesthetics.

Acceptance checks (Visualization):
• Three charts render when the final cell runs.
• Charts are built only from the aggregated CSV (no direct API reads here).

## Orchestration (final cell)
Your last cell should:

- Instantiate and run Extractor → writes CSVs to extraction-pipeline/.
- Instantiate and run Transformer → writes one CSV to transformation-pipeline/.
- Instantiate Visualizer, load the aggregated CSV, render three charts.

Keep it short and readable (think “main” function).

## Questions to guide your thinking
- Which fields from the API belong in the fact table vs. dimensions?
- What is a good primary key for each table?
- How will you handle missing state or brewery_type?
- Which aggregations best show trends or patterns in this dataset?
- How will you keep your aggregated CSV tidy and consistent across metrics?

## Checklist (before you submit)
- extraction-pipeline/ and transformation-pipeline/ exist with expected CSVs.
- 3NF tables have clear keys; FKs line up with PKs.
- Aggregated CSV includes at least three metrics and is tidy/long.
- Three matplotlib charts render from the aggregated CSV.
- Classes have docstrings and minimal, helpful logging.
- `requests` calls use params, timeout, and raise_for_status().
- Notebook runs top‑to‑bottom cleanly on a fresh kernel.

## Troubleshooting tips
- Pagination: Use page and per_page; stop when a page returns an empty list.
- Joins: Ensure key columns share the same dtype (e.g., all strings).
- Missing values: Decide on a policy (e.g., replace with "unknown") and apply it consistently.
- Weird counts: Print intermediate DataFrames (head()) to verify joins and group‑bys.
- HTTP errors: Print the URL and params when something fails; include timeout.

## Goal: Don’t just make it “work”
Make clear, justifiable choices. 
Document them briefly in comments so another engineer could follow your reasoning. 
Good luck! 