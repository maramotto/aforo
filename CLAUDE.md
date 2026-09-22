# Aforo

Multi-domain data platform. Bootstrap domain: cultural heritage.

## Architecture rules

- The core (`src/aforo/core/`) is domain-agnostic. It must never
  reference a specific domain, provider or dataset. No `culture`,
  `museum`, `europeana`, `wikimedia` or `wikidata` anywhere under
  `src/aforo/core/`.
- Dependencies point one way: `domains/` imports from the core,
  never the reverse.
- Raw data is partitioned by provider and dataset from the first
  write, and is never modified after it lands. Domains own what is
  derived from raw data, not the raw data itself: a single provider
  can feed more than one domain.
- Ingestion contracts are defined independently of any concrete source.
  A source with no logic of its own gets no code: it is described in
  `config/` (endpoint, raw output prefix, filter rules) and run by a
  generic engine in the core. The capture engine is a generic
  SSE-to-NDJSON writer and must not know what a wiki is.
- Capture filters are drop rules on event fields, defined in
  `config/capture.toml`. Never filter by a list of entities.

## Conventions

- Everything in English: code, commits, docs, DAG ids.
- Python managed with `uv`. Orchestration: Airflow 3.
  Transformation: dbt on DuckDB.
- Architecture decisions go in `docs/adr/`, numbered, written
  before the code they justify.

## Data sources

Each source carries its own licence and attribution requirements.
See `DATA_SOURCES.md` before adding or redistributing anything.