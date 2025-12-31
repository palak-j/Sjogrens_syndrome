# Reproducibility & Experimental Protocol

This document describes how the results in the paper can be reproduced once the full pipeline is implemented.

---

## 1. Corpus Construction

- Query PubMed for Sjögren’s syndrome–related abstracts
- Extract title + abstract text
- Sentence segmentation using biomedical-aware tokenization
- Optional filtering for disease-related keywords

---

## 2. Causal Sentence Filtering

### Procedure
- Run the trained causal sentence classifier
- Retain sentences above a chosen probability threshold

### Rationale
This reduces noise and computational cost for relation extraction while maintaining high recall.

---

## 3. Causal Relation Extraction

### Procedure
- Apply ELECTRA-based token classifier
- Label tokens as C/E/O
- Decode spans into cause/effect phrases

### Error Handling
- Sentences with:
  - overlapping C and E spans
  - missing spans
  - low-confidence predictions  
  may be excluded from downstream analysis

---

## 4. Normalization

### Procedure
- Apply SciSpacy NER
- Normalize cause/effect phrases to overlapping entities
- Track both raw and normalized forms

### Rationale
Normalization improves graph quality and interpretability.

---

## 5. Graph Construction

### Procedure
- Aggregate normalized cause → effect pairs
- Build a directed graph
- Optionally prune edges:
  - low frequency
  - low confidence
- Export graph for visualization (e.g., Gephi)

---

## 6. Evaluation Strategy

### Relation Extraction
- Precision, recall, and F1 on labeled test sets
- Comparison with baseline models where applicable

### Retrieval-Oriented Evaluation
- Evaluate extracted relations as a retrieval task
- Measure how well true causal relations are recovered

### Manual Review
- Random sampling of extracted edges
- Domain expert inspection for correctness

---

## Notes on Determinism
- Fixed random seeds
- Documented thresholds
- Explicit dataset splits
