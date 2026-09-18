# 0001 — Multi-domain by design, single domain at launch

- **Status:** Accepted
- **Date:** 2026-10-05
- **Deciders:** Mara

## Context

Aforo ingests data from sources that behave very differently. The bootstrap
domain is cultural heritage: a real-time stream of attention (Wikimedia
EventStreams) joined against slow, heterogeneous catalogues (Europeana, the
Met, the Rijksmuseum). A second domain — likely public transit — is under
consideration for mid-2027, but that decision has not been made and may not
be made at all.

The obvious move for a project with one domain is to build for that domain
and generalise later. That is also how most single-purpose platforms end up
impossible to extend: domain vocabulary leaks into table names, column
semantics and function signatures, and by the time a second domain arrives
the cost of separating them exceeds the cost of a rewrite.

The opposite failure is just as real: inventing abstractions for a domain
that does not exist yet, and carrying the weight of that speculation for
months without ever collecting on it.

## Decision

The platform is multi-domain **by boundary**, not by abstraction.

Concretely:

1. The core package (`src/aforo/`) never names a domain, a provider or a
   dataset. No `culture`, `museum`, `europeana` or `wikimedia` appears
   anywhere inside it.
2. Dependencies point one way. `domains/` imports from the core; the core
   has no knowledge that `domains/` exists.
3. Ingestion contracts are defined independently of any concrete source. A
   source adapter satisfies a contract; the contract does not describe the
   source.
4. Raw data is partitioned by domain and provider from the first write, and
   is never modified after it lands.
5. Abstractions are extracted from working code in the first domain. They
   are not designed in advance for a hypothetical second one.

Points 1 to 4 are cheap to hold from day one and expensive to retrofit.
Point 5 is what keeps this decision from becoming speculative generality:
the boundary is enforced now, the generalisation is earned later.

## Consequences

**Positive**

- Adding a second domain is an additive change: a new directory under
  `domains/`, new adapters, no edits to the core.
- Storage layout, naming and contracts stay readable to someone who does not
  know the cultural heritage domain.
- The constraint surfaces bad abstractions early. Anything that cannot be
  expressed without naming Europeana is a leak, and the rule makes it visible
  at the moment it is written rather than a year later.

**Negative**

- More structure than a single-domain project strictly needs, and some
  indirection that pays off only if a second domain ever lands.
- Domain-specific optimisations are harder to reach for. Cultural heritage
  identifiers, for example, cannot be assumed by the core even where doing so
  would be convenient.
- The rule needs enforcement. Discipline alone does not survive sixteen
  months of evening work.

**Enforcement**

A test in CI fails if any domain term appears under `src/aforo/`. A rule
that only lives in a document is a rule that stops being true.

## Alternatives considered

**Single-domain build, generalise on demand.** Cheapest today, and the
default. Rejected because the leak is invisible while it happens: by the
time the second domain arrives, domain semantics are embedded in dozens of
small decisions, and the migration competes with the feature that motivated
it.

**One repository per domain, shared library between them.** Clean
separation, but it forces the library to be versioned and released before
there is anything to share, and it splits the operational story across repos
for no gain at this size.

**Plugin architecture with runtime domain discovery.** Solves a problem this
project does not have. Two domains do not justify a plugin system, and the
indirection would cost more to read than it saves to extend.

## Revisit

June 2027, when the second domain is decided either way. If the answer is
no second domain, this ADR is superseded rather than deleted: the reasoning
stays on the record, and the boundary has already paid for itself in the
storage layout.