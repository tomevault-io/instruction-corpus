---
license: cc-by-4.0
language:
  - en
pretty_name: The TomeVault Instruction Corpus (2026-07)
size_categories:
  - 100K<n<1M
tags:
  - ai-agents
  - prompt-engineering
  - software-engineering
  - configuration
  - corpus-analysis
configs:
  - config_name: default
    data_files: instruction-files.parquet
---

# The TomeVault Instruction Corpus

De-identified structural measurements of AI instruction files (CLAUDE.md, AGENTS.md, SKILL.md, .cursorrules and related conventions).

Edition 2026-07. 229,375 files. Schema version 1.0.0. Licensed CC BY 4.0.

## What this is

Teams increasingly commit instructions for AI coding agents directly into their
repositories. `CLAUDE.md`, `AGENTS.md`, `SKILL.md`, `.cursorrules` and a dozen
sibling conventions are now ordinary source-controlled artefacts, and almost
nothing is measured about them.

This dataset is one row per instruction file that our pipeline has fetched and
parsed, carrying that file's structural properties. Size, format, licence,
whether it names a model that no longer exists, and whether a deterministic
ruleset thinks an agent asked to load it would get what the author intended.

It contains no file contents, no owner names, no repository names and no URLs.
Every measurement in it is reproducible from the method described below.

One row per instruction FILE INSTANCE that the pipeline has fetched, converted, and RETAINED THE BODY OF. A row is a file, NOT a repository and NOT an owner: one repository commonly contributes many rows, and a widely-forked file appears once per fork. Any count derived from this table is a count of files unless it explicitly de-duplicates. Two exclusions narrow it further: rows whose deterministic security grade is 'fail', and rows the pipeline tracks without a stored body, which cannot be measured and are therefore absent rather than counted as passing. The retained-body requirement is the largest of the three filters, so this is a measurable subset of what we track and not a census of it.

## Source and updates

This is one edition of a monthly series. Every edition is dated, immutable once
published, and keeps its own citable identity, so a figure you quote does not
change underneath you.

- Series index and past editions: <https://tomevault.io/standards/state-reports>
- Method, column definitions and the checks behind them: <https://tomevault.io/standards/state-reports>
- Published by TomeVault: <https://tomevault.io>
- Questions, corrections, or removal requests: oli@tomevault.io

If you use this dataset in something public we would genuinely like to see it.

## What this cannot tell you

Three limits are worth stating before anyone builds on this.

A row is a file, not a project and not a team. A popular instruction file that
has been forked four hundred times contributes four hundred rows. Any count
here is a count of file instances unless you de-duplicate it yourself, and the
difference between the two is large.

The observation window is ours, not the ecosystem's. The `ingested_week` column
records when our crawler first saw a file, which is not when it was written.
Growth curves drawn from that column describe our crawl, not adoption.

Structural checks are not quality judgements. A file can pass every check here
and still give an agent bad instructions. What the loadability columns measure
is whether the instruction reaches the agent intact, which is a narrower and
more testable question than whether it is any good.

## Columns

| Column | Type | Meaning |
|---|---|---|
| `format` | string | The instruction-file convention this file follows, as detected by the pipeline (claude_md, agents_md, skill_md, cursorrules, cursor_mdc, gemini_md, copilot_instructions, windsurf_rules, other). |
| `format_family` | string | The format grouped to its tool family (claude, agents, cursor, gemini, copilot, windsurf, skill, other). Formats that a single tool reads under more than one filename collapse to one family here. |
| `role` | string | Whether the file is an always-on config, an on-demand skill, or a tome. |
| `source_platform` | string | The platform the file was discovered on. |
| `account_type` | string | Whether the owning account is an Organization or an individual User, as reported by the source platform. Null where the platform did not say. |
| `bytes` | int64 | Size of the file body in bytes. |
| `lines` | int32 | Number of newline-separated lines in the file body. |
| `token_count` | int32 | Approximate token count of the body, as computed by the pipeline at ingestion. Null where it was never computed. |
| `licence_spdx` | string | SPDX identifier of the REPOSITORY's licence as reported by the source platform. Null means the platform reported none, which is not the same as the work being public domain. This population is licence-filtered, so the distribution here is not the ecosystem's: see the licence note in the biases section before deriving any rate from it. |
| `names_a_model` | bool | True when the body names at least one model identifier from the curated tracking list, at any lifecycle status. |
| `model_is_retired` | bool | True when the body names at least one model identifier whose curated status is deprecated, superseded or retired. False when the file names only live models AND when it names no model at all. Read this column together with names_a_model: the share of the whole corpus and the share of model-naming files are very different numbers, and only the first is a statement about the corpus. |
| `retired_model_count` | int32 | How many DISTINCT retired identifiers the body names. |
| `loadability_tier` | string | The verdict of the deterministic loadability ruleset over this file: whether an agent asked to load it would get what the author intended. |
| `loadability_findings` | int32 | Number of distinct loadability checks that fired at least once on this file. Zero means the file passed every applicable check. |
| `has_broken_reference` | bool | True when at least one loadability finding is a reference the file makes to something that will not travel with it, or that is already gone. |
| `security_grade` | string | Grade from the deterministic pattern scanner. Rows graded 'fail' are not in this dataset at all; this column distinguishes the rest. |
| `quality_grade` | string | Grade from the deterministic quality ruleset. Null where never graded. |
| `ingested_week` | string | ISO week (YYYY-Www) in which the pipeline first observed the file. See the ingestion-window caveat in the dataset card. |

