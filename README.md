# The TomeVault Instruction Corpus

Open, de-identified measurements of the AI instruction files teams commit to
their repositories. `CLAUDE.md`, `AGENTS.md`, `SKILL.md`, `.cursorrules` and a
dozen sibling conventions are now ordinary source-controlled artefacts, and
almost nothing is measured about them.

Published monthly. CC BY 4.0. No file contents, no owner names, no repository
names, no URLs.

## Latest edition

**2026-07** — 229,375 files.

| | |
|---|---|
| Skills / configs | 187,976 / 41,399 |
| Names a model | 4,991 (2.2%) |
| Names a **retired** model | 4,098 (1.79% of the corpus) |
| Has a broken internal reference | 55,327 (24.1%) |

Read `data/2026-07/DATASET-CARD.md` before using any of these numbers. It states
the population, the method and the biases, including the one that matters most:
the share of the whole corpus naming a retired model and the share of
*model-naming files* naming one are very different figures, and only the first
is a statement about the corpus.

## Get it

```bash
git clone https://github.com/tomevault-io/instruction-corpus.git
```

```python
import pandas as pd
df = pd.read_parquet("data/2026-07/instruction-files.parquet")
```

Also on [Hugging Face](https://huggingface.co/datasets/TomeVault/instruction-corpus),
[Kaggle](https://www.kaggle.com/datasets/tomevault/ai-instruction-corpus) and
[Zenodo](https://doi.org/10.5281/zenodo.21724369).

## Cite it

The concept DOI always resolves to the newest edition:

> TomeVault. *The TomeVault Instruction Corpus.* https://doi.org/10.5281/zenodo.21724369

For a figure you want pinned, cite the edition instead:

> TomeVault (2026). *The TomeVault Instruction Corpus*, edition 2026-07.
> https://doi.org/10.5281/zenodo.21724370

Published editions are immutable. Corrections ship as new dated editions, so a
number you quote does not change underneath you.

## What this cannot tell you

A row is a file, not a project and not a team. A popular file forked four
hundred times contributes four hundred rows.

The observation window is ours, not the ecosystem's. `ingested_week` records
when our crawler first saw a file, not when it was written.

Structural checks are not quality judgements. A file can pass every check here
and still give an agent bad instructions.

The full set of limits is in each edition's dataset card.

## Removal

Files belonging to anyone who has asked to be removed are excluded, resolved at
the owner level rather than per file. Email oli@tomevault.io and subsequent
editions will not contain them.

## About

Built and published by [TomeVault](https://tomevault.io). The monthly series
lives at https://tomevault.io/standards/state-reports.
