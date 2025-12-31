# Method (Technical Details)

This project implements a **two-stage causal information extraction pipeline** followed by **entity normalization and graph construction**, as proposed in the paper.

The system is designed to minimize error propagation by:
- filtering non-causal sentences early
- using transfer learning across heterogeneous causal datasets
- separating span extraction from entity normalization

---

## Stage 1 — Causal Sentence Classification

### Objective
Identify whether a biomedical sentence expresses an explicit **causal relationship**.

### Input
A single sentence extracted from biomedical abstracts.

### Output
Binary label:
- `1`: causal sentence
- `0`: non-causal sentence  
(optionally with a probability score)

---

### Model Architecture

The classifier is a **BiLSTM-based neural network** augmented with a **Walsh–Hadamard Transform (WHT)**.

Core components:
1. **Sentence embedding layer**
   - Pretrained word embeddings or contextual embeddings
2. **Bidirectional LSTM**
   - Captures forward and backward dependencies
3. **Walsh–Hadamard Transform layer**
   - Applied to hidden representations
   - Improves feature mixing and context propagation
4. **Fully connected classification layer**
   - Sigmoid / softmax output

The WHT allows efficient mixing of contextual information without adding trainable parameters, improving generalization across domains.

---

### Training Strategy

- Trained on **multiple causal sentence datasets**
- Uses **transfer learning** to generalize to biomedical text
- High-recall setting preferred to avoid missing causal sentences
- Threshold selection is tuned to balance recall and precision

---

## Stage 2 — Causal Relation Extraction (Span Identification)

### Objective
Given a causal sentence, extract:
- **Cause phrase**
- **Effect phrase**

This is framed as a **sequence labeling task**.

---

### Label Schema

Each token is labeled as:
- `C` — part of the cause span
- `E` — part of the effect span
- `O` — other / irrelevant

Contiguous `C` tokens form a cause phrase; contiguous `E` tokens form an effect phrase.

---

### Model Architecture

The relation extractor is based on **ELECTRA**, fine-tuned for token classification.

Architecture details:
1. **Pretrained ELECTRA encoder**
   - Provides contextual token representations
2. **Optional BiGRU layer**
   - Further sequence modeling over ELECTRA outputs
3. **Token-level classification head**
   - Outputs probabilities over {C, E, O}

---

### Transfer Learning Setup

- Model is trained on a **combined dataset** composed of:
  - multiple causal relation extraction corpora
  - different domains and annotation styles
- This reduces overfitting to any single dataset
- Enables zero-shot / few-shot transfer to Sjögren’s syndrome literature

---

### Span Decoding

- Greedy decoding over token labels
- Handles multi-token causes/effects
- Sentences with missing or ambiguous spans may be discarded or flagged

---

## Stage 3 — Phrase Normalization via Biomedical NER

### Objective
Normalize extracted cause/effect phrases to standardized biomedical entities for graph construction.

---

### Approach

1. Run **SciSpacy biomedical NER**
   - Disease and chemical entity models
2. For each extracted span:
   - Identify overlapping named entities
   - Select the best-matching entity based on overlap
3. Replace raw text spans with normalized entity mentions

This step reduces:
- lexical variability
- synonym fragmentation
- graph node explosion

---

### Output
Normalized tuples: 
(cause_entity, effect_entity)

with optional metadata:
- entity type (DISEASE, CHEMICAL, etc.)
- confidence score
- source sentence ID

---

## Stage 4 — Causal Graph Construction

### Graph Definition

- **Nodes**: normalized biomedical entities
- **Edges**: directed cause → effect relations
- **Edge weights** (optional):
  - frequency
  - averaged confidence
  - combined score

---

### Graph Analysis

Typical analyses include:
- in-degree / out-degree centrality
- identification of hub causes
- downstream effect aggregation
- community detection for latent factor discovery

The resulting graph enables qualitative and quantitative inspection of disease mechanisms and contributing factors.