## Method

Files are discovered by crawling public repositories, fetched, and classified by
format. Every measurement in this release comes from deterministic rulesets, not
from a language model, so the same input always produces the same row.

Loadability findings come from the same ruleset our product runs, version
`loadability-v1.1`. The `has_broken_reference` column is
true when a file fires either the stale-path check or the reference-portability
check, meaning it points at something that has gone or that will not travel with
the file when it moves.

Retired-model flags come from a hand-curated list of model identifiers and their
lifecycle status, last updated `2026-06-23`. Only
identifiers marked deprecated, superseded or retired are flagged. Matching is
whole-token, so `gpt-4` does not match inside `gpt-4o`.

Licence classification reuses the same policy module that gates our own
ingestion, so the compliance figures here and our enforcement are definitionally
the same thing.

`aggregate.json` in this release carries the corresponding month's headline
metrics computed over the same snapshot.

## Known biases

**Read `model_is_retired` together with `names_a_model`.** Most files name no
model at all, and a file that names no model cannot be stale. The share of the
whole corpus naming a retired model and the share of model-naming files naming a
retired model are very different numbers. Only the first is a statement about
the corpus. Publishing the second as though it were the first overstates the
problem by more than an order of magnitude, and we would rather you did not.

**The curated model list carries more dead identifiers than live ones.** That
shape inflates any conditional staleness rate independently of how careless
anyone has been.

**Discovery is not uniform.** Coverage reflects what a crawler over public
repositories reaches. Private repositories, self-hosted platforms and files
inside archives are absent, and there is no reason to assume the absent
population resembles this one.

**Security-failing files are excluded.** Rows whose deterministic security grade
is `fail` were removed before export. The remaining population is therefore
slightly cleaner than the raw corpus.

**The licence distribution is filtered, and it is not the ecosystem's.** Files
whose repository licence fails our ingestion policy do not reach this population
at all, so permissive licences are heavily over-represented here by construction.
A small share of rows additionally carry no SPDX identifier, because the source
platform reported none. Neither the shape of this column nor the size of its null
slice supports any claim about how much of the wider ecosystem is unlicensed or
copyleft. Do not read a compliance rate out of this dataset.

`ingested_week` is the ISO week in which OUR PIPELINE first observed the file, not the week the file was authored upstream. The series describes the pipeline's observation window and must never be presented as ecosystem growth. Deriving an adoption curve from this column is a misuse of it.

## Removal and consent

This release excludes every file belonging to an owner who has asked to be
removed. Owners are excluded at the OWNER level. The exporter first collects every owner having at least one row with opted_out = 1, then drops all rows belonging to those owners. Row-level filtering would leak the untouched rows of an owner who has already asked to be removed.

Edition 2026-07 excludes 275 files across
16 owners on that basis.

To be removed from future editions, contact oli@tomevault.io. Because the
dataset carries no owner names or repository names, removal takes effect by
dropping the underlying rows, and subsequent editions simply do not contain
them.

## Cite this

Plain text:

> TomeVault (2026). The TomeVault Instruction Corpus, edition
> 2026-07. https://tomevault.io/standards/state-reports

BibTeX:

```bibtex
@dataset{tomevault_instruction_corpus_2026_07,
  title  = {The TomeVault Instruction Corpus},
  author = {TomeVault},
  year   = {2026},
  note   = {Edition 2026-07, schema 1.0.0},
  url    = {https://tomevault.io/standards/state-reports}
}
```

Corrections ship as new dated editions. A published edition is never edited in
place, so a figure you quote does not change underneath you.
