# Causality Extraction for Sjögren’s Syndrome (Literature Mining)

This repository accompanies the published paper:

[Paper](https://www.cell.com/heliyon/fulltext/S2405-8440(23)06473-3)

**Using transfer learning-based causality extraction to mine latent factors for Sjögren’s syndrome from biomedical literature** (Heliyon, 2023)


---

## What this project does

Given biomedical abstracts (e.g., PubMed), the system:

1. **Finds causal sentences** (causal vs. non-causal classification)
2. **Extracts causal relations** from those sentences by tagging spans as:
   - `C` = Cause phrase
   - `E` = Effect phrase
   - `O` = Other
3. **Normalizes extracted phrases** to biomedical entities using SciSpacy NER overlap logic
4. **Builds a directed causal network** (cause → effect edges) and derives latent factors/insights

<img width="789" height="742" alt="image" src="https://github.com/user-attachments/assets/a033eeda-0d4e-4dac-a3a8-132b1cf9c649" />


---

## Why this matters

Biomedical literature contains many causal statements that are hard to aggregate manually. A causal graph built from extracted relations can reveal:
- common upstream factors (high out-degree causes)
- downstream effects (high in-degree effects)
- clusters/modules of related biomedical concepts
- interpretable “latent factors” useful for hypothesis generation

---

## Expected input/output formats

### Input: abstracts JSONL (`templates/input_format_example.jsonl`)
Each line is one document (e.g., PubMed abstract).

### Output: normalized causal edges CSV (`templates/normalized_edges_example.csv`)
Each row is a directed edge: `cause_entity → effect_entity` with optional confidence.

---

<img width="895" height="451" alt="image" src="https://github.com/user-attachments/assets/e856dd74-6be1-4836-8665-85057eaf03a8" />

---

## More Details here:

- Pipeline & architecture: [Method](docs/method.md)
- Reproducibility checklist: [Reproduction](docs/reproduction.md)

---

## License

- Code: MIT
- Datasets/models: follow original licenses
