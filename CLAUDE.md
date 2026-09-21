# Aforo

Multi-domain data platform. Bootstrap domain: cultural heritage.

## Architecture rules

- The core (`src/aforo/`) is domain-agnostic. It must never
  reference a specific domain, provider or dataset. No `culture`,
  `museum`, `europeana`, `wikimedia` or `wikidata` anywhere under
  `src/aforo/`.
- Dependencies point one way: `domains/` imports from the core,
  never the reverse.
- Raw data is partitioned by provider and dataset from the first
  write, and is never modified after it lands. Domains own what is
  derived from raw data, not the raw data itself: a single provider
  can feed more than one domain.
- Ingestion contracts are defined independently of any concrete source.

## Conventions

- Everything in English: code, commits, docs, DAG ids.
- Python managed with `uv`. Orchestration: Airflow 3.
  Transformation: dbt on DuckDB.
- Architecture decisions go in `docs/adr/`, numbered, written
  before the code they justify.

## Data sources

Each source carries its own licence and attribution requirements.
See `DATA_SOURCES.md` before adding or redistributing anything.