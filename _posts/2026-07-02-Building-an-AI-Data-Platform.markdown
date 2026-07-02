---
layout: post
title: "Building an AI Data Platform from Scratch: Architecture Decisions and Lessons Learned"
date: 2026-07-02
description: How I designed a production-grade AI data platform — the Medallion architecture, a multi-schema PostgreSQL design, and the RAW → ETL → Model Zoo flow that ties it together, plus the trade-offs I'd make again and the ones I wouldn't.
img: workflow.jpg # Add image post (optional)
tags: [Data Engineering, Architecture, PostgreSQL, Medallion, MLOps, Platform] # add tag
---

# Building an AI Data Platform from Scratch: Architecture Decisions and Lessons Learned

Most "data platform" write-ups show you the final diagram and skip the part that actually matters — *why* each box is where it is, and what it cost to put it there. This post is the opposite. I want to walk through the real decisions I made building an AI data platform from the ground up: what I chose, what I traded away, and what I'd do differently now.

The goal of the platform was simple to state and hard to build: take messy, heterogeneous source data and turn it into governed, model-ready datasets that data scientists could trust without asking me where a number came from.

## The shape of the problem

Before any architecture, three constraints drove everything:

1. **Reproducibility** — if a model behaves differently next week, I need to be able to point at exactly which data changed.
2. **Separation of concerns** — raw ingestion, business logic, and model consumption have completely different failure modes and should never share a blast radius.
3. **Governance from day one** — sensitivity classification and access logging aren't a phase-two feature. Retrofitting them is painful and usually incomplete.

Those three constraints are what led me to a **Medallion architecture** rather than a single warehouse schema.

## Why Medallion (Bronze → Silver → Gold)

The Medallion pattern organizes data into three quality tiers:

- **Bronze (RAW)** — data lands exactly as it arrived. No cleaning, no joins, no opinions. Just an immutable, timestamped record of what the source actually sent.
- **Silver (Curated)** — cleaned, deduplicated, type-enforced, conformed to shared keys. This is where business logic lives.
- **Gold (Model-ready)** — aggregated, feature-engineered datasets shaped for a specific consumer: a dashboard, a model, a report.

The single most important decision I made was **keeping RAW physically separate from everything downstream**. It's tempting to clean data "on the way in" to save a hop. Don't. The moment you transform on ingestion, you lose the ability to answer "what did the source actually send us?" — and that question comes up constantly when debugging a bad model or a broken pipeline. Immutable RAW is your audit trail, your replay button, and your sanity check all in one.

> **Lesson:** RAW is cheap to store and priceless to keep. Storage is the least expensive thing in the whole system; reprocessability is the most valuable.

## The multi-schema PostgreSQL design

Rather than spread the tiers across separate databases, I modeled them as **separate schemas within a single PostgreSQL instance**:

```
platform_db
├── raw/        -- Bronze: immutable landing tables, one per source
├── curated/    -- Silver: cleaned, conformed, business logic applied
├── model_zoo/  -- Gold: feature tables and model-ready views
└── governance/ -- catalog, sensitivity tags, access logs
```

Why one database, many schemas — instead of many databases?

- **Transactional integrity across tiers.** A single transaction can promote curated data and update the governance catalog atomically. Cross-database transactions in Postgres are awkward at best.
- **One permission surface.** Schema-level `GRANT`s map cleanly onto roles: ingestion writes only to `raw`, transformation jobs read `raw` and write `curated`, data scientists get read-only on `model_zoo` and nothing else.
- **Simpler operations.** One instance to back up, monitor, and tune — with logical isolation where it counts.

The trade-off is real: a single instance is a single scaling ceiling. I accepted that because the workload was analytical-batch, not high-concurrency OLTP, and because schema separation gives a clean migration path — if `raw` outgrows the box, it lifts out into its own instance with minimal code change since it's already an isolated namespace.

## The RAW → ETL → Model Zoo flow

Here's how data actually moves:

**1. Ingestion into RAW.** Each source gets its own landing table in the `raw` schema. Rows are append-only and stamped with an ingestion timestamp and a source batch id. Nothing is overwritten. If a source resends a record, we get a new row, not a mutation.

**2. ETL into Curated.** Transformation jobs read from `raw`, apply cleaning and business rules, and write conformed tables into `curated`. Every curated table carries lineage back to the RAW batch that produced it — so any curated row can be traced to the exact raw payload behind it.

**3. Promotion into the Model Zoo.** The `model_zoo` schema holds the Gold tier: feature tables and model-ready views built on top of curated data. The name is deliberate — it's a *zoo* of consumption-shaped datasets, each one owned by and named for the model or use case it serves. Data scientists shop here. They never touch `raw` or `curated` directly.

The discipline that makes this work: **data only ever flows downhill.** RAW never reads from curated; curated never reads from the model zoo. That one rule eliminates an entire class of circular-dependency and stale-data bugs before they can exist.

## What I'd do again

- **Immutable RAW.** Non-negotiable. It has paid for its storage cost many times over in debugging alone.
- **Schema-per-tier in one Postgres instance.** The right amount of isolation without the operational tax of a fleet of databases.
- **Governance as a first-class schema.** Having the catalog and access logs live *inside* the platform, in the same transactional boundary as the data, meant governance was never out of sync with reality.

## What I'd do differently

- **I'd formalize data contracts at the RAW boundary sooner.** Early on, schema drift in a source silently broke a downstream ETL job. A lightweight contract check on ingestion would have caught it at the door instead of three tiers deep.
- **I'd invest in lineage tooling earlier.** I tracked lineage in table columns, which works but is manual. A dedicated lineage layer would have made the "why did this number change?" question answerable in seconds rather than minutes.

## Closing thought

A data platform isn't a stack of tools — it's a set of promises about where data lives, how it moves, and who can see it. The Medallion tiers, the schema separation, and the strict downhill flow are all just mechanisms for keeping those promises. Get the promises right first, and the tool choices mostly fall out of them.

---

*I'm a Senior Data Engineer building production data platforms across AWS, GCP, and Azure. If you're working on similar problems, I'd genuinely like to hear how you've approached the RAW-vs-curated boundary — [find me on LinkedIn](https://www.linkedin.com/in/ines-gahlouzi/).*
