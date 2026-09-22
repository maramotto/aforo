# Data sources

Every source Aforo reads, with how it is accessed and what can and cannot be
done with the data. Read this before adding a source or publishing anything
derived from one.

## Wikimedia EventStreams — `recentchange`

- **What:** every change to every public Wikimedia wiki, as JSON over SSE,
  from `https://stream.wikimedia.org/v2/stream/recentchange`.
- **Access:** public, no key. Requests identify themselves with a
  User-Agent that carries a contact address, as Wikimedia's User-Agent
  policy asks.
- **Capture:** the full stream, stored as received (see
  `config/capture.toml`), under `raw/wikimedia/recentchange/` in object
  storage.
- **Licence and attribution:** to be documented before anything derived
  from this source is published.

### Raw data is private

The stream is a snapshot of each event at the moment it happened. Wikimedia
can later hide part of it (revision deletion or suppression of an edit
summary, a username or content that exposed personal information), but a
copy captured before that keeps it.

For that reason:

- Raw data lives only in private object storage and never in this
  repository (`raw/` is in `.gitignore`).
- Raw data is never republished as-is. What Aforo publishes is aggregates
  and derived tables.
- Anything published at row level (a list of edits, a sample) is checked
  against the current state of the wiki first, so that content Wikimedia
  has since hidden is not republished.
